```
class OrderService {
    public void completeOrder(String userEmail) {
        System.out.println("Order completed!");
        sendEmail(userEmail, "Your order has been completed!");
    }

    private void sendEmail(String email, String message) {
        System.out.println("Sending email to " + email + ": " + message);
    }
}

public class Main {
    public static void main(String[] args) {
        OrderService orderService = new OrderService();
        orderService.completeOrder("user@example.com");
    }
}
```

```
class FileService {
    public void save(String filename, String data, boolean encrypt, boolean compress) {
        if (encrypt) {
            data = encrypt(data);
        }
        if (compress) {
            data = compress(data);
        }
        System.out.println("Saving data to " + filename + ": " + data);
    }

    private String encrypt(String data) {
        return new StringBuilder(data).reverse().toString();
    }

    private String compress(String data) {
        return data.length() + ":" + data;
    }
}

public class Main {
    public static void main(String[] args) {
        FileService fileService = new FileService();
        fileService.save("output.txt", "Hello, World!", true, true);
    }
}
```
