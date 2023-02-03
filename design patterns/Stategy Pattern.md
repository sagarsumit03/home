# Stretegy Design Pattern:

> The strategy design pattern is operational design pattern.

```
public interface PaymentStrategy {

	public void pay(int amount);
}
```

Now we will have to create concrete implementation of algorithms for payment using credit/debit card or through paypal. CreditCardStrategy.java

```
public class CreditCardStrategy implements PaymentStrategy {

	private String name;
	private String cardNumber;
	private String cvv;
	private String dateOfExpiry;
	
	public CreditCardStrategy(String nm, String ccNum, String cvv, String expiryDate){
		this.name=nm;
		this.cardNumber=ccNum;
		this.cvv=cvv;
		this.dateOfExpiry=expiryDate;
	}
	@Override
	public void pay(int amount) {
		System.out.println(amount +" paid with credit/debit card");
	}

}
```
```
public class PaypalStrategy implements PaymentStrategy {

	private String emailId;
	private String password;
	
	public PaypalStrategy(String email, String pwd){
		this.emailId=email;
		this.password=pwd;
	}
	
	@Override
	public void pay(int amount) {
		System.out.println(amount + " paid using Paypal.");
	}

}
```

Now our strategy pattern example algorithms are ready. We can implement Shopping Cart and payment method will require input as Payment strategy. Item.java

```
public class Item {

	private String upcCode;
	private int price;
	
	public Item(String upc, int cost){
		this.upcCode=upc;
		this.price=cost;
	}

	public String getUpcCode() {
		return upcCode;
	}

	public int getPrice() {
		return price;
	}
	
}
```

```
public class ShoppingCart {

	//List of items
	List<Item> items;
	
	public ShoppingCart(){
		this.items=new ArrayList<Item>();
	}
	
	public void addItem(Item item){
		this.items.add(item);
	}
	
	public void removeItem(Item item){
		this.items.remove(item);
	}
	
	public int calculateTotal(){
		int sum = 0;
		for(Item item : items){
			sum += item.getPrice();
		}
		return sum;
	}
	
	public void pay(PaymentStrategy paymentMethod){
		int amount = calculateTotal();
		paymentMethod.pay(amount);
	}
}
```

Notice that payment method of shopping cart requires payment algorithm as argument and doesn’t store it anywhere as instance variable. Let’s test our strategy pattern example setup with a simple program. ShoppingCartTest.java

```
public class Driver {

	public static void main(String[] args) {
		ShoppingCart cart = new ShoppingCart();
		
		Item item1 = new Item("1234",10);
		Item item2 = new Item("5678",40);
		
		cart.addItem(item1);
		cart.addItem(item2);
		
		//pay by paypal
		cart.pay(new PaypalStrategy("myemail@example.com", "mypwd"));
		
		//pay by credit card
		cart.pay(new CreditCardStrategy("Pankaj Kumar", "1234567890123456", "786", "12/15"));
	}

}
```
Output of above program is:
```
50 paid using Paypal.
50 paid with credit/debit card
```

>A factory pattern is used to create objects of a specific type. A strategy pattern is use to perform an operation (or set of operations) in a particular manner. In the classic example, a factory might create different types of Animals: Dog, Cat, Tiger, while a strategy pattern would perform particular actions, for example, Move; using Run, Walk, or Lope strategies.
