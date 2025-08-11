# The store’s backend returns product prices in cents. How would you calculate the price in dollars for display to users?

If i dont have access to backend i would write an api service on front that divides the prices' response by 100

# You receive a CSV file with customer emails, but some are duplicated. Write pseudocode to remove duplicates and output a clean list.

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

# A user’s account shows a negative balance, which should be impossible. Describe the first two things you would check.

First verify the raw database value and transaction logs to confirm if the negative balance truly exists in storage, then audit the transaction processing code for race conditions and missing validation checks. If everything ok, i would search for the bug on frontend - examining endpoint calls or value formatting.

# Given a list of orders, each with an ID and total price, write pseudocode to find the order with the highest total.

a. Start with first order as current highest
b. Store its total as current max value
c. Move to next order in list
d. Compare its total with current max
e. If higher, update current highest order and max value
f. If not, keep existing values
g. Repeat until all orders checked
h. Return final highest order

# The marketing team wants to apply a 15% discount to all products for one day. How would you ensure this doesn’t break existing pricing logic?

Through an api middleware for discounts, to be used on the product endpoints. 
The middleware would fetch discounts info on db, set on an admin platform, and could apply discount for all products in a given day, or for a few indefenetly.  
W

# Given two lists of customer IDs from two different systems, write pseudocode to return only the IDs present in both.

```python
def find_common_ids(list1, list2):
    set1 = set(list1)
    common_ids = [id ofr id in list2 if id in set1]
    return common_ids
```

# Your API endpoint for retrieving product details has slowed down. Explain your approach to finding the cause.

For API slowdowns, first check database performance (queries, indexes, connection pools), then profile the backend (Python: cProfile/py-spy, Node: clinic.js/--inspect), optimize bottlenecks (caching, serialization, async operations), and verify infrastructure; in Python watch for CPU-bound tasks and consider ASGI scaling, while in Node monitor event loop lag and memory leaks due to its single-threaded nature, with both requiring analysis of external calls and middleware chains.

# a sales dashboard needs to show total sales per product category. write pseudocode to group sales by category and sum the totals.

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

# A user reports that their profile picture disappears randomly. How would you investigate whether this is a frontend or backend issue?

I would make a script to call the profile pictures endpoint a hundred times. if its faulty i would look for race conditions of backend. if it delivers all the times, i would test on incodignito mode on cosole (no extensions). then console and network tab on browser to see ifs properly making requests and loading. if not i would check the state management on the given framework.

# Given a list of invoices with amounts and dates, write pseudocode to return only invoices from the current month.

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

# A ride-sharing app wants to find the driver with the most completed trips in the past week. How would you design a query or algorithm for this?

```js

/// js

const drives = [
    {
    driver_name: 'Marc Johnson',
    complete: false,
    date_created: 'utc',
    date_updated: 'utc'
    }
    // ...   
]


const oneWeekAgo = new Date();
oneWeekAgo.setDate(oneWeekAgo.getDate() - 7);

const best_driver = Object.entries(
  drives
    .filter(drive => drive.complete && new Date(drive.date_updated) > oneWeekAgo)
    .reduce((acc, drive) => {
      acc[drive.driver_name] = (acc[drive.driver_name] || 0) + 1;
      return acc;
    }, {})
)
.sort(([,a], [,b]) => b - a)[0];

```

```python
# Python equivalent
from datetime import datetime, timedelta
from collections import Counter

drives = [
    {
        'driver_name': 'Marc Johnson',
        'complete': False,
        'date_created': '2023-01-01T00:00:00Z',
        'date_updated': '2023-01-01T00:00:00Z'
    }
    # ...
]

one_week_ago = datetime.now() - timedelta(weeks=1)

completed_drives = [
    drive for drive in drives
    if drive['complete'] and datetime.fromisoformat(drive['date_updated'].replace('Z', '+00:00')) > one_week_ago
]

driver_counts = Counter(drive['driver_name'] for drive in completed_drives)

best_driver = driver_counts.most_common(1)[0] if driver_counts else None

one_week_ago = datetime.now() - timedelta(weeks=1)

completed_drives = [
    drive for drive in drives
    if drive['complete'] and datetime.fromisoformat(drive['date_updated'].replace('Z', '+00:00')) > one_week_ago
]

driver_counts = Counter(drive['driver_name'] for drive in completed_drives)

sorted_drivers = sorted(driver_counts.items(), key=lambda x: x[1], reverse=True)

best_driver = sorted_drivers[0] if sorted_drivers else None

```

# The product manager wants to add “related products” suggestions. Explain the factors you’d consider before deciding how to implement it.

On the AI side, i'd use Pytorch to make a item-to-item collaborative filtering based on user purchase/interaction data, like category, brand and features. For infrastructure, a microservices architecture with a dedicated recommendation service that pre-computes similarities in batch jobs with a caching layer (Redis) to handle high traffic. The system would use a message queue (like Kafka) for real-time user interaction data processing and employ A/B testing capabilities to continuously improve the recommendation algorithms. 

# You have transaction data with timestamps. Write pseudocode to find the hour of the day with the highest number of transactions.

```python
def find_peak(transactions):
    hour_counts = [0] * 24
    for transaction in transactions:
        timestamp = transaction["timestamp"]
        hour = int(timestamp.split()[1].split(":")[0])
        hours_counts +=1

    peak = 0
    max =0

    for hour in range(24):
        if hour_counts[hour] > max:
            max = hour_counts[hour]
            peak = hour
    return peak
```


# A support team reports that user search is returning outdated results. Describe your step-by-step plan to fix this.

1. Test the flow in a dev account on incognito mode (no extensions)
2. Check console and network tab for possible errors or wrong requests
3. Make database queries to see if there are new results for given users and others
4. Test backend for race conditions, caused by database transactions or async operations
5. Apply bugfixes and create index for the database in order to return new results


# Given a list of products with quantities in stock, write pseudocode to identify items with quantity less than 5.

```python

# built in method

products = [
    {'produtct': 'TV', 'quantity': 4},
    {'produtct': 'Fridge', 'quantity': 10},
    {'produtct': 'Sofa', 'quantity': 2}
]

few_stock_produtcs = [i for i in products if i['quantity'] < 5 ]

# no method

few_stock_products = []
for product in producst:
    if product['quantity'] < 5:
        few_stock_products.append(product)
```

```js

// built in method

products = [
    {'produtct': 'TV', 'quantity': 4},
    {'produtct': 'Fridge', 'quantity': 10},
    {'produtct': 'Sofa', 'quantity': 2}
]

few_stock_produtcs = products.filter(product => product.quantity < 5)

```


# Your company is integrating a third-party payment API. What steps would you take to ensure it’s reliable before going live?

My first approach wouldnt involve code at all. I would search for it in social media, SACs and forums to see if it is legit. Then i would go to their documentation, check if follows common security procedures like jwt. One important thing here is to see how the handdle user sensitive information. If they ask for credit card numbe in a request then its a big no. Then i would search for legal terms to see how they would prevent and handle data breach on their side.

After this is settled, i would prepare different scenarios on testing enviroment like failed payment, pending confirmation and etc. Stripe provides a good alternation to this with its payment session technoly, which handle the financial data on their side, and once its done retrieves http status with the result. 


# You have a dataset of user sessions with start and end times. Write pseudocode to calculate the average session duration.

```python
def calculate_session(sessions):
    if not sessions
        return 0

    total_duration = 0
    for session in sessions: 
        start_time = session['start_time']
        end_time = session['end_time']

        session_duration = end_time - start_time
        total_duration += sessions_duration

    return total_duration / len(session)

```

# The analytics team says daily active users dropped 15% last month. Explain how you’d investigate possible causes.

If there is exising feedback mechanism or active SAC i would start from there.
If not, i would check development history of the last month, to see if there was a significant change on the platform. The following step would be to test the whole platorm flow on fresh and old account to find possible issue. 

There is also a analytics approach, that heavily depends on the client. If they have a robust tracking system, i would compare the same period with the past years to see if its a expected trend. Also if the less engaged users share any distinc characteristics, and if there is a change on marketing campaigns on the given period. 

Finally, i would make a market search to see if a similar business is rising with competitive price.

# Given sales records, write pseudocode to find the top 5% of customers by total spending.


```python
def find_top_customers(sales_records):
    customer_totals = {}
    for record in sales_records:
        customer_id = record['customer_id']
        amount = record['amount']
        customer_totals[customer_id] = customer_totals.get(customer_id, 0) + amount

    sorted_customers = sorted(customer_totals.items(), keys=lambda x: x[1], reverse=True)

    num_customers = len(customers_total)
    top_percent = 0.05
    top_count = max(1, int(num_customers * top_percent))

    top_customers = sorted_customers[:top_count]
    return [{'customer_id': customer_id, 'total_spending': total} for customer_id, total in top_customers ]

```

# The CTO asks you to improve system performance. Explain how you’d turn this vague request into specific, measurable engineering tasks.

1. Start on frontend codebase, making a automated search that retrieves all the endpoints currently used on the platform.
2. Run a python script hitting every endpoint with the required bodys and out, generating then a output contiang the status and duration of each request. 
3. While running the script the python server would be on watch mode using the library memory_profiles
4. This approach would help me identidy the bottlenecks ont eh server, applying bugfixes on possible causes like race conditionios or unhanddled async operations
5. Make database queries on the affected areas to check latency, create index if there isnt none. 