# Answering-Real-World-Data-Analytics-Science-Problems

We start by cleaning our data. Tasks during this section include:

Drop NaN values from DataFrame
Removing rows based on a condition
Change the type of columns (to_numeric, to_datetime, astype)
Once we have cleaned up our data a bit, we move the data exploration section. In this section we explore 5 high level business questions related to our data:

What was the best month for sales? How much was earned that month?
What city sold the most product?
What time should we display advertisemens to maximize the likelihood of customer’s buying product?
What products are most often sold together?
What product sold the most? Why do you think it sold the most?
To answer these questions we walk through many different pandas & matplotlib methods. They include:

Concatenating multiple csvs together to create a new DataFrame (pd.concat)
Adding columns
Parsing cells as strings to make new columns (.str)
Using the .apply() method
Using groupby to perform aggregate analysis
Plotting bar charts and lines graphs to visualize our results
Labeling our graphs


#### Importing important Libraries

```
import pandas as pd 
import pandas as pd 
import matplotlib.pyplot as plt 
import os

import matplotlib.image as mpimg
%matplotlib inline
import seaborn as sns
```
Since we have the data of 12 months of sales data in 12 different files.
So our first task will be to merge the 12 months data into a single file.
The reason why we are doing this in because we want to do some yearly analysis also so it will be easier to to that together compared to doing for 12 files.

The files appear as below:
- Sales_January_2021
- Sales_February_2021
- Sales_March_2021
- 
- 
- 
- Sales_December_2021


 T0 start simple let's just read the single month data
 ```
 df = pd.read_csv('./Sales_Data/Sales_April_2021.csv')
df.head(5)
```
![image](https://user-images.githubusercontent.com/61817305/166199658-b9fef6e9-8a45-4ed5-a792-56e8b25e0f7d.png)

### List comprehension 

```
files = [file for file in os.listdir('./Sales_Data')] # we will get the 12 months data 

for file in files:
    print (file)
Sales_April_2021.csv
Sales_August_2021.csv
Sales_December_2021.csv
Sales_February_2021.csv
Sales_January_2021.csv
Sales_July_2021.csv
Sales_June_2021.csv
Sales_March_2021.csv
Sales_May_2021.csv
Sales_November_2021.csv
Sales_October_2021.csv
Sales_September_2021.csv

```

Now we just need to take the above files and concatnate them into a single CSV

### creating a empty dataframe 

```
all_months_data = pd.DataFrame() 

files = [file for file in os.listdir('./Sales_Data/')]

for file in files:
    df = pd.read_csv('./Sales_Data/' + file)    
    all_months_data = pd.concat([all_months_data, df]) # adding all months data to the dataframe
    
all_months_data.head()
![image](https://user-images.githubusercontent.com/61817305/166199959-6cd004b2-fc5f-4d4d-a6d5-3bd925ae8e42.png)


```
- instead of giving the complete file path we write + file which also gives the file path
   
- './Sales_Data/' is the folder and + file is the name of sales data file for month

### Save all data into a new csv file '12_months_data.csv'
```
all_months_data.to_csv('12_months_data.csv',index=False)

```
### Read the updated data frame 

```
all_data = pd.read_csv('./12_months_data.csv')
all_data.head(5)
```
![image](https://user-images.githubusercontent.com/61817305/166200065-e7833f18-6db0-4ca5-b1c3-8510d307f167.png)

Notice that we have the order date but not exactly the month
So let's add the specific column for the month, which will be helpful in our analysis

## Data cleaning 
### checking rows with nan values 
```
nan_values = all_data[all_data.isna().any(axis=1)]
nan_values
```
![image](https://user-images.githubusercontent.com/61817305/166200114-c1490be8-de61-40a9-a982-c2c21578d9c7.png)

### Drop nan values 
```
all_data = all_data.dropna(how='all')   # dropping all nan values in rows in the dataframe 
all_data.head(5)
```
![image](https://user-images.githubusercontent.com/61817305/166200148-82f3ce2d-542d-4e9d-87d6-28adb11f03f5.png)


* Notice the unique values inside Quantity Ordered column 
* we see there is one unique values as Quantity Ordered
```
all_data['Quantity Ordered'].unique()
```

#### we also notice that there are some records that are missing the values 
#### example  Quantity Ordered has some values as "Quantity Ordered" instead of a number 
```
#all_data [all_data['Quantity Ordered'].str[0:]== 'Quantity Ordered']
#all_data [all_data['Product'].str[0:]== 'Product']
temp_data = all_data [all_data['Order Date'].str[0:]== 'Order Date']
temp_data
# We don't want the below records
```
![image](https://user-images.githubusercontent.com/61817305/166200220-f4548cba-a630-47a1-a1b9-e5ee292128b1.png)

#### excluding the temp_data from all_data dataframe 
```
all_data = all_data[all_data['Order Date'].str[0:] != 'Order Date']   # !=  not equal to 
```
```
all_data.dtypes 
```
![image](https://user-images.githubusercontent.com/61817305/166200258-083a7f0d-7960-4946-8270-6367066458ab.png)

# Convert Columns to correct types
```
all_data['Quantity Ordered'] = pd.to_numeric(all_data['Quantity Ordered']) # make int
all_data['Price Each'] = pd.to_numeric(all_data['Price Each']) # make float
all_data.dtypes 
```

# Question: What was the best month for sales? How much was earned that month?
```
all_data.head(5)
```
![image](https://user-images.githubusercontent.com/61817305/166200288-4310465b-0e1c-47a3-9121-a5e858ea0418.png)

* The first thing that we are going to do is to add an additional column to the data 
 
* Notice: We have the order date but we don't have the month, so we will add a specific column for the month 

### Add data with additional column   
##### Month column
* Take the first two characters in the order date sting and make that the month column 

```
all_data['Month'] = all_data['Order Date'].str[0:2]
                    #convert order data to a string and extract the first two elements which in our case
                    # is the month Order Date 04/19/19 08:46	
        
all_data.head(3)
```
![image](https://user-images.githubusercontent.com/61817305/166200331-a5a18671-479f-4045-a781-ae507810366b.png)

```
# Since the month column in in the form of a stirng, hence converting the it back to integer 

all_data['Month'] = all_data['Month'].astype('int32')  # converting months data to numeric value

```
if we go back to our question. we have got the months separated but we require sales as well, but sales column is missing but we have the Quantity Ordered
and the Price Each which shall give us the sales amount. Sales = quantity * price

```
 all_data['Sales'] = all_data['Quantity Ordered']* all_data['Price Each']
all_data.head(3)
```
![image](https://user-images.githubusercontent.com/61817305/166200384-61698ad2-2d91-4749-beca-fc15deaa9c3d.png)

```
top_month_sale=all_data.groupby('Month').sum()
#top_month_sales = pd.DataFrame(top_month_sale)
top_month_sale['Month']=  range(1,13)
#top_month_sale.set_index('Month', inplace=True)
top_month_sale
```
![image](https://user-images.githubusercontent.com/61817305/166200425-1bc49d8e-571f-4cce-95d9-3beefbd4372f.png)

```
top_month_sale.columns
```

```
sns.set(rc={'figure.figsize':(12,6)})
sns.set_style('whitegrid')  # whitegrid  darkgrid
```

```
fig_1=sns.barplot(x="Month", y="Sales", data=top_month_sale,
                 palette='Blues_d') # Blues_d # flare
fig_1.set_title('Sales by Month')
fig_1.set_ylabel('Sales in USD ($)')
fig_1.set_xlabel('Months')
plt.show()
![image](https://user-images.githubusercontent.com/61817305/166200492-2d3e4a5e-e51c-4030-af72-f2844e47c65f.png)

```
## Question:  What city had the highest number of sales ?

Notice that the City column is missing but we can find the city in the Purchase Address. 
```
all_data.head()
```
We are going to split the Purchase Address by comma ","  return the first index and then add that to the City column.

```
all_data['Purchase Address']
![image](https://user-images.githubusercontent.com/61817305/166200547-69a80b55-279d-42bf-a0e5-d31d995fc5b4.png)

```
* lambda x ( every row in pruchase address) : Split every row in Purchase address by comma and return the first index 
* add that to the new column CITY 
```
#all_data['City'] = all_data['Purchase Address'].apply(lambda x: x.split(',')[1])
#all_data.head(3)
```

```
# Alternate Way

def get_city (address):
    return(address.split(',')[1]) # split the Purchase Address by comma "," return the first index

def get_state(address):
    return(address.split(',')[2]).split(' ')[1] 
# split the Purchase Address by comma "," return the 2 index and split 2 index by whitespace and 
#return second index 

all_data['City'] = all_data['Purchase Address'].apply(lambda x: get_city(x) +' ('+ get_state (x) + ')')



# Example 
# 917 1st St, Dallas, TX 75001     < Purchase Address
# 917 1st St                       < 0 Index
# Dallas                           < 1 Index
# TX 75001                         < 2 Index

###### Return value at first index which is Dallas

# Return 2 Index > TX 75001  >  split by whitespace 
# TX          < 1 index
# 75001       < 2 Index

###### Return first index which is TX

```

```
all_data.head()
```
*  Be careful with the City and city they kinda look the same 

* RUN THE CODE TO SEE THE ISSUE

```
#top_city_sales = all_data.groupby('City').sum()   # print the columns as well City won't be in the columns
#top_city_sales['City']= all_data['City'].unique()  # That's why we are adding it as as column
#top_city_sales
```
* Notice below that when we add the unique city to new column City > The order changes > That won't give us the correct results.
*  TO avoid that we use the below method

```
#top_city_sales = all_data.groupby('City').sum()
# Correct Method to get the cities in order 
#top_city_sales['City']= [City for City, df in all_data.groupby('City')]    # adding the new column
#top_city_sales

```

```
# best way to get the desired results > RESET_INDEX
top_city_sales = all_data.groupby('City').sum().reset_index() 
top_city_sales.columns
```
```
top_city_sales
```
![image](https://user-images.githubusercontent.com/61817305/166198401-29e42361-0886-43ec-bb83-b26a1e6ca9ca.png)

```
fig_1=sns.barplot(x="City", y="Sales", data=top_city_sales,
                 palette='Blues_d') # Blues_d # flare
fig_1.set_title('Sales by Month')
fig_1.set_ylabel('Sales in USD ($)')
fig_1.set_xlabel('Months')
fig_1.set_xticklabels(fig_1.get_xticklabels(), rotation=50)
plt.show()
```
![image](https://user-images.githubusercontent.com/61817305/166198512-4efdd6e6-95d0-4aa5-ad69-e1ea31326bb1.png)

## Question: What time should we display the advertisment to maximize the likelihood of coustomers buying products?

```
all_data.head(5)
```
![image](https://user-images.githubusercontent.com/61817305/166198617-aba809aa-8a0b-4093-9b17-20ade3c90b0d.png)

*  Convert the Order Date data into the date time object 

```
all_data['Order Date'] = pd.to_datetime(all_data['Order Date'])
all_data.head(3)
```
![image](https://user-images.githubusercontent.com/61817305/166198792-e5e5e605-7552-474b-bfe2-572513ff3c67.png)

```
all_data['Hour'] = all_data['Order Date'].dt.hour   
# create a new column hour from the order date by using .dt.hour which is part of datetime object 
all_data['Minute'] = all_data['Order Date'].dt.minute

all_data.head(5)
```
![image](https://user-images.githubusercontent.com/61817305/166198828-fbdb710f-1aad-4b27-9734-17adfa112898.png)

```
hours = [hour for hour, df in all_data.groupby('Hour')]

plt.plot(hours, all_data.groupby(['Hour']).count(), linewidth=2,markersize=8,marker='o',color='gray')
plt.xticks(hours)
plt.xlabel('Hours')
plt.ylabel('Number of Orders')
plt.grid()
plt.show()
```
![image](https://user-images.githubusercontent.com/61817305/166198892-e9bec1ea-9713-49c7-b934-547dddb43e3c.png)
* Recomendation to advertise: 11am (11) or 7pm (19)

## Question: What products are most often sold together?
```
all_data.head(5)
```
![image](https://user-images.githubusercontent.com/61817305/166198990-e293b1df-e9a3-4442-ae55-b13c78c81cbd.png)

* If the Order ID values have the same ID then they must have been ordered together. 
* Counting all the duplicate Order ID's. We will create a new dataframe df >
* We will filter all_data dataframe by Order ID .duplicated. 
* This will check all the rows in the Order ID column and see which ones are duplciated and then 
* we will pass in keep=False will keep all the occurances of duplicate 

```
df=all_data[all_data['Order ID'].duplicated(keep=False)]  # creating new dataframe 
df.head(5)
# keep = False > notice below order ID 176560 is repeated couple of times because we set keep = False
# It keeps all the occurances of duplicates
```
![image](https://user-images.githubusercontent.com/61817305/166199122-d19b7c69-8ece-4300-900a-1418d8b5b746.png)

```
# create new column grouped > This column will have Google Phone and Wired Headphones  on the same line

df['Multiple_Products'] = df.groupby('Order ID')['Product'].transform(lambda x:',' .join(x))

# We groupby df based on order id and specifically looking for in the order id is the product column 
# and then we are transforming wherein it takes in lambda x (all the products under product id)
# and joining them by a comma
df.head(4)
```
![image](https://user-images.githubusercontent.com/61817305/166199170-d3d8fe3e-b99f-4140-a3d6-de0868e94ef1.png)

```
# Since the Order ID's are repeating 
df = df[['Order ID', 'Multiple_Products']].drop_duplicates()
# Get Order ID and Multiple Products column from df and drop the duplicates within them 
df.head(10)

```
![image](https://user-images.githubusercontent.com/61817305/166199204-a6fdc598-557b-433d-9b11-c6ddea9d46a3.png)

```
# count the pairs that are occuring together most frequently 
# using couple of libraires for that

from itertools import combinations
from collections import Counter

count_2 = Counter()   # initiate counter > adds product sold in pairs (2)
count_3 = Counter()   # initiate counter > adds products sold in threes (3)

for row in df['Multiple_Products']:
    row_list = row.split(',')     # split rows by commas 
    count_2.update(Counter(combinations(row_list, 2)))   # 2 indicates the items that are sold in couples 
    count_3.update(Counter(combinations(row_list, 3)))   # we can use 3, 4,5  as well 
```

## Group of Two Products 
```
grp_2 = pd.DataFrame.from_dict(count_2, orient='index').reset_index() # create df from count_2 COUNTER
group_of_2 = grp_2.rename(columns={'index':'Multiple products sold', 0:'count'})  # renaming the cols of df
two_sold = group_of_2.nlargest(15, ['count'])  #getting top 15 results
```

### Grouped products in Two's
```
#figure 
sns.set(rc={'figure.figsize':(12,6)})
sns.set_style('whitegrid')

fig_5=sns.barplot(x="count", y="Multiple products sold", data=two_sold,
                 palette='Blues_d',orient='h') # Blues_d # flare
fig_5.set_title('Products sold together')
fig_5.set_ylabel('Group of Two products') 
fig_5.set_xlabel('Count of multiple products sold')
#fig_1.set_xticklabels(fig_1.get_xticklabels(), rotation=90)
plt.show()
```
![image](https://user-images.githubusercontent.com/61817305/166199328-fa293675-3eb6-4e5d-9abc-50f2ca38c45a.png)

### Group of Three Products 
since some orders had 2, 3, 4 and even 5 products, for now we are getting group of 3

```
grp_3 = pd.DataFrame.from_dict(count_3, orient='index').reset_index() # create df from count_3 COUNTER
group_of_3 = grp_3.rename(columns={'index':'Multiple products sold', 0:'count'}) # renaming the cols of df
three_sold = group_of_3.nlargest(15, ['count'])
```

```
# Grouped products in Threes

sns.set(rc={'figure.figsize':(12,6)})
sns.set_style('whitegrid')

fig_5=sns.barplot(x="count", y="Multiple products sold", data=three_sold,
                 palette='Blues_d',orient='h') # Blues_d # flare
fig_5.set_title('Products sold together')
fig_5.set_ylabel('Group of Three products') 
fig_5.set_xlabel('Count of multiple products sold')
#fig_1.set_xticklabels(fig_1.get_xticklabels(), rotation=90)
plt.show()
```
![image](https://user-images.githubusercontent.com/61817305/166199455-0ea615e9-6d95-4329-b70b-6c65a482cded.png)

## Question: What products sold the most ?
```
all_data.head(5)
```
![image](https://user-images.githubusercontent.com/61817305/166199517-0339d045-9cb5-4a8e-a5f0-95545c9385c7.png)


```
product_group = all_data.groupby('Product')
quantity_ordered = product_group.sum()['Quantity Ordered']
```
```
products_sold = pd.DataFrame(quantity_ordered).reset_index()
top_sold_products = products_sold.nlargest(15,['Quantity Ordered'])
top_sold_products

```
![image](https://user-images.githubusercontent.com/61817305/166199558-6eff4344-d32c-4e2f-b971-0d2407efe126.png)

```
sns.set(rc={'figure.figsize':(12,6)})
sns.set_style('whitegrid')

fig_5=sns.barplot(x="Quantity Ordered", y="Product", data=top_sold_products,
                 palette='Blues_d', orient='h') # Blues_d # flare
fig_5.set_title('Top Selling Products')
fig_5.set_xlabel('Quantity Sold')
fig_5.set_ylabel('Product Name') 
fig_1.set_xticklabels(fig_1.get_xticklabels(), rotation=45)
plt.show()
```
![image](https://user-images.githubusercontent.com/61817305/166199596-372c61b2-f0c8-4903-9a36-b7c8a115bd9d.png)
