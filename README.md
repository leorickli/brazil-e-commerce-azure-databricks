# brazil-e-commerce-azure-databricks

<img width="949" alt="Screenshot 2023-08-19 at 21 42 18" src="https://github.com/leorickli/brazil-e-commerce-azure-databricks/assets/106999054/ce8657dc-5f79-4fea-be6d-6005c1d225cb">

In this project, we will be using some ETL, analytics, and BI tools on Microsoft Azure cloud in the company of Databricks and Power Bi. We will use the [Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce?select=olist_geolocation_dataset.csv) to create a relational database for analysis.

The Microsoft Azure platform was used to create the necessary infrastructure because I believe it offers a better connection with Databricks and Power Bi. The following Azure tools and others were used:

- **Data Factory**: Used for the ingestion of the [raw files](https://github.com/leorickli/brazil-e-commerce-azure-databricks/tree/main/raw_files) that you can find in this repository. It will then send those raw files to a staging area in a data lake inside a Storage Account
- **Data Lake Gen 2**: These will be the staging areas for the data. The first staging area will be the *raw data* that will be ingested by Databricks. The second staging area will be the *transformed data* by Databricks that will be ingested by Azure Synapse.
- **Databricks**: Used for data cleaning and some EDA (Exploratory Data Analysis). This section is important because it will make the correct schema for further data analysis on Synapse.
- **Synapse**: This is the analytics tool offered by Azure. We can use the transformed data to make a relational database model and query data for analytics.
- **Power Bi**: Used for presenting dashboards with the data that is the relational format.

Using a fundamental Data Engineering method called ETL (Extract, Transform, and Load), we will extract the [raw files](https://github.com/leorickli/brazil-e-commerce-azure-databricks/tree/main/raw_files) from this repository, transform it to align with the tight schema constraints of a data warehouse and load it into a data warehouse for analysis and visualizations.

Before we start deploying the resources, it's important to create a unique resource group for this project.

### Extract

Using Data Factory, we establish a *source* (the [raw files](https://github.com/leorickli/brazil-e-commerce-azure-databricks/tree/main/raw_files) from this repository) and the *sink* (the staging area where the data will be sent).

<img width="1134" alt="Screenshot 2023-08-19 at 22 39 52" src="https://github.com/leorickli/brazil-e-commerce-azure-databricks/assets/106999054/b02cc51d-57e5-4c1e-a6fa-790bee954271">

### Transform

After the data has been inserted into the first staging area, we will use Databricks to transform the data through cleaning and EDA. Databricks goes in this process with the use of a [notebook](https://github.com/leorickli/brazil-e-commerce-azure-databricks/blob/main/databricks_notebook.ipynb) that is also linked in this repository.

The majority of the tables were already pretty decent in terms of cleaning, there were just some minor datatype changes for a better schema representation of the columns. There was one exception with the table "order_reviews", there were some problematic rows that were giving error messages when Synapse was trying to read it, those rows had double quotes in them, so I managed to delete all of those rows.

Once the data is transformed, it is then sent to another staging area where it will be ingested by Synapse.

### Load

Data is now loaded into Synapse, we will make this data become relational by creating an ERD so we can query some answers from it.

<img width="1079" alt="Screenshot 2023-08-20 at 11 05 55" src="https://github.com/leorickli/brazil-e-commerce-azure-databricks/assets/106999054/9b22d2cc-22d6-46bd-ad3f-57418430e703">

We can now query some answers using SQL.

```
-- checking the states that have the biggest amount of customers
SELECT [customer_state], COUNT(*) AS [total]
 FROM [ecommerceDB].[dbo].[customers]
 GROUP BY [customer_state]
 ORDER BY [total] DESC
```

<img width="282" alt="Screenshot 2023-08-20 at 11 25 13" src="https://github.com/leorickli/brazil-e-commerce-azure-databricks/assets/106999054/3e47d600-7a97-4f4e-ae22-dfeb07ae1b14">

```
-- on the "order_payments" table, find the average payment of the payment type and the number of times it was used, from top to bottom
SELECT [payment_type], COUNT(*) AS [total], ROUND(AVG([payment_value]),2) AS [average_payment]
 FROM [ecommerceDB].[dbo].[order_payments]
 WHERE [payment_type] != 'not_defined'
 GROUP BY [payment_type]
 ORDER BY [total] DESC
```

<img width="557" alt="Screenshot 2023-08-20 at 11 25 33" src="https://github.com/leorickli/brazil-e-commerce-azure-databricks/assets/106999054/4234513b-250a-4a24-a501-b5bfaa810137">

```
-- for testing the joins: join the "order_paymets", "orders", "order_items", and "products" tables and get the "order_status", "payment_type", average price in descending order, and the "product_category_name"
SELECT TOP (100) o.[order_status], op.[payment_type], AVG(oi.[price]) AS [average_price], p.[product_category_name]
FROM [ecommerceDB].[dbo].[orders] o
JOIN [ecommerceDB].[dbo].[order_payments] op ON o.[order_id] = op.[order_id]
JOIN [ecommerceDB].[dbo].[order_items] oi ON o.[order_id] = oi.[order_id]
JOIN [ecommerceDB].[dbo].[products] p ON oi.[product_id] = p.[product_id]
GROUP BY o.[order_status], op.[payment_type], p.[product_category_name]
ORDER BY [average_price] DESC;
```

<img width="822" alt="Screenshot 2023-08-20 at 11 28 22" src="https://github.com/leorickli/brazil-e-commerce-azure-databricks/assets/106999054/0129d3fb-1407-4fad-aef9-904c2cfb0fb0">

## Dashboards

Before we start making our dashboards, we have to establish a connection between Power Bi and Synapse. To do that, download Power Bi desktop and grab the "Serverless SQL endpoint" inside the resource group that you are using.

![Screenshot 2023-08-20 141429](https://github.com/leorickli/brazil-e-commerce-azure-databricks/assets/106999054/4a261005-b94e-4a6c-a6fd-e1646c3d2af8)

Now that we inserted our credentials in, we can start making some dashboards.

![image](https://github.com/leorickli/brazil-e-commerce-azure-databricks/assets/106999054/bf0e264c-5f16-4f88-97c0-6741755e8435)


