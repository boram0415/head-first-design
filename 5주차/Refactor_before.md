### 과제 수행 방식

- 하기 코드를 보고 지금까지 학습한 패턴 중 1개 이상의 패턴을 적용해 자유롭게 리팩토링 해 주시고, 왜 그렇게 했는지 이유도 같이 작성해 주시면 됩니다.

- 풀이하신 과제는 Refactor_after_"본인이름".md 파일에 작성해 올려주시면 됩니다.

- 방향을 잡기가 어렵다고 느끼신다면 Refactor_hint.md 파일을 참고하시면 도움이 됩니다.

<br>

---

<br>

```java
// 레거시 외부 결제 시스템 (변경 불가능한 라이브러리)
class OldPayPalProcessor {
    public void initiatePayment(double dollars, String merchantCode) {
        System.out.println("Processing $" + dollars + " via PayPal to " + merchantCode);
    }
    
    public boolean checkStatus(long transactionId) {
        // 실제로는 REST API 호출
        return new Random().nextBoolean();
    }
}

class NewStripeGateway {
    public PaymentResult chargeInKRW(int wonAmount, MerchantAccount account) {
        System.out.println("Charging ₩" + wonAmount + " to " + account.getAccountId());
        return new PaymentResult(new Random().nextLong(), true);
    }
}

// 서비스 내부 도메인 모델
record PaymentRequest(int amountKRW, String currencyType, Merchant merchant) {}
record PaymentResult(long transactionId, boolean isSuccess) {}
record Merchant(String id, String name, String paymentType) {}

// 문제가 되는 결제 처리기
class PaymentHandler {
    private OldPayPalProcessor payPal = new OldPayPalProcessor();
    private NewStripeGateway stripe = new NewStripeGateway();
    
    // 결제 방식별 분기 처리
    public PaymentResult processPayment(PaymentRequest request) {
        if(request.merchant().paymentType().equals("PAYPAL")) {
            double usdAmount = convertToUSD(request.amountKRW());
            payPal.initiatePayment(usdAmount, "OUR_MERCHANT_CODE");
            return new PaymentResult(System.currentTimeMillis(), true);
        }
        else if(request.merchant().paymentType().equals("STRIPE")) {
            return stripe.chargeInKRW(
                request.amountKRW(), 
                resolveMerchantAccount(request.merchant())
            );
        }
        else if(request.merchant().paymentType().equals("KAKAOPAY")) {
            // 신규 추가된 결제 방식 (기존 구조에 무리하게 추가됨)
            System.out.println("카카오페이 직접 호출 로직...");
            return new PaymentResult(0L, false);
        }
        throw new UnsupportedOperationException("지원하지 않는 결제 방식");
    }
    
    private double convertToUSD(int won) { return won / 1200.0; }
    private MerchantAccount resolveMerchantAccount(Merchant m) { /* 복잡한 계정 조회 로직 */ }
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
```