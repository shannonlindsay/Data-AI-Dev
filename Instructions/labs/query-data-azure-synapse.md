---
lab:
    title: 'Query a Data Lake Store using serverless SQL pools in Azure Synapse Analytics'
    module: 'Query data by using Azure Synapse Analytics'
---

# Query a Data Lake Store using serverless SQL pools in Azure Synapse Analytics

In this lab, you will learn how to work with files stored in the data lake and external file sources, through T-SQL statements executed by a serverless SQL pool in Azure Synapse Analytics. You will query Parquet files stored in a data lake, as well as CSV files stored in an external data store. Next, you will create Azure Active Directory security groups and enforce access to files in the data lake through Role-Based Access Control (RBAC) and Access Control Lists (ACLs).

After completing this lab, you will be able to:

- Query Parquet data with serverless SQL pools
- Create external tables for Parquet and CSV files
- Create views with serverless SQL pools

## Lab setup and pre-requisites

Before starting this lab, ensure you have successfully completed the setup steps to create your lab environment.

Understanding data through data exploration is one of the core challenges faced today by data professionals. Depending on the underlying structure of the data as well as the specific requirements of the exploration process, different data processing engines will offer varying degrees of performance, complexity, and flexibility.

In Azure Synapse Analytics, you can use either SQL, Apache Spark for Synapse, or both. Which service you use mostly depends on your personal preference and expertise. When conducting data engineering tasks, both options can be equally valid in many cases. 

In this exercise, you will explore the data lake using both options.

### Task 1: Query sales Parquet data with serverless SQL pools

When you query Parquet files using serverless SQL pools, you can explore the data with T-SQL syntax.

1. Open Synapse Studio (<https://web.azuresynapse.net/>), and then navigate to the **Data** hub.

    ![The Data menu item is highlighted.](images/data-hub.png "Data hub")

2. In the pane on the left, on the **Linked** tab, expand **Azure Data Lake Storage Gen2** and the **asaworkspace*xxxxxx***  primary ADLS Gen2 account, and select the **wwi-02** container
3. In the **sale-small/Year=2019/Quarter=Q4/Month=12/Day=20191231** folder, right-click the **sale-small-20191231-snappy.parquet** file, select **New SQL script**, and then select **Select TOP 100 rows**.

    ![The Data hub is displayed with the options highlighted.](images/data-hub-parquet-select-rows.png "Select TOP 100 rows")

3. Ensure **Built-in** is selected in the **Connect to** dropdown list above the query window, and then run the query. Data is loaded by the serverless SQL endpoint and processed as if was coming from any regular relational database.

    ![The Built-in connection is highlighted.](images/built-in-selected.png "SQL Built-in")

    The cell output shows the query results from the Parquet file.

    ![The cell output is displayed.](images/sql-on-demand-output.png "SQL output")

4. Modify the SQL query to perform aggregates and grouping operations to better understand the data. Replace the query with the following, replacing *SUFFIX* with the unique suffix for your Azure Data Lake store and making sure that the file path in the OPENROWSET function matches the current file path:

    ```sql
    SELECT
        TransactionDate, ProductId,
            CAST(SUM(ProfitAmount) AS decimal(18,2)) AS [(sum) Profit],
            CAST(AVG(ProfitAmount) AS decimal(18,2)) AS [(avg) Profit],
            SUM(Quantity) AS [(sum) Quantity]
    FROM
        OPENROWSET(
            BULK 'https://asadatalakeSUFFIX.dfs.core.windows.net/wwi-02/sale-small/Year=2019/Quarter=Q4/Month=12/Day=20191231/sale-small-20191231-snappy.parquet',
            FORMAT='PARQUET'
        ) AS [r] GROUP BY r.TransactionDate, r.ProductId;
    ```

    ![The T-SQL query above is displayed within the query window.](images/sql-serverless-aggregates.png "Query window")

5. Let's move on from this single file from 2019 and transition to a newer data set. We want to figure out how many records are contained within the Parquet files for all 2019 data. This information is important for planning how we optimize for importing the data into Azure Synapse Analytics. To do this, we'll replace the query with the following (be sure to update the suffix of your data lake in the BULK statement):

    ```sql
    SELECT
        COUNT(*)
    FROM
        OPENROWSET(
            BULK 'https://asadatalakeSUFFIX.dfs.core.windows.net/wwi-02/sale-small/Year=2019/*/*/*/*',
            FORMAT='PARQUET'
        ) AS [r];
    ```

    > Notice how we updated the path to include all Parquet files in all subfolders of *sale-small/Year=2019*.

    The output should be **4124857** records.

### Task 2: Create an external table for 2019 sales data

Rather than creating a script with OPENROWSET and a path to the root 2019 folder every time we want to query the Parquet files, we can create an external table.

1. In Synapse Studio, return to the **wwi-02** tab, which should still be showing the contents of the *sale-small/Year=2019/Quarter=Q4/Month=12/Day=20191231* folder.
2. Right-click the **sale-small-20191231-snappy.parquet** file, select **New SQL script**, and then select **Create external table**. In the New external table dialog box, click **Continue**.
3. Make sure **Built-in** is selected for the **SQL pool**. Then, under **Select a database**, select **+ New** and create a database named `demo`, and click **Create**. For **External table name**, enter `All2019Sales`. Finally, under **Create external table**, ensure **Using SQL script** is selected, and then select **Open Script** to generate the SQL script.

    ![The create external table form is displayed.](images/create-external-table-form.png "Create external table")

    > **Note**: The **Properties** pane for the script open automatically. You can close it by using the **Properties** button above it on the right to make it easier to work with the script.

    The generated script contains the following components:

    - **1)** The script begins with creating the *SynapseParquetFormat* external file format with a *FORMAT_TYPE* of *PARQUET*.
    - **2)** Next, the external data source is created, pointing to the *wwi-02* container of the data lake storage account.
    - **3)** The CREATE EXTERNAL TABLE WITH statement specifies the file location and refers to the new external file format and data source created above.
    - **4)** Finally, we select the top 100 results from the `2019Sales` external table.
    
4. In the CREATE EXTERNAL TABLE statement, in the **[TransactionId] varchar(8000)** line, change 8000 to 4000 and add `COLLATE Latin1_General_100_BIN2_UTF8`; and replace the *LOCATION* value with `sale-small/Year=2019/*/*/*/*.parquet` so that the statement becomes similar to the following (except with your unique resource SUFFIX):

```sql
CREATE EXTERNAL TABLE All2019Sales (
    [TransactionId] varchar(4000) COLLATE Latin1_General_100_BIN2_UTF8,
    [CustomerId] int,
    [ProductId] smallint,
    [Quantity] smallint,
    [Price] numeric(38,18),
    [TotalAmount] numeric(38,18),  
    [TransactionDate] int,
    [ProfitAmount] numeric(38,18),
    [Hour] smallint,
    [Minute] smallint,
    [StoreId] smallint
    )
    WITH (
    LOCATION = 'sale-small/Year=2019/*/*/*/*.parquet',
    DATA_SOURCE = [wwi-02_asadatalakeSUFFIX_dfs_core_windows_net],
    FILE_FORMAT = [SynapseParquetFormat]
    )
GO
```

5. Make sure the script is connected to the serverless SQL pool (**Built-in**) and that the **demo** database is selected in the **Use database** list (use the **...** button to see the list if the pane is too small to display it, and then use the &#8635; button to refresh the list if needed).

    ![The Built-in pool and demo database are selected.](images/built-in-and-demo.png "Script toolbar")

6. Run the modified script.

    After running the script, we can see the output of the SELECT query against the **All2019Sales** external table. This displays the first 100 records from the Parquet files located in the *YEAR=2019* folder.

    ![The query output is displayed.](images/create-external-table-output.png "Query output")

    > **Tip**: If an error occurs because of a mistake in your code, you should delete any resources that were successfully created before trying again. You can do this by running the appropriate DROP statements, or by switching to the **Workspace** tab, refreshing the list of **Databases**, and deleting the objects in the **demo** database.

### Task 3: Create an external table for CSV files

Tailwind Traders found an open data source for country population data that they want to use. They do not want to merely copy the data since it is regularly updated with projected populations in future years.

You decide to create an external table that connects to the external data source.

1. Replace the SQL script you ran in the previous task with the following code:

    ```sql
    IF NOT EXISTS (SELECT * FROM sys.symmetric_keys) BEGIN
        declare @pasword nvarchar(400) = CAST(newid() as VARCHAR(400));
        EXEC('CREATE MASTER KEY ENCRYPTION BY PASSWORD = ''' + @pasword + '''')
    END

    CREATE DATABASE SCOPED CREDENTIAL [sqlondemand]
    WITH IDENTITY='SHARED ACCESS SIGNATURE',  
    SECRET = 'sv=2018-03-28&ss=bf&srt=sco&sp=rl&st=2019-10-14T12%3A10%3A25Z&se=2061-12-31T12%3A10%3A00Z&sig=KlSU2ullCscyTS0An0nozEpo4tO5JAgGBvw%2FJX2lguw%3D'
    GO

    -- Create external data source secured using credential
    CREATE EXTERNAL DATA SOURCE SqlOnDemandDemo WITH (
        LOCATION = 'https://sqlondemandstorage.blob.core.windows.net',
        CREDENTIAL = sqlondemand
    );
    GO

    CREATE EXTERNAL FILE FORMAT QuotedCsvWithHeader
    WITH (  
        FORMAT_TYPE = DELIMITEDTEXT,
        FORMAT_OPTIONS (
            FIELD_TERMINATOR = ',',
            STRING_DELIMITER = '"',
            FIRST_ROW = 2
        )
    );
    GO

    CREATE EXTERNAL TABLE [population]
    (
        [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
        [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
        [year] smallint,
        [population] bigint
    )
    WITH (
        LOCATION = 'csv/population/population.csv',
        DATA_SOURCE = SqlOnDemandDemo,
        FILE_FORMAT = QuotedCsvWithHeader
    );
    GO
    ```

    At the top of the script, we create a MASTER KEY with a random password. Next, we create a database-scoped credential for the containers in the external storage account using a shared access signature (SAS) for delegated access. This credential is used when we create the **SqlOnDemandDemo** external data source that points to the location of the external storage account that contains the population data:

    ![The script is displayed.](images/script1.png "Create master key and credential")

    > Database-scoped credentials are used when any principal calls the OPENROWSET function with a DATA_SOURCE or selects data from an external table that doesn't access public files. The database scoped credential doesn't need to match the name of storage account because it will be explicitly used in the DATA SOURCE that defines the storage location.

    In the next part of the script, we create an external file format called **QuotedCsvWithHeader**. Creating an external file format is a prerequisite for creating an External Table. By creating an External File Format, you specify the actual layout of the data referenced by an external table. Here we specify the CSV field terminator, string delimiter, and set the FIRST_ROW value to 2 since the file contains a header row:

    ![The script is displayed.](images/script2.png "Create external file format")

    Finally, at the bottom of the script, we create an external table named **population**. The WITH clause specifies the relative location of the CSV file, points to the data source created above, as well as the *QuotedCsvWithHeader* file format:

    ![The script is displayed.](images/script3.png "Create external table")

2. Run the script.

    Note that there are no data results for this query.

3. Replace the SQL script with the following to select from the population external table, filtered by 2019 data where the population is greater than 100 million:

    ```sql
    SELECT [country_code]
        ,[country_name]
        ,[year]
        ,[population]
    FROM [dbo].[population]
    WHERE [year] = 2019 and population > 100000000
    ```

4. Run the script.
5. In the query results, select the **Chart** view, then configure it as follows:

    - **Chart type**: Bar
    - **Category column**: country_name`
    - **Legend (series) columns**: population
    - **Legend position**: bottom - center

    ![The chart is displayed.](images/population-chart.png "Population chart")

### Task 4: Create a view with a serverless SQL pool

Let's create a view to wrap a SQL query. Views allow you to reuse queries and are needed if you want to use tools, such as Power BI, in conjunction with serverless SQL pools.

1. In the **Data** hub, on the **Linked** tab, in the **Azure Data Lake Storage Gen2/asaworkspace*xxxxxx*/ wwi-02** container, navigate to the **customer-info** folder. Then right-click the **customerinfo.csv** file, select **New SQL script**, and then **Select TOP 100 rows**.

    ![The Data hub is displayed with the options highlighted.](images/customerinfo-select-rows.png "Select TOP 100 rows")

3. Select **Run** to execute the script, and notice that the first row of the CSV file is the column header row. The columns in the resultset are named **C1**, **C2**, and so on.

    ![The CSV results are displayed.](images/select-customerinfo.png "customerinfo.csv file")

4. Update the script with the following code and **make sure you replace SUFFIX** in the OPENROWSET BULK path with your unique resource suffix.

    ```sql
    CREATE VIEW CustomerInfo AS
        SELECT * 
    FROM OPENROWSET(
            BULK 'https://asadatalakeSUFFIX.dfs.core.windows.net/wwi-02/customer-info/customerinfo.csv',
            FORMAT = 'CSV',
            PARSER_VERSION='2.0',
            FIRSTROW=2
        )
        WITH (
        [UserName] NVARCHAR (50),
        [Gender] NVARCHAR (10),
        [Phone] NVARCHAR (50),
        [Email] NVARCHAR (100),
        [CreditCard] NVARCHAR (50)
        ) AS [r];
        GO

    SELECT * FROM CustomerInfo;
    GO
    ```

    ![The script is displayed.](images/create-view-script.png "Create view script")

5. In the **Use database** list, ensure **demo** is still selected, and then run the script.

    We just created the view to wrap the SQL query that selects data from the CSV file, then selected rows from the view:

    ![The query results are displayed.](images/create-view-script-results.png "Query results")

    Notice that the first row no longer contains the column headers. This is because we used the FIRSTROW=2 setting in the OPENROWSET statement when we created the view.

6. In the **Data** hub, select the **Workspace** tab. Then select the actions ellipses **(...)** to the right of the Databases group and select **&#8635; Refresh**. If the workspace is blank, then refresh the browser page.

    ![The refresh button is highlighted.](images/refresh-databases.png "Refresh databases")

7. Expand the **demo** SQL database.

    ![The demo database is displayed.](images/demo-database.png "Demo database")

    The database contains the following objects that we created in our earlier steps:

    - **1) External tables**: *All2019Sales* and *population*.
    - **2) External data sources**: *SqlOnDemandDemo* and *wwi-02_asadatalakeinadayXXX_dfs_core_windows_net*.
    - **3) External file formats**: *QuotedCsvWithHeader* and *SynapseParquetFormat*.
    - **4) Views**: *CustomerInfo*
