# brazil-e-commerce-azure-databricks

<img width="949" alt="Screenshot 2023-08-19 at 21 42 18" src="https://github.com/leorickli/brazil-e-commerce-azure-databricks/assets/106999054/ce8657dc-5f79-4fea-be6d-6005c1d225cb">

In this project we will be using some ETL, analytics and BI tools on Microsoft Azure cloud in the company of Databricks and Power Bi. We will use the [Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce?select=olist_geolocation_dataset.csv) to create a relational database for analysis.

The Microsoft Azure platform was used to create the necessary infrastructure because I believe it offers a better connection with Databricks and Power Bi. The following Azure tools and others were used:

- **Data Factory**: Used for the ingestion of the [raw files](https://github.com/leorickli/brazil-e-commerce-azure-databricks/tree/main/raw_files) that you can find in this repository. It will then send those raw files to a staging area in a data lake inside a Storage Account
- **Data Lake Gen 2**: These will be the staging areas for the data. The first staging area will be the raw data that will be ingested by Databricks. The second staging area will be the transformed data by Databricks that will be ingested by Azure Synapse.
- **Databricks**: Used for data cleaning and some EDA (Exploratory Data Analysis). This section is important because it will make the correct schema for further data analysis on Synapse.
- **Synapse**: This is the analytics tool offered by Azure. We can use the transformed data to make a relational database model and query data for analytics.
- **Power Bi**: Used for presenting dashboards with the data that is the relational format.

Using a fundamental processing method called ETL (Extract, Transform and Load), we will extract the [raw files](https://github.com/leorickli/brazil-e-commerce-azure-databricks/tree/main/raw_files) from this repository, transform it to align with the tight schema constraints of a data warehouse and load it into a data warehouse for analysis and visualizations.

Before we start deploying the resources, it's important to create a unique resource group for this project.

### Extract

Using Data Factory, we estabilish a *source* (the [raw files](https://github.com/leorickli/brazil-e-commerce-azure-databricks/tree/main/raw_files) from this repository) and the *sink* (the staging area where the data will be sent).

<img width="1134" alt="Screenshot 2023-08-19 at 22 39 52" src="https://github.com/leorickli/brazil-e-commerce-azure-databricks/assets/106999054/b02cc51d-57e5-4c1e-a6fa-790bee954271">

### Transform

After the data has been inserted into the first staging area, we will use Databricks to transform the data through cleaning and EDA. Databricks goes in this process with the use of notebooks that are also linked in this repository.










