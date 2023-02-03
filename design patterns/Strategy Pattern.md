# Strategy Design Pattern:

> The strategy design pattern is behavioral design pattern. 

## It is used to switch between family of algorithms. 

1. This pattern contains one abstract strategy interface and many concrete strategy implementations (algorithms) of that interface.

2. The application uses strategy interface only. Depending on some configuration parameter, the concrete strategy will be tagged to interface.


A Simple example is to Encrypt a file. Based off the size we can choose the `STRATEGY`:
	
-- For small files, you can use "in memory" strategy, where the complete file is read and kept in memory ( let's say for files < 1 gb )

-- For large files, you can use another strategy, where parts of the file are read in memory and partial encrypted results are stored in tmp files.
	

```
 File file = getFile();
 Cipher c = CipherFactory.getCipher( file.size() );
 c.performAction();



// implementations:
interface  Cipher  {
     public void performAction();
}

class InMemoryCipherStrategy implements Cipher { 
	 public void performAction() {
	     // load in byte[] ....
	 }
}

class SwaptToDiskCipher implements Cipher { 
	 public void performAction() {
	     // swapt partial results to file.
	 }

}
```

here: `Cipher c = CipherFactory.getCipher( file.size() );` is responsible to return the correct strategy instance for the cipher.
To not confuse strategy with Factory being used we can replace `CipherFactory` with below:

```
Cipher C =null;
if (file.size() <= 2048) {
	C = new InMemoryCipherStrategy();
}else{  
	c= SwaptToDiskCipher ();
}

```
---
A more complex example would be Paying with Different Gateway at check-out. Similar example would be to choose/create different types of Coffee or Burger. Where we choose different `Algorithm` (example: if a user wants a Cheese Burger or a Chicken Burger, The Chef can just replace the patty and keep the rest as is)

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
