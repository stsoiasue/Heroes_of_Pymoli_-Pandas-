Heros of Pymoli Data Analysis

- Observed Trend 1: Males are the majority of players of the game.
- Observed Trend 2: The Age group with the most number of players is 20-24.
- Observed Trend 3: Males spend the most in terms of actual numbers.

```python
# import dependencies
import pandas as pd
import os
```


```python
# assign file path to variable
pymo_file = os.path.join('purchase_data.json')
```


```python
# create dataframe with purchase data
pymo_raw = pd.read_json(pymo_file)
pymo_raw.head()
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




```python
player_count = pymo_raw['SN'].nunique()

# create summary DataFrame with purchase data
play_count = pd.DataFrame({'Player Count': player_count} , index=[0])
play_count
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
      <th>Player Count</th>
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




```python
unique_items = len(pymo_raw['Item Name'].unique())
```


```python
avg_pur_price = round(pymo_raw['Price'].mean(),2)
```


```python
total_purchases = len(pymo_raw)
```


```python
total_revenue = round(pymo_raw['Price'].sum(),2)
```


```python
# Purchasing Anaysis (Total)
# create dictionary to hold calculated data points
purchase_data = {
    'Unique Items': unique_items,
    'Avg Price': avg_pur_price,
    'Total Purchases': total_purchases,
    'Total Revenue': total_revenue
}

# create summary DataFrame with purchase data
purch_analysis = pd.DataFrame(purchase_data , index=[0])

# format columns with currency values
purch_analysis['Avg Price'] = purch_analysis['Avg Price'].map("${:.2f}".format)
purch_analysis['Total Revenue'] = purch_analysis['Total Revenue'].map("${:.2f}".format)

purch_analysis
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
      <th>Avg Price</th>
      <th>Total Purchases</th>
      <th>Total Revenue</th>
      <th>Unique Items</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>$2.93</td>
      <td>780</td>
      <td>$2286.33</td>
      <td>179</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Gender Demographics
# group raw data by gender
pymo_gdr = pymo_raw.groupby(['Gender'])

# get count of players by gender
pymo_gdr_count = pymo_gdr['SN'].nunique()

# calculate count by gender as a percentage of all players
pymo_gdr_pct = round(pymo_gdr_count / player_count, 4) * 100

# add count and percentage to summary DataFrame
gdr_demo = pd.DataFrame({
    'Player count': pymo_gdr_count,
    'Percentage of Players': pymo_gdr_pct
})
gdr_demo
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
      <th>Player count</th>
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
      <td>17.45</td>
      <td>100</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>81.15</td>
      <td>465</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>1.40</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Gender Purchase Analysis
# create DataFrame with count mean and sum of Price series by gender
purch_analysis_gdr = pymo_gdr['Price'].agg(['count', 'mean', 'sum'])

# change DataFrame column names
purch_analysis_gdr = purch_analysis_gdr.rename(columns={
    'count': 'Total Purchases',
    'mean': 'Avg Price',
    'sum': 'Total Revenue'
})

# add gender count to gender purchase analysis
purch_analysis_gdr['Player Count'] = pymo_gdr_count

# calculate normalized total
normalized_total_gdr = purch_analysis_gdr['Total Revenue'] / purch_analysis_gdr['Player Count']
purch_analysis_gdr['Total Revenue (Normalized)'] = normalized_total_gdr

# format columns with currency values
purch_analysis_gdr['Avg Price'] = purch_analysis_gdr['Avg Price'].map("${:.2f}".format)
purch_analysis_gdr['Total Revenue'] = purch_analysis_gdr['Total Revenue'].map("${:.2f}".format)
purch_analysis_gdr['Total Revenue (Normalized)'] = purch_analysis_gdr['Total Revenue (Normalized)'].map("${:.2f}".format)

# rearrange columns
purch_analysis_gdr = purch_analysis_gdr[[
    'Player Count', 'Total Purchases',
    'Avg Price', 'Total Revenue',
    'Total Revenue (Normalized)'
]]   

purch_analysis_gdr
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
      <th>Player Count</th>
      <th>Total Purchases</th>
      <th>Avg Price</th>
      <th>Total Revenue</th>
      <th>Total Revenue (Normalized)</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>100</td>
      <td>136</td>
      <td>$2.82</td>
      <td>$382.91</td>
      <td>$3.83</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>465</td>
      <td>633</td>
      <td>$2.95</td>
      <td>$1867.68</td>
      <td>$4.02</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>8</td>
      <td>11</td>
      <td>$3.25</td>
      <td>$35.74</td>
      <td>$4.47</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Age Demographics
# find min and max age
min_age = pymo_raw['Age'].min()
max_age = pymo_raw['Age'].max()

print('Youngest Player: ' + str(min_age))
print('Oldest Player: ' + str(max_age))

# define bins limits for age groups
age_bins = [0, 9, 14, 19, 24, 29, 34, 39, 100]

# define bin categories for age groups
age_cat = ['<10', '10-14', '15-19', '20-24',
           '25-29', '30-34', '35-39', '40+']

# assign categories and add to pymoli age dataframe
pymo_age_assign = pd.cut(pymo_raw['Age'], age_bins, labels=age_cat)

# add age groups to pymo_raw DataFrame
pymo_raw['Age Groups'] = pymo_age_assign

# group raw data by Age Groups
pymo_age = pymo_raw.groupby('Age Groups')

# get count of players by gender
pymo_age_count = pymo_age['SN'].nunique()

# calculate count by gender as a percentage of all players
pymo_age_pct = round(pymo_age_count / player_count, 4) * 100

# add count and percentage to summary DataFrame
age_demo = pd.DataFrame({
    'Player Count': pymo_age_count,
    'Percentage of Players': pymo_age_pct
})
age_demo
```

    Youngest Player: 7
    Oldest Player: 45





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
      <th>Player Count</th>
    </tr>
    <tr>
      <th>Age Groups</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>3.32</td>
      <td>19</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>4.01</td>
      <td>23</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>17.45</td>
      <td>100</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>45.20</td>
      <td>259</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>15.18</td>
      <td>87</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>8.20</td>
      <td>47</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>4.71</td>
      <td>27</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>1.92</td>
      <td>11</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Age Purcahase Analysis
# create DataFrame with count mean and sum of Price series by Age Group
purch_analysis_age = pymo_age['Price'].agg(['count', 'mean', 'sum'])

# change DataFrame column names
purch_analysis_age = purch_analysis_age.rename(columns={
    'count': 'Total Purchases',
    'mean': 'Avg Price',
    'sum': 'Total Revenue'
})

# add gender count to gender purchase analysis
purch_analysis_age['Player Count'] = pymo_age_count

# calculate normalized total
normalized_total_age = purch_analysis_age['Total Revenue'] / purch_analysis_age['Player Count']
purch_analysis_age['Total Revenue (Normalized)'] = normalized_total_age

# format columns with currency values
purch_analysis_age['Avg Price'] = purch_analysis_age['Avg Price'].map("${:.2f}".format)
purch_analysis_age['Total Revenue'] = purch_analysis_age['Total Revenue'].map("${:.2f}".format)
purch_analysis_age['Total Revenue (Normalized)'] = purch_analysis_age['Total Revenue (Normalized)'].map("${:.2f}".format)

# rearrange columns
purch_analysis_age = purch_analysis_age[[
    'Player Count', 'Total Purchases',
    'Avg Price', 'Total Revenue',
    'Total Revenue (Normalized)'
]]   

purch_analysis_age
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
      <th>Player Count</th>
      <th>Total Purchases</th>
      <th>Avg Price</th>
      <th>Total Revenue</th>
      <th>Total Revenue (Normalized)</th>
    </tr>
    <tr>
      <th>Age Groups</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>19</td>
      <td>28</td>
      <td>$2.98</td>
      <td>$83.46</td>
      <td>$4.39</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>23</td>
      <td>35</td>
      <td>$2.77</td>
      <td>$96.95</td>
      <td>$4.22</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>100</td>
      <td>133</td>
      <td>$2.91</td>
      <td>$386.42</td>
      <td>$3.86</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>259</td>
      <td>336</td>
      <td>$2.91</td>
      <td>$978.77</td>
      <td>$3.78</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>87</td>
      <td>125</td>
      <td>$2.96</td>
      <td>$370.33</td>
      <td>$4.26</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>47</td>
      <td>64</td>
      <td>$3.08</td>
      <td>$197.25</td>
      <td>$4.20</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>27</td>
      <td>42</td>
      <td>$2.84</td>
      <td>$119.40</td>
      <td>$4.42</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>11</td>
      <td>17</td>
      <td>$3.16</td>
      <td>$53.75</td>
      <td>$4.89</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Top Spender Purchase Analysis
# group raw data by screen name
pymo_user = pymo_raw.groupby('SN')

# create DataFrame with count mean and sum of Price series by screen name
purch_analysis_user = pymo_user['Price'].agg(['count', 'mean', 'sum'])

# change DataFrame column names
purch_analysis_user = purch_analysis_user.rename(columns={
    'count': 'Total Purchases',
    'mean': 'Avg Price',
    'sum': 'Total Revenue'
})

# sort DataFrame values by Total Revenue in descending order 
purch_analysis_user = purch_analysis_user.sort_values(by='Total Revenue', ascending=False)

# reset index keeping old index (SN)
purch_analysis_user = purch_analysis_user.reset_index(drop=False)

# format columns with currecy values
purch_analysis_user['Avg Price'] = purch_analysis_user['Avg Price'].map("${:.2f}".format)
purch_analysis_user['Total Revenue'] = purch_analysis_user['Total Revenue'].map("${:.2f}".format)

# create new DataFrame with 5 top spenders
purch_analysis_top_spend = purch_analysis_user.iloc[:5]

# set SN as index
purch_analysis_top_spend.set_index('SN', inplace=True)

purch_analysis_top_spend
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
      <th>Total Purchases</th>
      <th>Avg Price</th>
      <th>Total Revenue</th>
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




```python
# Most Popular Item Purchase Analysis
# group raw data by Item Name
pymo_item = pymo_raw.groupby(['Item ID', 'Item Name', 'Price'])

# create DataFrame with count mean and sum of Price series by Item Name
purch_analysis_item = pymo_item['Price'].agg(['count', 'sum'])

# change DataFrame column names
purch_analysis_item = purch_analysis_item.rename(columns={
    'count': 'Total Purchases',
    'sum': 'Total Revenue'
})

# sort DataFrame values by Total Purchases in descending order
purch_analysis_most_pop = purch_analysis_item.sort_values(by='Total Purchases', ascending=False)

# create new DataFrame with grouped item data
purch_analysis_most_pop = purch_analysis_most_pop.reset_index(drop=False)

# format columns with currency values
purch_analysis_most_pop['Price'] = purch_analysis_most_pop['Price'].map('${:.2f}'.format)
purch_analysis_most_pop['Total Revenue'] = purch_analysis_most_pop['Total Revenue'].map('${:.2f}'.format)


# select rows of 5 most popular items based on purchase count
purch_analysis_most_pop = purch_analysis_most_pop.iloc[:5]

# set Item ID as index
purch_analysis_most_pop.set_index('Item ID', inplace=True)

purch_analysis_most_pop
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
      <th>Item Name</th>
      <th>Price</th>
      <th>Total Purchases</th>
      <th>Total Revenue</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>39</th>
      <td>Betrayal, Whisper of Grieving Widows</td>
      <td>$2.35</td>
      <td>11</td>
      <td>$25.85</td>
    </tr>
    <tr>
      <th>84</th>
      <td>Arcane Gem</td>
      <td>$2.23</td>
      <td>11</td>
      <td>$24.53</td>
    </tr>
    <tr>
      <th>31</th>
      <td>Trickster</td>
      <td>$2.07</td>
      <td>9</td>
      <td>$18.63</td>
    </tr>
    <tr>
      <th>175</th>
      <td>Woeful Adamantite Claymore</td>
      <td>$1.24</td>
      <td>9</td>
      <td>$11.16</td>
    </tr>
    <tr>
      <th>13</th>
      <td>Serenity</td>
      <td>$1.49</td>
      <td>9</td>
      <td>$13.41</td>
    </tr>
  </tbody>
</table>
</div>




```python
# Most Profitable Item Purchase Analysis
# sort DataFrame by Total Revenue in descending order
purch_analysis_most_profit = purch_analysis_item.sort_values(by='Total Revenue', ascending=False)

# reset index keeping old index values
purch_analysis_most_profit = purch_analysis_most_profit.reset_index(drop=False)

# format columns with currency values
purch_analysis_most_profit['Price'] = purch_analysis_most_profit['Price'].map('${:.2f}'.format)
purch_analysis_most_profit['Total Revenue'] = purch_analysis_most_profit['Total Revenue'].map('${:.2f}'.format)


# select rows of 5 most profitable items
purch_analysis_most_profit = purch_analysis_most_profit.iloc[:5]

# set Item ID as index
purch_analysis_most_profit.set_index('Item ID', inplace=True)

purch_analysis_most_profit
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
      <th>Item Name</th>
      <th>Price</th>
      <th>Total Purchases</th>
      <th>Total Revenue</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>34</th>
      <td>Retribution Axe</td>
      <td>$4.14</td>
      <td>9</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>115</th>
      <td>Spectral Diamond Doomblade</td>
      <td>$4.25</td>
      <td>7</td>
      <td>$29.75</td>
    </tr>
    <tr>
      <th>32</th>
      <td>Orenmir</td>
      <td>$4.95</td>
      <td>6</td>
      <td>$29.70</td>
    </tr>
    <tr>
      <th>103</th>
      <td>Singed Scalpel</td>
      <td>$4.87</td>
      <td>6</td>
      <td>$29.22</td>
    </tr>
    <tr>
      <th>107</th>
      <td>Splitter, Foe Of Subtlety</td>
      <td>$3.61</td>
      <td>8</td>
      <td>$28.88</td>
    </tr>
  </tbody>
</table>
</div>


