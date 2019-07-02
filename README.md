# Mercari-price-prediction
- [Problem Description](#Problem-description)
- [Data and Features](#Data-and-Features)
- [Approach](#Approach)
- [Architecture](#Architecture)
- [Approach](#Approach)


## Problem Description ##

Product pricing gets even harder at scale, considering just how many products are sold online. Clothing has strong seasonal pricing trends and is heavily influenced by brand names, while electronics have fluctuating prices based on product specs.

Mercari, Japan’s biggest community-powered shopping app, knows this problem deeply. They’d like to offer pricing suggestions to sellers, but this is tough because their sellers are enabled to put just about anything, or any bundle of things, on Mercari's marketplace.

In this competition, Mercari’s challenging you to build an algorithm that automatically suggests the right product prices. You’ll be provided user-inputted text descriptions of their products, including details like product category name, brand name, and item condition.


## Data and Features ##
The files consist of a list of product listings. These files are tab-delimited.

1. train_id or test_id - the id of the listing
2. name - the title of the listing. Note that we have cleaned the data to remove text that look like prices (e.g. $20) to avoid leakage. These removed prices are represented as [rm]
3. item_condition_id - the condition of the items provided by the seller
4. category_name - category of the listing
5. brand_name
6. price - the price that the item was sold for. This is the target variable that you will predict. The unit is USD. This column doesn't exist in test.tsv since that is what you will predict.
7. shipping - 1 if shipping fee is paid by seller and 0 by buyer
8. item_description - the full description of the item. Note that we have cleaned the data to remove text that look like prices (e.g. $20) to avoid leakage. These removed prices are represented as [rm]

## Architecture ##

  ![architecture diagram](https://github.com/NehaTamore/mercari-price-prediction/blob/feature/model/mercari_architecture.png)

## Approach ##

- For categorical variables: 
  1. Shipping : Shipping information was present for all the products. And from the EDA it was found that products that tend to be shipped by the seller fall under slightly higher price range than the other products. For modeling shipping is represnted by a binary variable 
  2. Item condition id : The condition id is the categorical variable, taking 1 to 5 values. From the prices perspective it's bit tricky to deduce which id would correspond to most used or unused, as the variation is prices was impacted hugely by the product being sold. condition id could also be represented by one-hot-encoding, yet it's embedded into 5dimensional space so that the network could learn additional information if any. Also the model tends to perform slightly better when embeddings are used for item condition id!
  3. Brand name : Feature imputation technique 
    Brand name is the only feature for which has almost half of the valus missing. The solution to missing values was found with very elegant string matching algorithm run on item name and description. This gave us 0.02 rmsle hike!
  4. Category : Splitting the category adds some value to the model, but time vs rmsle trade off did not allow to implement the same in this model.
- Text features : Text features were embedded into appropriate sized dimensional space and GRUs were used to extract important information from these two features
  1. name : Length of the name variable has some corelation with the price of the product.  
  2. item description : Using item description length does not harm the performance either
  (Future Scope : Using YAKE to find keywords from description could be helpful in this scenario)

- Numerical variable :
  1. price : Major improvement in the model performance could be observed when few transformations were used on price variable. The distribution of target variable was skewed and thus taking log and then using min max scaler increased accuracy by 20%
  
 - There was some duplicate products, but removal of which didn't improve the rmsle score. Thus it is removed from the baseline model
