
```java
import java.util.Map;
import java.util.concurrent.ConcurrentHashMap;
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

class Bank {
	// use final so that we ensure the behavior of the class cannot be chagnged
	// also so child classes are not created from it
	// basically for secuirty, consistency, and to make it immutable
    private static final class Account {
        private Long balance;
        private final Lock lock = new ReentrantLock(true);
        private int activityCount;

        public Account(Long balance) {
            this.balance = balance;
            this.activityCount = 0;
        }

        public boolean deposit(long amount) {
            lock.lock();
            try {
                balance += amount;
            } finally {
                lock.unlock();
            }

            return true;
        }

        public boolean withdraw(long amount) {
            lock.lock();
            try {
                if (balance < amount) return false;
                balance -= amount;
            } finally {
                lock.unlock();
            }

            return true;
        }

		public void incrementActivity() {
	        lock.lock();
	        try {
	            activityCount++;
	        } finally {
	            lock.unlock();
	        }
	    }

	    public int getActivityCount() {
	        lock.lock();
	        try {
	            return activityCount;
	        } finally {
	            lock.unlock();
	        }
	    }

		public int getBalance() {
			return this.balance;
		}
    }

    private final Map<Integer, Account> accounts = new ConcurrentHashMap<>();

    public Bank(long[] balance) {
        for (int i = 0; i < balance.length; i++) {
            accounts.putIfAbsent(i, new Account(balance[i]));
        }
    }

    public boolean transfer(int account1, int account2, long money) {
        if (!validateAccount(account1) || !(validateAccount(account2)) || money < 0) return false;

        var acc1 = getAccount(account1);
        var acc2 = getAccount(account2);

		    // Lock ordering to prevent deadlocks
		Account firstLock, secondLock;
		if (account1 < account2) {
			firstLock = acc1;
			secondLock = acc2;
		} else {
			firstLock = acc2;
			secondLock = acc1;
		}

        acc1.lock.lock();
        try {
            acc2.lock.lock();
            try {
                if (acc1.withdraw(money)) {
                    acc2.deposit(money);
                } else {
                    return false;
                }
            } finally {
                acc2.lock.unlock();
            }
        } finally {
            acc1.lock.unlock();
        }

        return acc1.getBalance();
    }

    public boolean deposit(int account, long money) {
        if (!validateAccount(account)) return false;
        return getAccount(account).deposit(money);
    }


    public boolean withdraw(int account, long money) {
        if (!validateAccount(account)) return false;
        return getAccount(account).withdraw(money);
    }

    private boolean validateAccount(int account) {
        return account > 0 && account <= accounts.size();
    }

    private Account getAccount(int account) {
        var acc = accounts.get(account - 1);
        if (acc == null)
            throw new IllegalStateException("invalid account definition " + account + " " + accounts.size());

        return acc;
    }

	public List<Integer> getMostActiveAccounts(int n) {
		List<Integer> topNAccounts = accounts.keySet();
		Collections.sort(topNAccounts, (a, b) -> {
			if(accounts.get(a).getActivityCount() == accounts.get(b).getActivityCount()) return Integer.compare(a, b);
			else Integer.compare(accounts.get(b).getActivityCount(), accounts.get(a).getActivityCount())
		});

		while(topNAccounts.size() != n) {
			topNAccounts.remove(topNAccounts.size() - 1);
		}
		return topNAccounts;
	
//	    return accounts.entrySet().stream()
//	        .sorted((e1, e2) -> Integer.compare(e2.getValue().getActivityCount(), e1.getValue().getActivityCount()))
//	        .limit(n)
//	        .map(e -> e.getKey() + 1) // convert back to 1-based account numbers
//	        .collect(Collectors.toList());
	}
}

/**
 * Your Bank object will be instantiated and called as such:
 * Bank obj = new Bank(balance);
 * boolean param_1 = obj.transfer(account1,account2,money);
 * boolean param_2 = obj.deposit(account,money);
 * boolean param_3 = obj.withdraw(account,money);
 */
```


### Pros:

- Thread-safe with `ReentrantLock`.
    
- Uses `ConcurrentHashMap` for safe account access.
	- provides guarantee on the thread safety of the operations within the HashMap itself
	- 
    
- Basic banking operations are implemented cleanly.


- to avoid dead locking, when TRANSFERRING grab the accounts by lowest ID first
	- This way, instead of grabbing random accounts, we grab accounts based on lwoest first at all times, and wait for that lowest one to be released before we proceed with our operation
- One issue can be when creating accounts. What can happen is multiple threads check to see if the account exists, and they all see that the account is nonexistent, so they put/create their own versions of the account into the map