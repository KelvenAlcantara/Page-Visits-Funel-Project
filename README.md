# Page Visits Funel Project

## Overview
This project utilizes the Pandas library to perform an in-depth analysis of user behavior on a t-shirt store website. The goal is to understand how users navigate through the site and identify potential friction points in the purchasing process.

## Funnel Analysis
I created three funnels to evaluate user interaction at different stages:
- Only Visits – Percentage of users who visit the site but do not add items to the cart.
- Cart Abandonment – Percentage of users who add items to the cart but do not proceed to checkout.
- Checkout Abandonment – Percentage of users who reach the checkout but do not complete the purchase.

## Key Insights
A high percentage of users visit the site without adding products to the cart.
A significant number of users who reach checkout complete their purchases.
This suggests that while the checkout process is efficient, improvements in the site’s layout and user experience could encourage more users to move beyond the initial visit and engage with the purchasing process.

## Notebook
```python
import pandas as pd
```

Import all the files


```python
visits = pd.read_csv('visits.csv',
                     parse_dates=[1])
cart = pd.read_csv('cart.csv',
                   parse_dates=[1])
                   
checkout = pd.read_csv('checkout.csv',
                       parse_dates=[1])
purchase = pd.read_csv('purchase.csv',
                       parse_dates=[1])
```

Step 1: Inspect the DataFrames using `print` and `head`


```python
print(visits.head(5))
print(cart.head(5))
print(checkout.head(5))
print(purchase.head(5))
```

                                    user_id          visit_time
    0  943647ef-3682-4750-a2e1-918ba6f16188 2017-04-07 15:14:00
    1  0c3a3dd0-fb64-4eac-bf84-ba069ce409f2 2017-01-26 14:24:00
    2  6e0b2d60-4027-4d9a-babd-0e7d40859fb1 2017-08-20 08:23:00
    3  6879527e-c5a6-4d14-b2da-50b85212b0ab 2017-11-04 18:15:00
    4  a84327ff-5daa-4ba1-b789-d5b4caf81e96 2017-02-27 11:25:00
                                    user_id           cart_time
    0  2be90e7c-9cca-44e0-bcc5-124b945ff168 2017-11-07 20:45:00
    1  4397f73f-1da3-4ab3-91af-762792e25973 2017-05-27 01:35:00
    2  a9db3d4b-0a0a-4398-a55a-ebb2c7adf663 2017-03-04 10:38:00
    3  b594862a-36c5-47d5-b818-6e9512b939b3 2017-09-27 08:22:00
    4  a68a16e2-94f0-4ce8-8ce3-784af0bbb974 2017-07-26 15:48:00
                                    user_id       checkout_time
    0  d33bdc47-4afa-45bc-b4e4-dbe948e34c0d 2017-06-25 09:29:00
    1  4ac186f0-9954-4fea-8a27-c081e428e34e 2017-04-07 20:11:00
    2  3c9c78a7-124a-4b77-8d2e-e1926e011e7d 2017-07-13 11:38:00
    3  89fe330a-8966-4756-8f7c-3bdbcd47279a 2017-04-20 16:15:00
    4  3ccdaf69-2d30-40de-b083-51372881aedd 2017-01-08 20:52:00
                                    user_id       purchase_time
    0  4b44ace4-2721-47a0-b24b-15fbfa2abf85 2017-05-11 04:25:00
    1  02e684ae-a448-408f-a9ff-dcb4a5c99aac 2017-09-05 08:45:00
    2  4b4bc391-749e-4b90-ab8f-4f6e3c84d6dc 2017-11-20 20:49:00
    3  a5dbb25f-3c36-4103-9030-9f7c6241cd8d 2017-01-22 15:18:00
    4  46a3186d-7f5a-4ab9-87af-84d05bfd4867 2017-06-11 11:32:00
    

Step 2: Left merging visits and cart


```python
visits_cart = pd.merge(visits, cart, how='left')
print(visits_cart.head())
```

                                    user_id          visit_time  \
    0  943647ef-3682-4750-a2e1-918ba6f16188 2017-04-07 15:14:00   
    1  0c3a3dd0-fb64-4eac-bf84-ba069ce409f2 2017-01-26 14:24:00   
    2  6e0b2d60-4027-4d9a-babd-0e7d40859fb1 2017-08-20 08:23:00   
    3  6879527e-c5a6-4d14-b2da-50b85212b0ab 2017-11-04 18:15:00   
    4  a84327ff-5daa-4ba1-b789-d5b4caf81e96 2017-02-27 11:25:00   
    
                cart_time  
    0                 NaT  
    1 2017-01-26 14:44:00  
    2 2017-08-20 08:31:00  
    3                 NaT  
    4                 NaT  
    

Step 3: How long is `visits_cart`?


```python
visits_cart_len = len(visits_cart)
print(visits_cart_len)
```

    2000
    

Step 4: How many timestamps are null for `cart_time`?


```python
null_tmst_cart_time = len(visits_cart[(visits_cart.cart_time).isnull()])
print(null_tmst_cart_time)
```

    1652
    

Step 5: What percentage only visited?


```python
only_visit_perc = float(null_tmst_cart_time) / float(visits_cart_len)
print(only_visit_perc)
```

    0.826
    

Step 6: What percentage placed a t-shirt in their cart but did not checkout?


```python
check_or_placed = pd.merge(cart, checkout, how='left')
#print(check_or_placed)

not_check_but_placed = len(check_or_placed[check_or_placed.checkout_time.isnull()]) / len(cart) 
print(not_check_but_placed)
```

    0.3505747126436782
    

Step 7: Merge it all together


```python
from tabulate import tabulate

all_df = visits_cart.merge(checkout, how='left').merge(checkout, how='left').merge(purchase, how='left')
print(tabulate(all_df.head(), headers='keys', tablefmt='grid'))
```

    +----+--------------------------------------+---------------------+---------------------+---------------------+---------------------+
    |    | user_id                              | visit_time          | cart_time           | checkout_time       | purchase_time       |
    +====+======================================+=====================+=====================+=====================+=====================+
    |  0 | 943647ef-3682-4750-a2e1-918ba6f16188 | 2017-04-07 15:14:00 | NaT                 | NaT                 | NaT                 |
    +----+--------------------------------------+---------------------+---------------------+---------------------+---------------------+
    |  1 | 0c3a3dd0-fb64-4eac-bf84-ba069ce409f2 | 2017-01-26 14:24:00 | 2017-01-26 14:44:00 | 2017-01-26 14:54:00 | 2017-01-26 15:08:00 |
    +----+--------------------------------------+---------------------+---------------------+---------------------+---------------------+
    |  2 | 6e0b2d60-4027-4d9a-babd-0e7d40859fb1 | 2017-08-20 08:23:00 | 2017-08-20 08:31:00 | NaT                 | NaT                 |
    +----+--------------------------------------+---------------------+---------------------+---------------------+---------------------+
    |  3 | 6879527e-c5a6-4d14-b2da-50b85212b0ab | 2017-11-04 18:15:00 | NaT                 | NaT                 | NaT                 |
    +----+--------------------------------------+---------------------+---------------------+---------------------+---------------------+
    |  4 | a84327ff-5daa-4ba1-b789-d5b4caf81e96 | 2017-02-27 11:25:00 | NaT                 | NaT                 | NaT                 |
    +----+--------------------------------------+---------------------+---------------------+---------------------+---------------------+
    

Step 8: % of users who got to checkout but did not purchase


```python
didnot_purchase = float(len(all_df[(all_df.purchase_time.isnull()) & (~all_df.checkout_time.isnull())])) / float(len(all_df[~all_df.checkout_time.isnull()]))
                                                                                                        
print(didnot_purchase)
```

    0.24550898203592814
    

Step 9: check each part of the funnel, let's print all 3 of them again


```python
print(f"The percentage of people that only visit the site was {round((only_visit_perc*100), 2)}")
print(f"The percentage of people that placed the item in the cart but didn't checked was {round((not_check_but_placed*100), 2)}")
print(f"The percentage of people that put the item in checkout but didn't bought was {round((didnot_purchase*100), 2)}")
```

    The percentage of people that only visit the site was 82.6
    The percentage of people that placed the item in the cart but didn't checked was 35.06
    The percentage of people that put the item in checkout but didn't bought was 24.55
    

*The weakest part of the funnel is clearly getting a person who visited the site to add a tshirt to their cart. Once they've added a t-shirt to their cart it is fairly likely they end up purchasing it. A suggestion could be to make the add-to-cart button more prominent on the front page.*


Step 10: adding new column


```python
all_df['time_to_click'] = all_df.purchase_time - all_df.visit_time
```

Step 11: examine the results


```python
print(all_df['time_to_click'].head())
```

    0               NaT
    1   0 days 00:44:00
    2               NaT
    3               NaT
    4               NaT
    Name: time_to_click, dtype: timedelta64[ns]
    

Step 12: average time to purchase


```python
avg_time_click = all_df['time_to_click'].mean()
```


```python
print(avg_time_click)
```

    0 days 00:43:12.380952380
