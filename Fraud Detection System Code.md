Object oriented approach, similar to Design Simple Bank Leetcode Question. I had to perform

fraud detection by taking in 1 set of data, calculating the previous list of all means, and

comparing the current transaction to see if it’s greater than 2 times the previous average means.

This question kept building in complexity, so first I had to parse the data into a systematic data

structure, and then I had to calculate the list of previous averages. And then I had to use this

average value and see if there was a fraud. The data looked like this:

(transaction ID, transaction type, transaction amount)

E.g.

(1323232, “CREDIT”, 34.0)

(5465676, “DEBIT”, 78.0)

(2186434, “CREDIT, 696.0) ← fraud

(transaction ID, transaction timestamp)

(1323232, 67567567)

(5465676, 67567997)

(2186434, 67568607)


```java
import java.util.*;

class Transaction {
	private final int id;
	private final String type;
	private final double amount;

	public Transaction(int id, String type, double amount) {
		this.id = id;
		this.type = type; // use .toUpperCase() for normalizing
		this.amount = amount;
	}

	public int getId() {
		return this.id;
	}

	public String getType() {
		return this.type;
	}

	public double getAmount() {
		return this.amount;
	}
}

class TransactionProcessor {
	private final List<Transaction> transactionHist = new ArrayList<>();
	private double totalAmount = 0.0;

	public boolean processTransaction(Transaction t) {
		boolean isFraud = false;
		
		if(!transactionHist.isEmpty()) {
			double avg = this.totalAmount / transactionHist.size();
			if(t.getAmount() > 2 * avg) isFraud = true;
		}

		transactionHist.add(tx);
		this.totalAmont += t.getAmount();

		return isFraud;
	}

	public List<Transaction> getTransactionHist() {
		return this.transactionHist;
	}
}
```