# Observer Pattern:

Think Twitter. When you say you want to follow someone, Twitter adds you to their follower list. When they sent a new tweet in, you see it in your input. In that case, your Twitter account is the Observer and the person you're following is the Observable.


In `Spring Flux` we have Publisher `FLUX` and subscriber.

example:

   ```java
  // 1. Publisher (Flux acts as the Subject/Publisher)
      Flux<String> fluxStream = Flux.just("Apple", "Banana", "Cherry");

      // 2. Subscriber (Observer reacting to items)
      fluxStream.subscribe(
          item -> System.out.println("Received: " + item),      // onNext
          error -> System.err.println("Error: " + error),       // onError
          () -> System.out.println("Completed stream!")         // onComplete
      );
   ```
    

One of the example would be:
Whenever the news publishes something the observer observes. 

  ```java
import java.util.ArrayList;
    import java.util.List;

    interface Observer {
        void update(String message);
    }

    class NewsPublisher {
        private List<Observer> subscribers = new ArrayList<>();
        
        public void subscribe(Observer observer) {
            subscribers.add(observer);
        }
        
        public void unsubscribe(Observer observer) {
            subscribers.remove(observer);
        }
        
        public void publish(String news) {
            for (Observer subscriber : subscribers) {
                subscriber.update(news);
            }
        }
    }

    class EmailSubscriber implements Observer {
        private String email;
        
        public EmailSubscriber(String email) {
            this.email = email;
        }
        
        public void update(String message) {
            System.out.println("Email to " + email + ": " + message);
        }
    }

    class SMSSubcriber implements Observer{
        private String smsNumber;
        
        public SMSSubcriber(String smsNumber) {
            this.smsNumber = smsNumber;
        }
        
        public void update(String message) {
            System.out.println("sms to " + smsNumber + ": " + message);
        }
    }

    public class Solution{
        public static void main(String[] args) {
            NewsPublisher publisher = new NewsPublisher();
            publisher.subscribe(new EmailSubscriber("alice@mail.com"));
            publisher.subscribe(new SMSSubcriber("bob@mail.com"));

            publisher.publish("Breaking news!");
        }
    // Output:
    // Email to alice@mail.com: Breaking news!
    // Email to bob@mail.com: Breaking news!
    }
    
  ```
