# 리팩터링한 코드

```java

// 공통 인터페이스
interface PaymentGateway {
    PaymentResult charge(PaymentRequest request);
}

// PayPal 어댑터
class PayPalAdapter implements PaymentGateway {
    private OldPayPalProcessor payPal = new OldPayPalProcessor();

    @Override
    public PaymentResult charge(PaymentRequest request) {
        double usdAmount = convertToUSD(request.amountKRW());
        payPal.initiatePayment(usdAmount, "OUR_MERCHANT_CODE");
        // 실제 구현에서는 결제 결과 확인이 필요함
        return new PaymentResult(System.currentTimeMillis(), true);
    }

    private double convertToUSD(int won) {
        return won / 1200.0;
    }
}

// Stripe 어댑터
class StripeAdapter implements PaymentGateway {
    private NewStripeGateway stripe = new NewStripeGateway();

    @Override
    public PaymentResult charge(PaymentRequest request) {
        MerchantAccount account = resolveMerchantAccount(request.merchant());
        return stripe.chargeInKRW(request.amountKRW(), account);
    }

    private MerchantAccount resolveMerchantAccount(Merchant m) {
        return new MerchantAccount(m.id(), m.name()); // 예시용 간단 처리
    }
}

// KakaoPay 어댑터
class KakaoPayAdapter implements PaymentGateway {
    @Override
    public PaymentResult charge(PaymentRequest request) {
        System.out.println("카카오페이 직접 호출 로직...");
        return new PaymentResult(0L, false);
    }
}

// 결제 게이트웨이 팩토리 (Factory Pattern)
class PaymentGatewayFactory {
    public static PaymentGateway getGateway(String type) {
        return switch (type.toUpperCase()) {
            case "PAYPAL" -> new PayPalAdapter();
            case "STRIPE" -> new StripeAdapter();
            case "KAKAOPAY" -> new KakaoPayAdapter();
            default -> throw new UnsupportedOperationException("지원하지 않는 결제 방식: " + type);
        };
    }
}

// 리팩토링된 결제 핸들러
class PaymentHandler {
    public PaymentResult processPayment(PaymentRequest request) {
        PaymentGateway gateway = PaymentGatewayFactory.getGateway(request.merchant().paymentType());
        return gateway.charge(request);
    }
}

// 실행 예제
public class PaymentService {
    public static void main(String[] args) {
        PaymentHandler handler = new PaymentHandler();

        PaymentRequest paypalReq = new PaymentRequest(
            150000, "USD", new Merchant("1", "PayPal Store", "PAYPAL")
        );
        PaymentRequest stripeReq = new PaymentRequest(
            99000, "KRW", new Merchant("2", "Stripe Shop", "STRIPE")
        );

        handler.processPayment(paypalReq);
        handler.processPayment(stripeReq);
    }
}

// 기존 클래스들 (수정 없음)
class OldPayPalProcessor {
    public void initiatePayment(double dollars, String merchantCode) {
        System.out.println("Processing $" + dollars + " via PayPal to " + merchantCode);
    }

    public boolean checkStatus(long transactionId) {
        return new Random().nextBoolean();
    }
}

class NewStripeGateway {
    public PaymentResult chargeInKRW(int wonAmount, MerchantAccount account) {
        System.out.println("Charging ₩" + wonAmount + " to " + account.getAccountId());
        return new PaymentResult(new Random().nextLong(), true);
    }
}

record PaymentRequest(int amountKRW, String currencyType, Merchant merchant) {}
record PaymentResult(long transactionId, boolean isSuccess) {}
record Merchant(String id, String name, String paymentType) {}

class MerchantAccount {
    private final String accountId;
    private final String name;

    public MerchantAccount(String accountId, String name) {
        this.accountId = accountId;
        this.name = name;
    }

    public String getAccountId() {
        return accountId;
    }

    public String getName() {
        return name;
    }
}

```
