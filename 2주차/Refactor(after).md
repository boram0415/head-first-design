```
// 주제(Subject) 인터페이스
interface OrderSubject {
    void addObserver(OrderObserver observer);
    void removeObserver(OrderObserver observer);
    void notifyObservers(String message);
}

// 옵저버(Observer) 인터페이스
interface OrderObserver {
    void update(String message);
}

// 구체적인 주제(Subject) 구현체
class OrderService implements OrderSubject {
    private List<OrderObserver> observers = new ArrayList<>();

    @Override
    public void addObserver(OrderObserver observer) {
        observers.add(observer);
    }

    @Override
    public void removeObserver(OrderObserver observer) {
        observers.remove(observer);
    }

    @Override
    public void notifyObservers(String message) {
        for (OrderObserver observer : observers) {
            observer.update(message);
        }
    }

    public void completeOrder() {
        System.out.println("Order completed!");
        notifyObservers("Your order has been completed!");
    }
}

// 구체적인 옵저버(Observer) 구현체: 이메일 알림
class EmailNotification implements OrderObserver {
    private String email;

    public EmailNotification(String email) {
        this.email = email;
    }

    @Override
    public void update(String message) {
        System.out.println("Sending email to " + email + ": " + message);
    }
}

// 메인 클래스
public class Main {
    public static void main(String[] args) {
        OrderService orderService = new OrderService();

        // 옵저버 등록
        orderService.addObserver(new EmailNotification("user@example.com"));

        // 주문 완료
        orderService.completeOrder();
    }
}
```

```
// 컴포넌트 인터페이스
interface DataProcessor {
    String process(String data);
}

// 구체적인 컴포넌트: 기본 데이터 처리
class PlainDataProcessor implements DataProcessor {
    @Override
    public String process(String data) {
        return data;
    }
}

// 데코레이터 추상 클래스
abstract class DataProcessorDecorator implements DataProcessor {
    protected DataProcessor dataProcessor;

    public DataProcessorDecorator(DataProcessor dataProcessor) {
        this.dataProcessor = dataProcessor;
    }
}

// 구체적인 데코레이터: 암호화
class EncryptionDecorator extends DataProcessorDecorator {
    public EncryptionDecorator(DataProcessor dataProcessor) {
        super(dataProcessor);
    }

    @Override
    public String process(String data) {
        String encryptedData = encrypt(data);
        return dataProcessor.process(encryptedData);
    }

    private String encrypt(String data) {
        return new StringBuilder(data).reverse().toString();
    }
}

// 구체적인 데코레이터: 압축
class CompressionDecorator extends DataProcessorDecorator {
    public CompressionDecorator(DataProcessor dataProcessor) {
        super(dataProcessor);
    }

    @Override
    public String process(String data) {
        String compressedData = compress(data);
        return dataProcessor.process(compressedData);
    }

    private String compress(String data) {
        return data.length() + ":" + data;
    }
}

// 파일 저장 서비스
class FileService {
    public void save(String filename, String data, DataProcessor processor) {
        String processedData = processor.process(data);
        System.out.println("Saving data to " + filename + ": " + processedData);
    }
}

// 메인 클래스
public class Main {
    public static void main(String[] args) {
        FileService fileService = new FileService();

        // 기본 데이터 처리
        DataProcessor processor = new PlainDataProcessor();

        // 암호화 + 압축 데코레이터 추가
        processor = new CompressionDecorator(new EncryptionDecorator(processor));

        // 파일 저장
        fileService.save("output.txt", "Hello, World!", processor);
    }
}
```
