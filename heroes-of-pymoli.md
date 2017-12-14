
# Heroes of Pymoli


```python
# import dependencies
import pandas as pd
import json
import os
```


```python
jsondata = os.path.join("Resources","purchase_data.json")
```


```python
#Read JSON data into a variable
with open(jsondata) as json_data:
    d = json.load(json_data)
```


```python
#turn data into dataframe
game_df = pd.DataFrame(d, columns=['SN', 'Age', 'Gender', 'Item ID', 'Item Name', 'Price'])
game_df.head()
```




<div>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>SN</th>
      <th>Age</th>
      <th>Gender</th>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Aelalis34</td>
      <td>38</td>
      <td>Male</td>
      <td>165</td>
      <td>Bone Crushing Silver Skewer</td>
      <td>3.37</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Eolo46</td>
      <td>21</td>
      <td>Male</td>
      <td>119</td>
      <td>Stormbringer, Dark Blade of Ending Misery</td>
      <td>2.32</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Assastnya25</td>
      <td>34</td>
      <td>Male</td>
      <td>174</td>
      <td>Primitive Blade</td>
      <td>2.46</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Pheusrical25</td>
      <td>21</td>
      <td>Male</td>
      <td>92</td>
      <td>Final Critic</td>
      <td>1.36</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Aela59</td>
      <td>23</td>
      <td>Male</td>
      <td>63</td>
      <td>Stormfury Mace</td>
      <td>1.27</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Player Count
total_players = len(game_df['SN'].value_counts())
Total_Players = pd.DataFrame({"Total Players": total_players}, index=[0])
Total_Players
```




<div>

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



# Purchasing Analysis


```python
#Number of Unique Items
unique_items = len(game_df['Item ID'].value_counts())

#Average Purchase Price
average_price = game_df['Price'].mean()

#Total Number of Purchases
total_purchases = game_df['Item Name'].count()


#Total Revenue
total_revenue = game_df['Price'].sum()

#Create DataFrame
purchasing_analysis = pd.DataFrame({"Number of Unique Items": [unique_items],
                                   "Average Price": [average_price],
                                   "Total Purchases": [total_purchases],
                                   "Total Revenue": [total_revenue],
                                
})

#Reorder DataFrame
purchasing_analysis = purchasing_analysis[["Number of Unique Items", "Average Price","Total Purchases", "Total Revenue"]]
                                

#improve formatting
purchasing_analysis["Average Price"] = purchasing_analysis["Average Price"].map("${0:,.2f}".format)
purchasing_analysis["Total Revenue"] = purchasing_analysis["Total Revenue"].map("${0:,.2f}".format)

#Reorder Columns
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
      <th>Total Purchases</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>183</td>
      <td>$2.93</td>
      <td>780</td>
      <td>$2,286.33</td>
    </tr>
  </tbody>
</table>
</div>



# Gender Demographics


```python
#Group data by Gender and filter duplicates
grouped_df = game_df.groupby(["Gender"])
unique_df = grouped_df.nunique()

#Total Gender
total_gender = unique_df["SN"].sum()

#Percentage and Count of Players
count = unique_df["SN"].unique()
percentage = unique_df["SN"]/ total_gender

#Create new dataframe
final_gender = pd.DataFrame({"Percentage of Players": percentage,
                            "Count":count})
#Change percentage format and re order columns
final_gender["Percentage of Players"] = final_gender["Percentage of Players"].map("{:,.2%}".format) 
final_gender.columns = ["Percentage of Players", "Count"]
#Print final dataframe
final_gender
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
      <th>Count</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>100</td>
      <td>17.45%</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>465</td>
      <td>81.15%</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>8</td>
      <td>1.40%</td>
    </tr>
  </tbody>
</table>
</div>



# Purchasing Analysis (Gender)


```python
#Purchase Count
purchase_count = unique_df["Gender"].value_counts()

#Average Purchase Price
average_price = grouped_df["Price"].mean()

#Total Purchase Value
purchase_price = grouped_df["Price"].sum()

#Normalized Totals
normalized = purchase_price / count

#Create new dataframe
gender_analysis = pd.DataFrame({"Average Purchase Price": average_price,"Total Purchase Price":purchase_price,"Normalized Totals": normalized})

#Clean up formatting and reorder columns
gender_analysis["Average Purchase Price"] = gender_analysis["Average Purchase Price"].map("${:,.2f}".format) 
gender_analysis["Total Purchase Price"] = gender_analysis["Total Purchase Price"].map("${:,.2f}".format) 
gender_analysis["Normalized Totals"] = gender_analysis["Normalized Totals"].map("${:,.2f}".format) 
#Reorder Columns
gender_analysis = gender_analysis[["Average Purchase Price", "Total Purchase Price", "Normalized Totals"]]

gender_analysis.head()
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
      <th>Total Purchase Price</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>$2.82</td>
      <td>$382.91</td>
      <td>$3.83</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>$2.95</td>
      <td>$1,867.68</td>
      <td>$4.02</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>$3.25</td>
      <td>$35.74</td>
      <td>$4.47</td>
    </tr>
  </tbody>
</table>
</div>



# Age Demographics


```python
#Age Demographics
#Drop Duplicates
cleaned_df = game_df.drop_duplicates("SN")

#The below each broken into bins of 4 years (i.e. <10, 10-14, 15-19, etc.)
bins = [10, 14, 18, 22, 26, 30, 34, 38, 42]
groups = ["<10", "10-14", "15-19", "20-24", "25-29", "30-34", "35-39", "40+"]

#Create a  new column for age groups and then groupby Age Groups
cleaned_df["Age Groups"] = pd.cut(cleaned_df["Age"], bins, labels=groups)
age_df = cleaned_df.groupby(["Age Groups"])

total_age = unique_df["Age"].sum()

#Purchase Count
age_purchase = cleaned_df["Age Groups"].value_counts()

#Percentage of Users
age_percentage = age_purchase / total_players

age_demographics = pd.DataFrame({"Total Count": age_purchase,
                             "Percentage of Players":age_percentage})

age_demographics["Percentage of Players"] = age_demographics["Percentage of Players"].map("{:,.2%}".format) 

age_demographics = pd.concat([age_demographics.loc[["<10"],:], age_demographics.drop("<10", axis=0)], axis=0)

age_demographics
```

    C:\Users\Emily.Nassi\AppData\Local\Continuum\anaconda3\lib\site-packages\ipykernel_launcher.py:10: SettingWithCopyWarning: 
    A value is trying to be set on a copy of a slice from a DataFrame.
    Try using .loc[row_indexer,col_indexer] = value instead
    
    See the caveats in the documentation: http://pandas.pydata.org/pandas-docs/stable/indexing.html#indexing-view-versus-copy
      # Remove the CWD from sys.path while we load stuff.





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
      <td>3.49%</td>
      <td>20</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>31.06%</td>
      <td>178</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>26.70%</td>
      <td>153</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>14.66%</td>
      <td>84</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>7.68%</td>
      <td>44</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>5.93%</td>
      <td>34</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>4.36%</td>
      <td>25</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>1.92%</td>
      <td>11</td>
    </tr>
  </tbody>
</table>
</div>



# Age Analysis


```python
#Average Purchase Price
age_average_price = age_df["Price"].mean()

#Total Purchase Value
age_price = age_df["Price"].sum()

#Normalized Totals
normalized_age = age_price / age_purchase

#Create new dataframe
age_analysis = pd.DataFrame({"Purchase Count": age_purchase,
                             "Average Purchase Price":age_average_price,
                            "Total Purchase Value":age_price,
                            "Normalized Totals": normalized_age})

#Clean up formatting
age_analysis["Average Purchase Price"] = age_analysis["Average Purchase Price"].map("${:,.2f}".format) 
age_analysis["Total Purchase Value"] = age_analysis["Total Purchase Value"].map("${:,.2f}".format) 
age_analysis["Normalized Totals"] = age_analysis["Normalized Totals"].map("${:,.2f}".format) 

#Reorder Columns
age_analysis = age_analysis[["Purchase Count", "Average Purchase Price", "Total Purchase Value", "Normalized Totals"]]

#Move bottom row to the top
age_analysis = pd.concat([age_analysis.loc[["<10"],:], age_analysis.drop("<10", axis=0)], axis=0)

age_analysis.head()

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
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>20</td>
      <td>$2.60</td>
      <td>$51.99</td>
      <td>$2.60</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>84</td>
      <td>$2.89</td>
      <td>$242.39</td>
      <td>$2.89</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>178</td>
      <td>$2.94</td>
      <td>$523.43</td>
      <td>$2.94</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>153</td>
      <td>$3.01</td>
      <td>$460.79</td>
      <td>$3.01</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>44</td>
      <td>$2.98</td>
      <td>$131.23</td>
      <td>$2.98</td>
    </tr>
  </tbody>
</table>
</div>



# Top Users


```python
grouped_sn = game_df.groupby(["SN"])

#Find total spent per user
total_price_sn = grouped_sn.sum()["Price"]

#Find avg spent per user
avg_price_sn = grouped_sn.mean()["Price"]

#Find purchase count per user
count_sn = grouped_sn.count()["Price"]

#Create new dataframe
top_user_df = pd.DataFrame({"Purchase Count":count_sn,
                            "Average Purchase Price":avg_price_sn,
                            "Total Purchase Price": total_price_sn
                            })

#Sort by total purchase price
sorted_df = top_user_df.sort_values("Total Purchase Price",ascending=False)

#Format numbers
sorted_df["Average Purchase Price"] = sorted_df["Average Purchase Price"].map("${:,.2f}".format) 
sorted_df["Total Purchase Price"] = sorted_df["Total Purchase Price"].map("${:,.2f}".format) 

#Reorder Columns
sorted_df = sorted_df[["Purchase Count", "Average Purchase Price", "Total Purchase Price"]]

#Display top 5
sorted_df.head(5)

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
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Price</th>
    </tr>
    <tr>
      <th>SN</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Undirrala66</th>
      <td>5</td>
      <td>$3.41</td>
      <td>$17.06</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>4</td>
      <td>$3.39</td>
      <td>$13.56</td>
    </tr>
    <tr>
      <th>Mindimnya67</th>
      <td>4</td>
      <td>$3.18</td>
      <td>$12.74</td>
    </tr>
    <tr>
      <th>Haellysu29</th>
      <td>3</td>
      <td>$4.24</td>
      <td>$12.73</td>
    </tr>
    <tr>
      <th>Eoda93</th>
      <td>3</td>
      <td>$3.86</td>
      <td>$11.58</td>
    </tr>
  </tbody>
</table>
</div>



# Most Popular Items


```python
grouped_id = game_df.set_index(["Item ID", "Item Name"])

grouped_id = grouped_id.groupby(level=["Item ID", "Item Name"])

#Find total spent per user
total_price_id = grouped_id.sum()["Price"]

#Find avg spent per user
avg_price_id = grouped_id.mean()["Price"]

#Find purchase count per user
count_id = grouped_id.count()["Price"]


#Create new dataframe
items_df = pd.DataFrame({ 
                         "Count":count_id,
                            "Average Purchase Price":avg_price_id,
                            "Total Purchase Price": total_price_id,
                            })


#Sort by total purchase price
sorted_items = items_df.sort_values("Count",ascending=False)

sorted_items["Average Purchase Price"] = sorted_items["Average Purchase Price"].map("${:,.2f}".format) 
sorted_items["Total Purchase Price"] = sorted_items["Total Purchase Price"].map("${:,.2f}".format) 

#Reorder Columns
sorted_items = sorted_items[["Count", "Average Purchase Price", "Total Purchase Price"]]


#Display top 5
sorted_items.head(5)


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
      <th></th>
      <th>Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Price</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>39</th>
      <th>Betrayal, Whisper of Grieving Widows</th>
      <td>11</td>
      <td>$2.35</td>
      <td>$25.85</td>
    </tr>
    <tr>
      <th>84</th>
      <th>Arcane Gem</th>
      <td>11</td>
      <td>$2.23</td>
      <td>$24.53</td>
    </tr>
    <tr>
      <th>31</th>
      <th>Trickster</th>
      <td>9</td>
      <td>$2.07</td>
      <td>$18.63</td>
    </tr>
    <tr>
      <th>175</th>
      <th>Woeful Adamantite Claymore</th>
      <td>9</td>
      <td>$1.24</td>
      <td>$11.16</td>
    </tr>
    <tr>
      <th>13</th>
      <th>Serenity</th>
      <td>9</td>
      <td>$1.49</td>
      <td>$13.41</td>
    </tr>
  </tbody>
</table>
</div>



# Most Profitable Items


```python

grouped_id = game_df.set_index(["Item ID", "Item Name"])

grouped_id = grouped_id.groupby(level=["Item ID", "Item Name"])

#Find total spent per user
total_price_id = grouped_id.sum()["Price"]

#Find avg spent per user
avg_price_id = grouped_id.mean()["Price"]

#Find purchase count per user
count_id = grouped_id.count()["Price"]

#Create new dataframe
items_df = pd.DataFrame({ "Count":count_id,
                        "Average Purchase Price":avg_price_id,
                        "Total Purchase Price": total_price_id,
                            })


#Sort by total purchase price
sorted_items = items_df.sort_values("Total Purchase Price",ascending=False)

sorted_items["Average Purchase Price"] = sorted_items["Average Purchase Price"].map("${:,.2f}".format) 
sorted_items["Total Purchase Price"] = sorted_items["Total Purchase Price"].map("${:,.2f}".format) 

#Reorder Columns
sorted_items = sorted_items[["Count", "Average Purchase Price", "Total Purchase Price"]]

#Display top 5
sorted_items.head(5)

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
      <th></th>
      <th>Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Price</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>34</th>
      <th>Retribution Axe</th>
      <td>9</td>
      <td>$4.14</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>115</th>
      <th>Spectral Diamond Doomblade</th>
      <td>7</td>
      <td>$4.25</td>
      <td>$29.75</td>
    </tr>
    <tr>
      <th>32</th>
      <th>Orenmir</th>
      <td>6</td>
      <td>$4.95</td>
      <td>$29.70</td>
    </tr>
    <tr>
      <th>103</th>
      <th>Singed Scalpel</th>
      <td>6</td>
      <td>$4.87</td>
      <td>$29.22</td>
    </tr>
    <tr>
      <th>107</th>
      <th>Splitter, Foe Of Subtlety</th>
      <td>8</td>
      <td>$3.61</td>
      <td>$28.88</td>
    </tr>
  </tbody>
</table>
</div>



# Analyses conducted using dataset 1.
1. Males spent 130% more than females on in app purchases and 192 percent more than other/nondisclosed. 
However, there is only a 13 cent difference in average price spent between male and female, while the average price 
spent from gender non-diclosed is 30 cents more than males.

2. Teenagers are the majority players of the game, at 30%, while 20-24 year olds make up 26 percent. There is a 19 percent
drop in players in the next age group, as 25-29 is 7.7%. 20-24 year olds spend more on average, than 15-19 year olds, while 
30-34 year olds spend the most at $3.35 per transaction.

3. The most popular items does not make the game as much money as the most profitable ones. However, 
the Retribution Axe, which is the most profitable item ties for third on the 
most popular, with a count of nine.  

