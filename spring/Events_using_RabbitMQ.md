
# 🛠️ Event-Driven Saga with RabbitMQ and Spring Boot

This project demonstrates how to coordinate distributed transactions using the **Saga Pattern** with **RabbitMQ** and **Spring Boot**.

It simulates a flow:

1. A user places an order → `OrderService`
2. Payment is processed → `PaymentService`
3. Shipment is attempted → `ShippingService`
4. If shipment fails, compensating actions are triggered to refund and cancel the order → `CompensationService`

---

## 🐇 Run RabbitMQ Locally

```bash
docker run -d --hostname my-rabbit --name some-rabbit \
  -e RABBITMQ_DEFAULT_USER=user \
  -e RABBITMQ_DEFAULT_PASS=password \
  -p 15672:15672 -p 5672:5672 \
  rabbitmq:3-management
```

> 📍 Access RabbitMQ UI at: [http://localhost:15672](http://localhost:15672)  
> Login with: `user / password`

---

## 🔁 Flow Overview

```
OrderController → OrderService 
    └──> order.created (event)

PaymentService listens on 'orders' queue
    └──> payment.success (event)

ShippingService listens on 'payments' queue
    └──> shipping.failed (event)

CompensationService listens on 'shipment' queue
    └──> Refunds payment + Cancels order
```

---

## 📦 Order Controller

A simple HTTP GET endpoint to simulate order placement:

```java
@Autowired
OrderService service;

@GetMapping("/order/{id}")
public void placeOrder(@PathVariable String id) {
    service.createOrder(Long.parseLong(id));
}
```

---

## 📦 OrderService

```java
@Service
public class OrderService {
    @Autowired
    private RabbitTemplate rabbitTemplate;

    public void createOrder(Long orderId) {
        System.out.println("📦 Order created: " + orderId);
        rabbitTemplate.convertAndSend("exchange", "order.created", orderId);
    }

    public void cancelOrder(Long orderId) {
        System.out.println("🛑 Order cancelled: " + orderId);
    }
}
```

---

## 💳 PaymentService

```java
@Service
public class PaymentService {
    @Autowired
    private RabbitTemplate rabbitTemplate;

    @RabbitListener(queues = "orders")
    public void handleOrderCreated(Long orderId) {
        System.out.println("💰 Processing payment for order: " + orderId);
        rabbitTemplate.convertAndSend("exchange", "payment.success", orderId);
    }

    public void refundPayment(Long orderId) {
        System.out.println("🔄 Refunding payment for order: " + orderId);
    }
}
```

---

## 🚚 ShippingService (Fails to Ship)

```java
@Service
public class ShippingService {
    @Autowired
    private RabbitTemplate rabbitTemplate;

    @RabbitListener(queues = "payments")
    public void handlePaymentSuccess(Long orderId) {
        System.out.println("🚚 Trying to schedule shipping for order: " + orderId);

        boolean shippingOk = false; // force failure for demo

        if (!shippingOk) {
            System.out.println("❌ Shipping failed for order: " + orderId);
            rabbitTemplate.convertAndSend("exchange", "shipping.failed", orderId);
        } else {
            System.out.println("✅ Shipping scheduled");
        }
    }
}
```

---

## 🛡️ CompensationService

```java
@Service
public class CompensationService {
    @Autowired
    private PaymentService paymentService;

    @Autowired
    private OrderService orderService;

    @RabbitListener(queues = "shipment")
    public void handleShippingFailure(Long orderId) {
        System.out.println("⚠️ Handling failure: Shipping failed for order " + orderId);

        paymentService.refundPayment(orderId);
        orderService.cancelOrder(orderId);
    }
}
```

---

## ✅ Sample Output

```
📦 Order created: 1001
💰 Processing payment for order: 1001
🚚 Trying to schedule shipping for order: 1001
❌ Shipping failed for order: 1001
⚠️ Handling failure: Shipping failed for order 1001
🔄 Refunding payment for order: 1001
🛑 Order cancelled: 1001
```

---

## 📌 Notes

- This is a **simple monolithic simulation**. In real microservices:
  - Services would be separate apps
  - Events would be JSON objects with metadata
  - Use proper message serialization (e.g., Jackson, Avro)
  - Add retry logic, dead-letter queues (DLQ), observability, and tracing
