
```python
class RetrieverQ(object):
    def __init__(self, store):
        self.store = store

    def retrieve_recent(self, customer_id, curr_time):
        result = []
        for index in range(0, 5):
            val = self.store.retrieve(customer_id, curr_time - 5 + index)
            if val is not None:
                result.append(val)
        return result
```

- This function retrieves the most recent 5 minutes of data (excluding the current minute) for a customer
	- it loops over the time range of curr_time - 5 to curr_time - 1
	- Calls `self.store.retrieve(customer_id, minute)` for each of those 5 minutes
	- appends the non-None results to result
	- returns the list fo retrieved values
- Improvements I would make
	- Change the range function to be more easily readable
		- `for minute in range(curr_time - 5, curr_time)`
	- Maybe check that there actually exists up that amount of recent times
	- Check to see if customer_id is actually valid
	- Maybe pass in recent amount as a parameter to the funciton
```python
class RetrieverQ(object):
    def __init__(self, store):
        self.store = store

    def retrieve_recent(self, customer_id, curr_time, window=5):
        result = []
        for minute in range(curr_time - window, curr_time):
            val = self.store.retrieve(customer_id, minute)
            if val is not None:
                result.append(val)
        return result
```


```python
def aggregate(abc):
    return len(abc)  # Counts the number of items in a list

def join_data_left function(currentTime):
    clk = RetrieverQ(ClickStore)
    tsc = RetrieverQ(TransactionStore)
    result = []
    for id in clk.id.items():  # Iterate over customer IDs
        clk_data = clk.retrieve_recent(id, currentTime)  # recent clickstream
        tsc_data = tsc.retrieve_recent(id, currentTime)  # recent transactions
        result.append((id, aggregate(clk_data), aggregate(tsc_data)))  # counts
    return result
```

- For each Cusomter ID in ClickStore
	- Fetch recent clickstream data
	- Fetch recent transaction data
	- aggregate each by counting how many records were returned
	- append a tuple(customer id, clickcount, transaction count)
- I would assume, the information that is being returned is a list containing the customer id, alongside the amount of clicks they have, and the amount of transactions they have
```
[
(1, 1, 0),
(2,1,1)
]
```

Issue might be with join_data_left function, where it should be join_data_left_function