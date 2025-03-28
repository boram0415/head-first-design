리팩토링 방향

- 전략패턴을 적용해 OCP 원칙을 위배하지 않게 구현
  <br>

---

<br>
```java
public interface PaymentStrategy {
    PaymentResult processPayment(PaymentRequest request);
}
```

```java
public class PayPalPaymentStrategy implements PaymentStrategy {
    private OldPayPalProcessor payPalProcessor = new OldPayPalProcessor();

    @Override
    public PaymentResult processPayment(PaymentRequest request) {
        double usdAmount = convertToUSD(request.amountKRW());
        payPalProcessor.initiatePayment(usdAmount, "OUR_MERCHANT_CODE");
        return new PaymentResult(System.currentTimeMillis(), true);
    }

    private double convertToUSD(int won) {
        return won / 1200.0;
    }
}

public class StripePaymentStrategy implements PaymentStrategy {
    private NewStripeGateway stripeGateway = new NewStripeGateway();

    @Override
    public PaymentResult processPayment(PaymentRequest request) {
        return stripeGateway.chargeInKRW(
            request.amountKRW(),
            resolveMerchantAccount(request.merchant())
        );
    }

    private MerchantAccount resolveMerchantAccount(Merchant merchant) {
        // 복잡한 계정 조회 로직
        return new MerchantAccount(merchant.id());
    }
}

public class KakaoPayPaymentStrategy implements PaymentStrategy {
    @Override
    public PaymentResult processPayment(PaymentRequest request) {
        System.out.println("카카오페이 직접 호출 로직...");
        return new PaymentResult(0L, false);
    }
}
```

```java
public class PaymentHandler {
    private Map<String, PaymentStrategy> paymentStrategies = new HashMap<>();

    public PaymentHandler() {
        paymentStrategies.put("PAYPAL", new PayPalPaymentStrategy());
        paymentStrategies.put("STRIPE", new StripePaymentStrategy());
        paymentStrategies.put("KAKAOPAY", new KakaoPayPaymentStrategy());
    }

    public PaymentResult processPayment(PaymentRequest request) {
        PaymentStrategy strategy = paymentStrategies.get(request.merchant().paymentType());
        if (strategy == null) {
            throw new UnsupportedOperationException("지원하지 않는 결제 방식");
        }
        return strategy.processPayment(request);
    }
}
```

```java
public class PaymentService {
    public static void main(String[] args) {
        PaymentHandler handler = new PaymentHandler();

        PaymentRequest paypalRequest = new PaymentRequest(
            150000, "USD", new Merchant("1", "PayPal Store", "PAYPAL")
        );
        PaymentRequest stripeRequest = new PaymentRequest(
            99000, "KRW", new Merchant("2", "Stripe Shop", "STRIPE")
        );
        PaymentResult paypalResult = handler.processPayment(paypalRequest);
        System.out.println("PayPal 결제 성공 여부: " + paypalResult.isSuccess());

        PaymentResult stripeResult = handler.processPayment(stripeRequest);
        System.out.println("Stripe 결제 성공 여부: " + stripeResult.isSuccess());
    }
}
```
