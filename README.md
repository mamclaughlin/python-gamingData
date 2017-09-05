
# Dependencies & Reading File



```python
#import dependencies
import pandas as pd
import numpy as np

#import file
file = "Resources/purchase_data.json"

#read file
df = pd.read_json(file)
df.head()
# df.style.format({"Price": "${:.2f}"})

```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Age</th>
      <th>Gender</th>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Price</th>
      <th>SN</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>38</td>
      <td>Male</td>
      <td>165</td>
      <td>Bone Crushing Silver Skewer</td>
      <td>3.37</td>
      <td>Aelalis34</td>
    </tr>
    <tr>
      <th>1</th>
      <td>21</td>
      <td>Male</td>
      <td>119</td>
      <td>Stormbringer, Dark Blade of Ending Misery</td>
      <td>2.32</td>
      <td>Eolo46</td>
    </tr>
    <tr>
      <th>2</th>
      <td>34</td>
      <td>Male</td>
      <td>174</td>
      <td>Primitive Blade</td>
      <td>2.46</td>
      <td>Assastnya25</td>
    </tr>
    <tr>
      <th>3</th>
      <td>21</td>
      <td>Male</td>
      <td>92</td>
      <td>Final Critic</td>
      <td>1.36</td>
      <td>Pheusrical25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>23</td>
      <td>Male</td>
      <td>63</td>
      <td>Stormfury Mace</td>
      <td>1.27</td>
      <td>Aela59</td>
    </tr>
  </tbody>
</table>
</div>



# Number of Players


```python
#Player Count
total_players = pd.DataFrame(
    np.array(
        [
            [len(df["SN"].unique())], 
        ]))

total_players.columns = ["Total Players"]
total_players
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>573</td>
    </tr>
  </tbody>
</table>
</div>



# Purchasing Analysis (Total)


```python
total_purchase_amount_sum = df["Price"].sum()
average_price = total_purchase_amount_sum/len(df.index)
total_num_purchases = len(df.index)
total_rev = df["Price"].sum()


purchasing_analysis = pd.DataFrame(
    np.array(
        [
            [len(df["Item ID"].unique()), 
             average_price,
             total_num_purchases,
             total_rev
            ]
            
        ]))
purchasing_analysis.columns = ["Number of Unique Items", "Average Price", "Number of Purchases", "Total Revenue"]
purchasing_analysis.style.format({"Average Price": "${:.2f}", "Total Revenue":"${:.2f}"})
purchasing_analysis
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Number of Unique Items</th>
      <th>Average Price</th>
      <th>Number of Purchases</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>183.0</td>
      <td>2.931192</td>
      <td>780.0</td>
      <td>2286.33</td>
    </tr>
  </tbody>
</table>
</div>



# Gender Demographics


```python
# Count
gender = df["Gender"].value_counts()

#more precise, by SN 
new_gender = df.groupby("Gender")["SN"].unique()
len(new_gender["Female"])
len(new_gender["Male"])
len(new_gender["Other / Non-Disclosed"])


# Percentage
female_gender_percent = len(new_gender["Female"])/len(df["SN"].unique()) * 100
male_gender_percent = len(new_gender["Male"])/len(df["SN"].unique()) * 100
non_disclosed_gender = len(new_gender["Other / Non-Disclosed"])/len(df["SN"].unique()) * 100

percentage = pd.DataFrame(
        {   "Gender": ["Female", "Male", "Non Disclosed Gender"],
            "Percentage of Players": [female_gender_percent,male_gender_percent,non_disclosed_gender],
            "Total Count": [len(new_gender["Female"]),len(new_gender["Male"]),len(new_gender["Other / Non-Disclosed"])],
        }
    )
percentage[["Gender","Total Count","Percentage of Players"]]
new_percentage = percentage.set_index(['Gender'])

del new_percentage.index.name
new_percentage


```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Percentage of Players</th>
      <th>Total Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>17.452007</td>
      <td>100</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>81.151832</td>
      <td>465</td>
    </tr>
    <tr>
      <th>Non Disclosed Gender</th>
      <td>1.396161</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>



# Purchasing Analysis (Gender)


```python
#Purchase Count Variables
filtered_f_purchase_price = df.loc[df["Gender"] == "Female"]
filtered_m_purchase_price = df.loc[df["Gender"] == "Male"]
filtered_n_purchase_price = df.loc[df["Gender"] == "Other / Non-Disclosed"]

# Purchase total
filtered_f_price_total = filtered_f_purchase_price["Price"].sum()
filtered_m_price_total = filtered_m_purchase_price["Price"].sum()
filtered_n_price_total = filtered_n_purchase_price["Price"].sum()

# Average price 
avg_purchase_f_price = filtered_f_price_total/gender["Female"]
avg_purchase_m_price = filtered_m_price_total/gender["Male"]
avg_purchase_n_price = filtered_n_price_total/gender["Other / Non-Disclosed"]

# normalized purchase
normalized_f_purchase = filtered_f_price_total/len(filtered_f_purchase_price["SN"].value_counts())
normalized_m_purchase = filtered_m_price_total/len(filtered_m_purchase_price["SN"].value_counts())
normalized_n_purchase = filtered_n_price_total/len(filtered_n_purchase_price["SN"].value_counts())


purchase_analysis = pd.DataFrame(
        {   "Gender": ["Female", "Male", "Non Disclosed Gender"],
            "Purchase Count": [gender["Female"],gender["Male"],gender["Other / Non-Disclosed"]],
            "Average Purchase Price": [avg_purchase_f_price,avg_purchase_m_price,avg_purchase_n_price],
            "Total Purchase Value": [filtered_f_price_total,filtered_m_price_total,filtered_n_price_total],
            "Normalized Totals": [normalized_f_purchase,normalized_m_purchase,normalized_n_purchase],
        }
    )
purchase_analysis[["Gender","Purchase Count","Average Purchase Price", "Total Purchase Value", "Normalized Totals"]]
new_purchase_analysis = purchase_analysis.set_index(['Gender'])

del new_purchase_analysis.index.name
new_purchase_analysis
```


    ---------------------------------------------------------------------------

    ValueError                                Traceback (most recent call last)

    <ipython-input-152-17120ae36a90> in <module>()
          1 #Purchase Count Variables
    ----> 2 filtered_f_purchase_price = df.loc[df["Gender"] == "Female" and df["SN"].unique()]
          3 filtered_m_purchase_price = df.loc[df["Gender"] == "Male"]
          4 filtered_n_purchase_price = df.loc[df["Gender"] == "Other / Non-Disclosed"]
          5 


    /Users/Mariah/anaconda/lib/python3.6/site-packages/pandas/core/generic.py in __nonzero__(self)
        951         raise ValueError("The truth value of a {0} is ambiguous. "
        952                          "Use a.empty, a.bool(), a.item(), a.any() or a.all()."
    --> 953                          .format(self.__class__.__name__))
        954 
        955     __bool__ = __nonzero__


    ValueError: The truth value of a Series is ambiguous. Use a.empty, a.bool(), a.item(), a.any() or a.all().


# Age Demographics
## Count and Percentage


```python
#Count
under_ten = len(df.loc[df["Age"] <= 10]) 
ten_fourteen = len(df.loc[(df["Age"] >= 10) & (df["Age"] <= 14)])
fift_nint = len(df.loc[(df["Age"] >= 15) & (df["Age"] <= 19)]) 
twent_twentfour = len(df.loc[(df["Age"] >= 20) & (df["Age"] <= 24)])
twentfive_twentnine = len(df.loc[(df["Age"] >= 25) & (df["Age"] <= 29)])
thirty_thirtyfour = len(df.loc[(df["Age"] >= 30) & (df["Age"] <= 34)])
thirtyfi_thirtynine = len(df.loc[(df["Age"] >= 35) & (df["Age"] <= 39)])
fourty_plus = len(df.loc[(df["Age"] >= 40)])

# Percentage
p_under_ten = under_ten/len(df.index) * 100
p_ten_fourteen = ten_fourteen/len(df.index) * 100
p_fift_nint = fift_nint/len(df.index) * 100
p_twent_twentfour = twent_twentfour/len(df.index) * 100
p_twentfive_twentnine = twentfive_twentnine/len(df.index) * 100
p_thirty_thirtyfour = thirty_thirtyfour/len(df.index) * 100
p_thirtyfi_thirtynine = thirtyfi_thirtynine/len(df.index) * 100
p_fourty_plus = fourty_plus/len(df.index) * 100

age_count_analysis = pd.DataFrame(
        {   "Age": ["<10", "10-14", "15-19", "20-24", "25-29", "30-34", "35-39", "40+"],
            "Percentage of Players": [p_under_ten, p_ten_fourteen, p_fift_nint, p_twent_twentfour, p_twentfive_twentnine, p_thirty_thirtyfour, p_thirtyfi_thirtynine, p_fourty_plus],
            "Total Count": [under_ten, ten_fourteen, fift_nint, twent_twentfour, twentfive_twentnine, thirty_thirtyfour,thirtyfi_thirtynine,fourty_plus],
        }
    )
age_count_analysis[["Age","Percentage of Players", "Total Count"]]
new_age_count_analysis = age_count_analysis.set_index(["Age"])

del new_age_count_analysis.index.name
new_age_count_analysis
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Percentage of Players</th>
      <th>Total Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>4.102564</td>
      <td>32</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>4.487179</td>
      <td>35</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>17.051282</td>
      <td>133</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>43.076923</td>
      <td>336</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>16.025641</td>
      <td>125</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>8.205128</td>
      <td>64</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>5.384615</td>
      <td>42</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>2.179487</td>
      <td>17</td>
    </tr>
  </tbody>
</table>
</div>



## Purchase Count, Average Purchase Price, Total Purchase Value, Normalized Totals


```python
# average purchase price --> use len() w/o unique()
# general values to work with
ap_under_ten = df.loc[df["Age"] <= 10] 
ap_ten_fourteen = df.loc[(df["Age"] >= 10) & (df["Age"] <= 14)]
ap_fift_nint = df.loc[(df["Age"] >= 15) & (df["Age"] <= 19)]
ap_twent_twentfour = df.loc[(df["Age"] >= 20) & (df["Age"] <= 24)]
ap_twentfive_twentnine = df.loc[(df["Age"] >= 25) & (df["Age"] <= 29)]
ap_thirty_thirtyfour = df.loc[(df["Age"] >= 30) & (df["Age"] <= 34)]
ap_thirtyfi_thirtynine = df.loc[(df["Age"] >= 35) & (df["Age"] <= 39)]
ap_fourty_plus = df.loc[(df["Age"] >= 40)]

# normalized 
n_under_ten = len(ap_under_ten["SN"].unique())
n_ten_fourteen = len(ap_ten_fourteen["SN"].unique())
n_fift_nint = len(ap_fift_nint["SN"].unique())
n_twent_twentfour = len(ap_twent_twentfour["SN"].unique())
n_twentfive_twentnine = len(ap_twentfive_twentnine["SN"].unique())
n_thirty_thirtyfour = len(ap_thirty_thirtyfour["SN"].unique())
n_thirtyfi_thirtynine = len(ap_thirtyfi_thirtynine["SN"].unique())
n_fourty_plus = len(ap_fourty_plus["SN"].unique())


# averages
av_under_ten = ap_under_ten["Price"].sum()/under_ten
av_ten_fourteen = ap_ten_fourteen["Price"].sum()/ten_fourteen
av_fift_nint = ap_fift_nint["Price"].sum()/fift_nint
av_twent_twentfour = ap_twent_twentfour["Price"].sum()/twent_twentfour
av_twentfive_twentnine = ap_twentfive_twentnine["Price"].sum()/twentfive_twentnine
av_thirty_thirtyfour = ap_thirty_thirtyfour["Price"].sum()/thirty_thirtyfour
av_thirtyfi_thirtynine = ap_thirtyfi_thirtynine["Price"].sum()/thirtyfi_thirtynine
av_fourty_plus = ap_fourty_plus["Price"].sum()/fourty_plus

# total purchase value
t_under_ten = ap_under_ten["Price"].sum()
t_ten_fourteen = ap_ten_fourteen["Price"].sum()
t_fift_nint = ap_fift_nint["Price"].sum()
t_twent_twentfour = ap_twent_twentfour["Price"].sum()
t_twentfive_twentnine = ap_twentfive_twentnine["Price"].sum()
t_thirty_thirtyfour = ap_thirty_thirtyfour["Price"].sum()
t_thirtyfi_thirtynine = ap_thirtyfi_thirtynine["Price"].sum()
t_fourty_plus = ap_fourty_plus["Price"].sum()


ap_analysis = pd.DataFrame(
        {   "Age": ["<10", "10-14", "15-19", "20-24", "25-29", "30-34", "35-39", "40+"],
            "Purchase Count": [under_ten, ten_fourteen, fift_nint, twent_twentfour, twentfive_twentnine,thirty_thirtyfour,thirtyfi_thirtynine,fourty_plus],
            "Average Purchase Price": [av_under_ten,av_ten_fourteen,av_fift_nint,av_twent_twentfour,av_twentfive_twentnine,av_thirty_thirtyfour,av_thirtyfi_thirtynine,av_fourty_plus],
           "Total Purchase Value": [t_under_ten,t_ten_fourteen,t_fift_nint,t_twent_twentfour,t_twentfive_twentnine,t_thirty_thirtyfour,t_thirtyfi_thirtynine, t_fourty_plus],
            "Normalized Totals": [n_under_ten,n_ten_fourteen,n_fift_nint,n_twent_twentfour,n_twentfive_twentnine,n_thirty_thirtyfour,n_thirtyfi_thirtynine,n_fourty_plus]
        }
    )
ap_analysis[["Age","Purchase Count", "Average Purchase Price"]]
new_ap_analysis = ap_analysis.set_index(['Age'])

del new_ap_analysis.index.name
new_ap_analysis
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Purchase Price</th>
      <th>Normalized Totals</th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>3.019375</td>
      <td>22</td>
      <td>32</td>
      <td>96.62</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>2.770000</td>
      <td>23</td>
      <td>35</td>
      <td>96.95</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>2.905414</td>
      <td>100</td>
      <td>133</td>
      <td>386.42</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>2.913006</td>
      <td>259</td>
      <td>336</td>
      <td>978.77</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>2.962640</td>
      <td>87</td>
      <td>125</td>
      <td>370.33</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>3.082031</td>
      <td>47</td>
      <td>64</td>
      <td>197.25</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>2.842857</td>
      <td>27</td>
      <td>42</td>
      <td>119.40</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>3.161765</td>
      <td>11</td>
      <td>17</td>
      <td>53.75</td>
    </tr>
  </tbody>
</table>
</div>



# Top Spenders


```python
df2 = df.groupby('SN').sum()
df4 = df2.sort_values(["Price"], ascending=False)

# get presence of each SN
df3 = df["SN"].value_counts()


users = []
total_purchase_value = []
total_counts = []
average_purchase = []
df3.loc[users]

for x in range(5):
    users.append(df4.index[x])
    total_purchase_value.append(df4["Price"][x])
    total_counts.append(df3.loc[users][x])
    average_purchase.append(total_purchase_value[x]/total_counts[x])


top_analysis = pd.DataFrame(
        {   "SN": [x for x in users],
            "Purchase Count": [x for x in total_counts],
            "Average Purchase Price": [x for x in average_purchase],
            "Total Purchase Value": [x for x in total_purchase_value],
        }
    )
top_analysis[["SN","Purchase Count", "Average Purchase Price", "Total Purchase Value"]]

new_top_analysis = top_analysis.set_index(['SN'])

del new_top_analysis.index.name
new_top_analysis
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Purchase Price</th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Undirrala66</th>
      <td>3.412000</td>
      <td>5</td>
      <td>17.06</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>3.390000</td>
      <td>4</td>
      <td>13.56</td>
    </tr>
    <tr>
      <th>Mindimnya67</th>
      <td>3.185000</td>
      <td>4</td>
      <td>12.74</td>
    </tr>
    <tr>
      <th>Haellysu29</th>
      <td>4.243333</td>
      <td>3</td>
      <td>12.73</td>
    </tr>
    <tr>
      <th>Eoda93</th>
      <td>3.860000</td>
      <td>3</td>
      <td>11.58</td>
    </tr>
  </tbody>
</table>
</div>



# Most Popular Items


```python
# Most Popular Items
item_id = []
item_purch_count = []

# most popular item - find most popular items
items = df["Item ID"].value_counts()

#used to test information
df.loc[df["Item ID"] == 84][["Item Name","Price"]]

#get Item IDs
for i in range(5):
    item_id.append(items.index[i])

# find price of items
for i in range(5):
    counts = df.loc[df["Item ID"].unique() == item_id[i]]
    item_purch_count.append(counts["Price"])


#filters item id's that match the array of popular items
matches = df.loc[df["Item ID"].isin(item_id)][["Item Name","Price", "Item ID"]]

#gets purchase count and sum
grouped_matches = matches.groupby(["Item Name"]).count()
matches_price_sum = matches.groupby(["Item Name"]).sum()

popular_items = pd.DataFrame(
        {   "Item ID": [x for x in item_id],
            "Item Name": [x for x in grouped_matches.index],
            "Purchase Count": [x for x in grouped_matches["Price"]],
            "Item Price": [x for x in matches_price_sum["Price"]/grouped_matches["Price"]],
            "Total Purchase Value": [x for x in matches_price_sum["Price"]]
        }
    )
popular_items[["Item ID", "Item Name", "Purchase Count", "Item Price", "Total Purchase Value"]]

new_popular_items = popular_items.set_index(['Item Name'])

del new_popular_items.index.name
new_popular_items
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Item ID</th>
      <th>Item Price</th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Arcane Gem</th>
      <td>84</td>
      <td>2.23</td>
      <td>11</td>
      <td>24.53</td>
    </tr>
    <tr>
      <th>Betrayal, Whisper of Grieving Widows</th>
      <td>39</td>
      <td>2.35</td>
      <td>11</td>
      <td>25.85</td>
    </tr>
    <tr>
      <th>Retribution Axe</th>
      <td>31</td>
      <td>4.14</td>
      <td>9</td>
      <td>37.26</td>
    </tr>
    <tr>
      <th>Trickster</th>
      <td>34</td>
      <td>2.07</td>
      <td>9</td>
      <td>18.63</td>
    </tr>
    <tr>
      <th>Woeful Adamantite Claymore</th>
      <td>175</td>
      <td>1.24</td>
      <td>9</td>
      <td>11.16</td>
    </tr>
  </tbody>
</table>
</div>



# Most Profitable Items


```python
p_item_id = []
p_item_purch_total = []

# most profitable item - find most popular items
p_items = df.groupby("Item ID").sum(ascending=True)
p_items_totals = p_items.sort_values(["Price"], ascending=False)

p_items_totals.iloc[0:5]["Price"].to_frame()

# #used to test information
# df.loc[df["Item ID"] == 84][["Item Name","Price"]]

#get Item IDs & purchase total
for i in range(5):
    p_item_id.append(p_items_totals.index[i])
    p_item_purch_total.append(p_items_totals["Price"].head(i))

p_item_id
p_item_purch_total


# #filters item id's that match the array of popular items
p_matches = df.loc[df["Item ID"].isin(p_item_id)][["Item Name","Price", "Item ID"]]


# #gets purchase count and sum
p_grouped_matches = p_matches.groupby(["Item Name"]).count()
p_matches_price_sum = p_matches.groupby(["Item Name"]).sum()

profitable_items = pd.DataFrame(
        {   "Item ID": [x for x in p_item_id],
            "Item Name": [x for x in p_grouped_matches.index],
            "Purchase Count": [x for x in p_grouped_matches["Price"]],
            "Item Price": [x for x in p_matches_price_sum["Price"]/p_grouped_matches["Price"]],
            "Total Purchase Value": [x for x in p_matches_price_sum["Price"]]
        }
    )
profitable_items[["Item ID", "Item Name", "Purchase Count", "Item Price", "Total Purchase Value"]]

new_profitable_items = profitable_items.set_index(['Item Name'])

del new_profitable_items.index.name
new_profitable_items


```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Item ID</th>
      <th>Item Price</th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Orenmir</th>
      <td>34</td>
      <td>4.95</td>
      <td>6</td>
      <td>29.70</td>
    </tr>
    <tr>
      <th>Retribution Axe</th>
      <td>115</td>
      <td>4.14</td>
      <td>9</td>
      <td>37.26</td>
    </tr>
    <tr>
      <th>Singed Scalpel</th>
      <td>32</td>
      <td>4.87</td>
      <td>6</td>
      <td>29.22</td>
    </tr>
    <tr>
      <th>Spectral Diamond Doomblade</th>
      <td>103</td>
      <td>4.25</td>
      <td>7</td>
      <td>29.75</td>
    </tr>
    <tr>
      <th>Splitter, Foe Of Subtlety</th>
      <td>107</td>
      <td>3.61</td>
      <td>8</td>
      <td>28.88</td>
    </tr>
  </tbody>
</table>
</div>




```python

```


```python

```
