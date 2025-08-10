> The store’s backend returns product prices in cents. How would you calculate the price in dollars for display to users?

If i dont have access to backend i would write an api service on front that divides the prices' response by 100

> You receive a CSV file with customer emails, but some are duplicated. Write pseudocode to remove duplicates and output a clean list.

```py
def remove_duplicates_email(input_file, output_file):
    unique_emails = set()

    with open(input_file, 'r') as infile:
        reader = csv.reader(infile)
        header = next(header)

        for row in reader:
            email = row[0]
            unique_emails.add(email.strip().lower())
    with open(output_file, 'w') as outfile:
        writer = csv.writer(outfile)
        writer.writerow(['email'])
        for email in sorted(unique_emails)
            writer.writerow([email])

```

> A user’s account shows a negative balance, which should be impossible. Describe the first two things you would check.

First verify the raw database value and transaction logs to confirm if the negative balance truly exists in storage, then audit the transaction processing code for race conditions and missing validation checks. If everything ok, i would search for the bug on frontend - examining endpoint calls or value formatting.

> Given a list of orders, each with an ID and total price, write pseudocode to find the order with the highest total.

a. Start with first order as current highest
b. Store its total as current max value
c. Move to next order in list
d. Compare its total with current max
e. If higher, update current highest order and max value
f. If not, keep existing values
g. Repeat until all orders checked
h. Return final highest order

> The marketing team wants to apply a 15% discount to all products for one day. How would you ensure this doesn’t break existing pricing logic?

Through an api middleware for discounts, to be used on the product endpoints. 
The middleware would fetch discounts info on db, set on an admin platform, and could apply discount for all products in a given day, or for a few indefenetly.  
W

> Given two lists of customer IDs from two different systems, write pseudocode to return only the IDs present in both.

```python
def find_common_ids(list1, list2):
    set1 = set(list1)
    common_ids = [id ofr id in list2 if id in set1]
    return common_ids
```

> Your API endpoint for retrieving product details has slowed down. Explain your approach to finding the cause.

For API slowdowns, first check database performance (queries, indexes, connection pools), then profile the backend (Python: cProfile/py-spy, Node: clinic.js/--inspect), optimize bottlenecks (caching, serialization, async operations), and verify infrastructure; in Python watch for CPU-bound tasks and consider ASGI scaling, while in Node monitor event loop lag and memory leaks due to its single-threaded nature, with both requiring analysis of external calls and middleware chains.

> a sales dashboard needs to show total sales per product category. write pseudocode to group sales by category and sum the totals.

Take a list of sales transactions (each with product category and amount)
Group transactions by their category
Sum the amounts within each group
Return the aggregated totals per category

```python

sales = [
    {'category': 'Electronics', 'amount': 100},
    {'category': 'Clothing', 'amount': 50},
    {'category': 'Electronics', 'amount': 200}
]

# plain python

def aggregation(sales_data):
    category_totals = {}
    for sale in sales_data:
        category = sale['category']
        amount = sale['amount']
        if category not in category_totals:
            category_totals[category] = 0
        category_totals[category] += amount
    return category_totals

# pandas

import pandas as pd
def aggregate_sales_pandas(sales_date)
    df = pd.DataFrame(sales_data)
    return df.groupby('category')['amount'].sum().to_dict()


```

> A user reports that their profile picture disappears randomly. How would you investigate whether this is a frontend or backend issue?

I would make a script to call the profile pictures endpoint a hundred times. if its faulty i would look for race conditions of backend. if it delivers all the times, i would test on incodignito mode on cosole (no extensions). then console and network tab on browser to see ifs properly making requests and loading. if not i would check the state management on the given framework.

> Given a list of invoices with amounts and dates, write pseudocode to return only invoices from the current month.

```

sql

SELECT * 
FROM invoices
WHERE EXTRACT(MONTH FROM date) = EXTRACT(MONTH FROM CURRENT_DATE)
  AND EXTRACT(YEAR FROM date) = EXTRACT(YEAR FROM CURRENT_DATE);


mongodb

{
  $expr: {
    $and: [
      { $eq: [{ $month: "$date" }, { $month: new Date() }] },
      { $eq: [{ $year: "$date" }, { $year: new Date() }] }
    ]
  }
}

```

> A ride-sharing app wants to find the driver with the most completed trips in the past week. How would you design a query or algorithm for this?

> The product manager wants to add “related products” suggestions. Explain the factors you’d consider before deciding how to implement it.

> You have transaction data with timestamps. Write pseudocode to find the hour of the day with the highest number of transactions.

> A support team reports that user search is returning outdated results. Describe your step-by-step plan to fix this.

> Given a list of products with quantities in stock, write pseudocode to identify items with quantity less than 5.

> Your company is integrating a third-party payment API. What steps would you take to ensure it’s reliable before going live?

> You have a dataset of user sessions with start and end times. Write pseudocode to calculate the average session duration.

> The analytics team says daily active users dropped 15% last month. Explain how you’d investigate possible causes.

> Given sales records, write pseudocode to find the top 5% of customers by total spending.

> The CTO asks you to improve system performance. Explain how you’d turn this vague request into specific, measurable engineering tasks.
