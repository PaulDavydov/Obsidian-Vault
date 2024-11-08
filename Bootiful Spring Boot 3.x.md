
Java records are a new feature in Java 21, supporting data oriented programming.
- java team allows for sealed types, records, pattern matching, smart switch expressions
- One way of preventing bad actors from coming into your code base and implementing code that can bypass security checks and cost the company a lot of money, is to use sealed types
	- You tell compiler that there are only certain implementations allowed in the heirarchy: finals, records or sealed again
- Other languages use tuples
	- essentially a variable that can store multiple values in itself
	- Java 21 has its own version of tuples, it is called a record and it has names in its


```java
class Loans {  
    String displayMessageFor( Loan loan) {  
        return switch (loan) {  
            case UnsecuredLoan (var interest)  -> //smart switches example
	            "ouch that " + interest + "% is going to hurt";  
            case SecuredLoan sl -> 
	            "hey, you get a loan";  
        };  
          
        // Smart switches essentially replaces the code below, smart switches example is above.  
//        if(loan instanceof SecuredLoan sl) {  
//            message = "hey, you get a loan";  
//        }  
//        if (loan instanceof UnsecuredLoan (var interest)) {  
//            message = "ouch that " + interest + "% is going to hurt";  
//        }  
    }  
}  
  
sealed interface Loan permits SecuredLoan, UnsecuredLoan{}  
  
//class NoOp implements Loan {  
//        void update(@RequestBody Order order) {  
//        var updated = this.repository.save(order);  
//        System.out.println("saved [" + saved + "]");  
//    }  
//}  
final class SecuredLoan implements Loan {}  
record UnsecuredLoan (float interest) implements Loan {}
```


export JAVA_HOME=/Library/Java/JavaVirtualMachines/graalvm-jdk-21.0.3+7.1/Contents/Home

export PATH=/Library/Java/JavaVirtualMachines/graalvm-jdk-21.0.3+7.1/Contents/Home/bin:$PATH
