# Use Case 5- Build a Sales and Geography Data Warehouse for Contoso in Microsoft Fabric

**Introduction**

Contoso, a multinational retail company, is looking to modernize its
data infrastructure to improve sales and geographical analysis.
Currently, their sales and customer data is scattered across multiple
systems, making it difficult for their business analysts and citizen
developers to derive insights. The company plans to consolidate this
data into a unified platform using Microsoft Fabric to enable
cross-querying, sales analysis, and geographical reporting.

In this lab, you’ll assume the role of a data engineer at Contoso tasked
with designing and implementing a data warehouse solution using
Microsoft Fabric. You will start by setting up a Fabric workspace,
creating a data warehouse, loading data from Azure Blob Storage, and
performing analytical tasks to deliver insights to Contoso's
decision-makers.

While many concepts in Microsoft Fabric may be familiar to data and
analytics professionals, it can be challenging to apply those concepts
in a new environment. This lab has been designed to walk step-by-step
through an end-to-end scenario from data acquisition to data consumption
to build a basic understanding of the Microsoft Fabric user experience,
the various experiences and their integration points, and the Microsoft
Fabric professional and citizen developer experiences.

**Objectives**

- Set up a Fabric workspace with trial enabled.

- Establish a new Warehouse named WideWorldImporters in Microsoft
  Fabric.

- Load data into the Warehouse_Fabric28 workspace using a Data Factory
  pipeline.

- Generate dimension_city and fact_sale tables within the data
  warehouse.

- Populate dimension_city and fact_sale tables with data from Azure Blob
  Storage.

- Create clones of dimension_city and fact_sale tables in the Warehouse.

- Develop a stored procedure to transform data and create
  aggregate_sale_by_date_city table.

- Generate a query using the visual query builder to merge and aggregate
  data.

- Use a notebook to query and analyze data from the dimension_customer
  table.

- Include WideWorldImporters and ShortcutExercise warehouses for
  cross-querying.

- Execute a T-SQL query across WideWorldImporters and ShortcutExercise
  warehouses.

- Generate column chart, map, and table visuals for Sales Analysis
  report.

- Create a report using data from the WideWorldImporters dataset in the
  OneLake data hub.

- Remove the workspace and its associated items.

# **Exercise 1:** **Create a Microsoft Fabric workspace**

## Task 1: Create a workspace

Before working with data in Fabric, create a workspace with the Fabric
trial enabled.

1.  In the Workspaces pane Select **+** **New
    workspace**.

    ![](./media/image1.png)

2.  In the **Create a workspace tab, enter** the following details and
    click on the **Apply** button.

    |   |    |
    |-----|-----|
    |Workspace Name|	Enter a unique name. for example – +++Warehouse_Fabric28+++|
    |Description	|This workspace contains all the artifacts for the data warehouse|
    |Advanced	|Under License mode, select Trial|
    |Default storage format	|Small semantic model storage format|

    ![A screenshot of a computer Description automatically
    generated](./media/image2.png)

    ![](./media/image3.png)

    ![](./media/image4.png)

3.  Wait for the deployment to complete. It takes 2-3 minutes to
    complete. When your new workspace opens, it should be empty.

    ![A screenshot of a computer Description automatically
    generated](./media/image5.png)

## Task 2: Create a Warehouse in Microsoft Fabric

1.  In the **Workspace** page, select **+New item** and click on
    **Warehouse**.

    ![A screenshot of a computer Description automatically
    generated](./media/image6.png)

2.  On the **New warehouse** dialog,
    enter +++**WideWorldImporters+++** and click on the **Create**
    button.

    ![A screenshot of a computer Description automatically
    generated](./media/image7.png)

3.  When provisioning is complete,
    the **WideWorldImporters** warehouse landing page appears.

    ![](./media/image8.png)

# **Exercise 2: Ingest data into a Warehouse in Microsoft Fabric**

## Task 1: Ingest data into a Warehouse

1.  From the **WideWorldImporters** warehouse landing page,
    select **Warehouse_Fabric28** in the left-sided navigation menu to
    return to the workspace item list.

    ![A screenshot of a computer Description automatically
    generated](./media/image9.png)

2.  In the **Warehouse_Fabric28** page, select +**New item**. Then,
    click Data pipeline to view the full list of available items under
    Get data.

    ![A screenshot of a computer Description automatically
    generated](./media/image10.png)

    ![A screenshot of a computer Description automatically
    generated](./media/image11.png)

3.  On the **New** **pipeline** dialog box, in
    the **Name** field, enter +++Load Customer Data+++ and click on
    the **Create** button.

    ![](./media/image12.png)

4.  You’ll be navigated to **Start building your data pipeline** section
    and click on **Pipeline activity**.

    ![](./media/image13.png)

5.  Select **Copy data** under **Move &** **transform** section.

    ![A screenshot of a computer Description automatically
    generated](./media/image14.png)

6.  Select the newly created **Copy data** activity from the
    design canvas to configure it.

    **Note**: Drag the horizonal line in the design canvas to have a
    complete view of various features.

    ![A screenshot of a computer Description automatically
    generated](./media/image15.png)

7.  On the **General** tab, in the **Name** field**,** enter +++CD
    Load dimension_customer+++ .

    ![A screenshot of a computer Description automatically
    generated](./media/image16.png)

8.  On the **Source** page, select the **Connection** dropdown.
    Select **More** to see all of the data sources you can choose from,
    including data sources in your local OneLake data hub.

    ![](./media/image17.png)

9.  On the **Get data** window, search **Azure Blob Storage** in, then
    click on the **Azure Blob Storage** button.

    ![A screenshot of a computer Description automatically
    generated](./media/image18.png)

10. On the **Connection settings** pane that appears on the right side,
    configure the following settings and click on the **Connect**
    button.

    - In the **Account name or URL**, enter
    +++https://fabrictutorialdata.blob.core.windows.net/sampledata/+++

    - In the **Connection credentials** section, click on the dropdown under
    **Connection**, then select **Create new connection**.

    - In **Connection name** field**,** enter +++**Wide World Importers
    Public Sample+++**.

    - Set the **Authentication kind** to **Anonymous**.

    ![](./media/image19.png)

11. In the **File path** text boxes, provide:

    - **Container:** +++sampledata+++

    - **File path - Directory:** +++WideWorldImportersDW/tables+++

    - **File path - File name:** +++dimension_customer.parquet+++

    - In the **File format** drop down, choose **Parquet** (if you are
        unable to see **Parquet**, then type in the search box and then
        select it)

    ![](./media/image20.png)

13. Click on **Preview data** on the right side
    of **File path** setting to ensure that there are no errors and then
    click on **close.**

    ![](./media/image21.png)

    ![A screenshot of a computer Description automatically
    generated](./media/image22.png)

14. On the **Destination** tab, enter the following settings.

    |   |    |
    |-----|-----|
    |Connection|	select WideWorldImporters from the dropdown|
    |Table option	|select the Auto create table radio button|
    |Table	|In the first box enter +++dbo+++ and In the second box enter +++dimension_customer+++|

    ![](./media/image23.png)

15. From the ribbon, select **Run**.

    ![](./media/image24.png)

16. In the **Save and run?** dialog box, click on **Save and run**
    button.

    ![](./media/image25.png)

17. Monitor the copy activity's progress on the **Output** page and wait
    for it to complete.

    ![A screenshot of a computer Description automatically
    generated](./media/image26.png)

18. To monitor the progress of the copy activity, review the pipeline
    run activities in the **Output** page (wait for it to complete with
    a **Succeeded** status).

    ![A screenshot of a computer Description automatically
    generated](./media/image27.png)

# **Exercise 3: Create tables in a Data Warehouse**

## Task 1: Create a table in a Warehouse using Script Activity in Data Pipeline

1.  Search for **Script** Activity in the pipeline Activities ribbon at
    the top and select it to add it to the pipeline canvas.

    ![A screenshot of a computer Description automatically
    generated](./media/image28.png)

2.  You’ll see there are two tabs – **General** and **Settings** tab.
    Keep the **General** settings as it is and select **Settings** tab
    to start configuring it.

    ![A screenshot of a computer Description automatically
    generated](./media/image29.png)

3.  Choose your connection and select the warehouse that you have
    created **WorldWideImporters** from the dropdown.

    ![A screenshot of a computer Description automatically
    generated](./media/image30.png)

4.  You can choose either **Query** to get a data result
    or **NonQuery** for any catalog operations. Here, as you’re going to
    insert a table, that will go in **NonQuery** Script.

    **Note: Database statements that perform catalog operations (for
    example, querying the structure of a database or creating database
    objects such as tables), or change the data in a database by executing
    UPDATE, INSERT, or DELETE statements.**

    ![A screenshot of a computer Description automatically
    generated](./media/image31.png)

5.  Click on the **pencil** icon to edit/add the script in code editor.

    ![](./media/image32.png)

6.  Paste the following code in the code editor and select **OK**.

    ```
    /*
    Drop the dimension_city table if it already exists.
    Create the dimension_city table.
    Drop the fact_sale table if it already exists.
    Create the fact_sale table.
    */

    --dimension_city
    DROP TABLE IF EXISTS [dbo].[dimension_city];
    CREATE TABLE [dbo].[dimension_city]
    (
        [CityKey] [int] NULL,
        [WWICityID] [int] NULL,
        [City] [varchar](8000) NULL,
        [StateProvince] [varchar](8000) NULL,
        [Country] [varchar](8000) NULL,
        [Continent] [varchar](8000) NULL,
        [SalesTerritory] [varchar](8000) NULL,
        [Region] [varchar](8000) NULL,
        [Subregion] [varchar](8000) NULL,
        [Location] [varchar](8000) NULL,
        [LatestRecordedPopulation] [bigint] NULL,
        [ValidFrom] [datetime2](6) NULL,
        [ValidTo] [datetime2](6) NULL,
        [LineageKey] [int] NULL
    );

    --fact_sale
    DROP TABLE IF EXISTS [dbo].[fact_sale];
    CREATE TABLE [dbo].[fact_sale]
    (
        [SaleKey] [bigint] NULL,
        [CityKey] [int] NULL,
        [CustomerKey] [int] NULL,
        [BillToCustomerKey] [int] NULL,
        [StockItemKey] [int] NULL,
        [InvoiceDateKey] [datetime2](6) NULL,
        [DeliveryDateKey] [datetime2](6) NULL,
        [SalespersonKey] [int] NULL,
        [WWIInvoiceID] [int] NULL,
        [Description] [varchar](8000) NULL,
        [Package] [varchar](8000) NULL,
        [Quantity] [int] NULL,
        [UnitPrice] [decimal](18, 2) NULL,
        [TaxRate] [decimal](18, 3) NULL,
        [TotalExcludingTax] [decimal](29, 2) NULL,
        [TaxAmount] [decimal](38, 6) NULL,
        [Profit] [decimal](18, 2) NULL,
        [TotalIncludingTax] [decimal](38, 6) NULL,
        [TotalDryItems] [int] NULL,
        [TotalChillerItems] [int] NULL,
        [LineageKey] [int] NULL,
        [Month] [int] NULL,
        [Year] [int] NULL,
        [Quarter] [int] NULL
    );
    ```

    ![A screenshot of a computer Description automatically
    generated](./media/image33.png)

7.  Query is now added in the script textbox.

    ![A screenshot of a computer Description automatically
    generated](./media/image34.png)

8.  Click on **Run** button from the Home tab to the run the pipeline.

    ![A screenshot of a computer Description automatically
    generated](./media/image35.png)

9.  Select **Save and run** button.

    ![A screenshot of a computer Description automatically
    generated](./media/image36.png)

10. Script1 Activity status is showing **succeeded**.

    ![A screenshot of a computer Description automatically
    generated](./media/image37.png)

11. To check if this script activity worked fine and if it created
    tables as per the query, navigate to **warehouse
    (WorldWideImporters)** through left pane.

    ![A screenshot of a computer Description automatically
    generated](./media/image38.png)

12. **Validate** the table was created successfully by selecting the
    **refresh** icon button on the ribbon.

    ![A screenshot of a computer Description automatically
    generated](./media/image39.png)

13. In the **Explorer** pane, expand **Schemas**, then **dbo**, then
    **Tables**, the **fact_sale** table and **dimension_city** table
    under the **dbo** schema is successfully created using the script
    activity in pipeline.

    ![A screenshot of a computer Description automatically
    generated](./media/image40.png)

## Task 2: Load data using T-SQL in Warehouse

Now that you know how to build a data warehouse, load a table, and
generate a report, it's time to extend the solution by exploring other
methods for loading data.

1.  On the Warehouse -
    **WideWorldImporters** page, go to the **Home** tab, select **SQL** from the dropdown, and click on **New SQL query**.

    ![](./media/image41.png)

2.  In the query editor, **paste** the following code, then click on
    **Run** to execute the query.

     ```
    --Copy data from the public Azure storage account to the dimension_city table.
    COPY INTO [dbo].[dimension_city]
    FROM 'https://fabrictutorialdata.blob.core.windows.net/sampledata/WideWorldImportersDW/tables/dimension_city.parquet'
    WITH (FILE_TYPE = 'PARQUET');

    --Copy data from the public Azure storage account to the fact_sale table.
    COPY INTO [dbo].[fact_sale]
    FROM 'https://fabrictutorialdata.blob.core.windows.net/sampledata/WideWorldImportersDW/tables/fact_sale.parquet'
    WITH (FILE_TYPE = 'PARQUET');
    ```

    ![](./media/image42.png) 

3.  After the query is completed, review the
    messages, which indicates the number of rows that were loaded into
    the **dimension_city** and **fact_sale** tables respectively.

    ![](./media/image43.png)

4.  Load the data preview to validate the data loaded successfully by
    selecting on the **fact_sale** table in the **Explorer**.

    ![A screenshot of a computer Description automatically
    generated](./media/image44.png)

5.  Rename the query. Right-click on **SQL query 1** in
    the **Explorer**, then select **Rename**.

    ![A screenshot of a computer Description automatically
    generated](./media/image45.png)

6.  In the **Rename** dialog box, under the **Name** field, enter
    +++**Load Tables+++**. Then, click on **Rename** button.

    ![A screenshot of a computer Description automatically
    generated](./media/image46.png)

7.  Click on the **Refresh** icon in the command bar below the **Home**
    tab.

    ![A screenshot of a computer Description automatically
    generated](./media/image47.png)

# **Exercise 4: Clone a table using T-SQL in Microsoft Fabric**

## Task 1: Create a table clone within the same schema in a warehouse

This task guides you through creating a table clone in Warehouse in Microsoft Fabric, using the CREATE TABLE AS CLONE OF T-SQL syntax.

1.  Create a table clone within the same schema in a warehouse.

2.  On the **WideWorldImporters** page, go to the **Home** tab, select **SQL** from the dropdown, and click on **New SQL query**.

    ![A screenshot of a computer Description automatically
    generated](./media/image48.png)

3.  In the query editor, paste the following code to create clones of
    the **dbo.dimension_city** and **dbo.fact_sale** tables.

     ```
    --Create a clone of the dbo.dimension_city table.
    CREATE TABLE [dbo].[dimension_city1] AS CLONE OF [dbo].[dimension_city];

    --Create a clone of the dbo.fact_sale table.
    CREATE TABLE [dbo].[fact_sale1] AS CLONE OF [dbo].[fact_sale];
    ```

    ![](./media/image49.png)

4.  Select **Run** to execute the query. The
    query takes a few seconds to execute. After the query is completed,
    the table clones **dimension_city1** and **fact_sale1** will be
    created.

    ![](./media/image50.png)

    ![A screenshot of a computer Description automatically
    generated](./media/image51.png)

5.  Load the data preview to validate the data loaded successfully by
    selecting on the **dimension_city1** table in the **Explorer**.

    ![A screenshot of a computer Description automatically
    generated](./media/image52.png)

6.  Right-click on **SQL query** that you’ve created to clone the
    tables in the **Explorer** and select **Rename**.

    ![A screenshot of a computer Description automatically
    generated](./media/image53.png)

7.  In the **Rename** dialog box, under the
    **Name** field, enter +++**Clone Table+++**, then click on the
    **Rename** button.

    ![](./media/image54.png)

8.  Click on the **Refresh** icon in the command bar below the **Home**
    tab.

    ![A screenshot of a computer Description automatically
    generated](./media/image55.png)

## Task 2: Create a table clone across schemas within the same warehouse

1.  On the **WideWorldImporters** page, go to the **Home** tab, select **SQL** from the dropdown, and click on **New SQL query**.

    ![A screenshot of a computer Description automatically
    generated](./media/image48.png)

2.  Create a new schema within the **WideWorldImporter** warehouse
    named **dbo1**. **Copy paste** and **run** the following T-SQL code
    as shown in the below image:

    ```
    CREATE SCHEMA dbo1;
    ```

    ![](./media/image56.png)

3.  In the query editor, remove the existing code and paste the following to create clones of the **dbo.dimension_city** and dbo.**fact_sale tables** in the **dbo1** schema.

    ```
    --Create a clone of the dbo.dimension_city table in the dbo1 schema.
    CREATE TABLE [dbo1].[dimension_city1] AS CLONE OF [dbo].[dimension_city];

    --Create a clone of the dbo.fact_sale table in the dbo1 schema.
    CREATE TABLE [dbo1].[fact_sale1] AS CLONE OF [dbo].[fact_sale];
    ```

    ![](./media/image57.png)

4.  Select **Run** to execute the query. The query takes a few seconds
    to execute.

    ![](./media/image58.png)

5.  After the query is completed,
    clones **dimension_city1** and **fact_sale1** are created in
    the **dbo1** schema.

    ![](./media/image59.png)

6.  Load the data preview to validate the data loaded successfully by
    selecting on the **dimension_city1** table under **dbo1** schema in
    the **Explorer**.

    ![](./media/image60.png)

7.  **To rename this query,** right-click on **SQL query 1** in the
    **Explorer** and select **Rename**.

    ![A screenshot of a computer Description automatically
    generated](./media/image61.png)

8.  In the **Rename** dialog box, under the
    **Name** field, enter +++**Clone Table in another schema+++**. Then,
    click on **Rename** button.

    ![](./media/image62.png)

9.  Click on the **Refresh** icon in the command bar below the **Home**
    tab.

    ![A screenshot of a computer Description automatically
    generated](./media/image63.png)

# **Exercise 5: Transform data using a stored procedure**

Learn how to create and save a new stored procedure to transform data.

1.  On the **WideWorldImporters** page, go to the **Home** tab, select **SQL** from the dropdown, and click on **New SQL query**.

    ![](./media/image64.png)

2.  In the query editor, **paste** the following code to create the
    stored procedure **dbo.populate_aggregate_sale_by_city**. This
    stored procedure will create and load
    the **dbo.aggregate_sale_by_date_city** table in a later step.

    ```
    --Drop the stored procedure if it already exists.
    DROP PROCEDURE IF EXISTS [dbo].[populate_aggregate_sale_by_city]
    GO

    --Create the populate_aggregate_sale_by_city stored procedure.
    CREATE PROCEDURE [dbo].[populate_aggregate_sale_by_city]
    AS
    BEGIN
    --If the aggregate table already exists, drop it. Then create the table.
    DROP TABLE IF EXISTS [dbo].[aggregate_sale_by_date_city];
    CREATE TABLE [dbo].[aggregate_sale_by_date_city]
        (
            [Date] [DATETIME2](6),
            [City] [VARCHAR](8000),
            [StateProvince] [VARCHAR](8000),
            [SalesTerritory] [VARCHAR](8000),
            [SumOfTotalExcludingTax] [DECIMAL](38,2),
            [SumOfTaxAmount] [DECIMAL](38,6),
            [SumOfTotalIncludingTax] [DECIMAL](38,6),
            [SumOfProfit] [DECIMAL](38,2)
        );

    --Reload the aggregated dataset to the table.
    INSERT INTO [dbo].[aggregate_sale_by_date_city]
    SELECT
        FS.[InvoiceDateKey] AS [Date], 
        DC.[City], 
        DC.[StateProvince], 
        DC.[SalesTerritory], 
        SUM(FS.[TotalExcludingTax]) AS [SumOfTotalExcludingTax], 
        SUM(FS.[TaxAmount]) AS [SumOfTaxAmount], 
        SUM(FS.[TotalIncludingTax]) AS [SumOfTotalIncludingTax], 
        SUM(FS.[Profit]) AS [SumOfProfit]
    FROM [dbo].[fact_sale] AS FS
    INNER JOIN [dbo].[dimension_city] AS DC
        ON FS.[CityKey] = DC.[CityKey]
    GROUP BY
        FS.[InvoiceDateKey],
        DC.[City], 
        DC.[StateProvince], 
        DC.[SalesTerritory]
    ORDER BY 
        FS.[InvoiceDateKey], 
        DC.[StateProvince], 
        DC.[City];
    END;
    ```
    ![](./media/image65.png)

3.  To execute the query, on the query designer
    ribbon, select **Run**.

    ![](./media/image66.png)

4.  Right-click on SQL query that you’ve created
    to clone the tables in the Explorer and select **Rename**.

    ![](./media/image67.png)

5.  In the **Rename** dialog box, under the
    **Name** field, enter +++**Create Aggregate Procedure+++**, then
    click on the **Rename** button.

    ![](./media/image68.png)

6.  Click on the **Refresh icon** below the
    **Home** tab.

    ![](./media/image69.png)

7.  In the **Explorer** tab, verify that you can see the newly created
    stored procedure by expanding the **StoredProcedures** node under
    the **dbo** schema.

    ![A screenshot of a computer Description automatically
    generated](./media/image70.png)

8.  On the **WideWorldImporters** page, go to the **Home** tab, select **SQL** from the dropdown, and click on **New SQL query**.

    ![](./media/image64.png)

9.  In the query editor, paste the following code. This T-SQL executes
    **dbo.populate_aggregate_sale_by_city** to create the
    **dbo.aggregate_sale_by_date_city** table. Run the query:

    ```
    --Execute the stored procedure to create the aggregate table.
    EXEC [dbo].[populate_aggregate_sale_by_city];
    ```

    ![](./media/image71.png)

10. To save this query for reference later, right-click on the query tab
    just above the editor and select **Rename.**

    ![](./media/image72.png)

11. In the **Rename** dialog box, under the **Name** field, enter
    +++Create Aggregate Procedure+++, then click on the
    **Rename** button.

    ![](./media/image73.png)

12. Select the **Refresh** icon on the ribbon.

    ![](./media/image74.png)

13. In the Object **Explorer** tab, load the data preview to validate
    the data loaded successfully by selecting on
    the **aggregate_sale_by_city** table in the **Explorer**.

    ![A screenshot of a computer Description automatically
    generated](./media/image75.png)

# **Exercise 6: Time travel using T-SQL at statement level**

1.  On the **WideWorldImporters** page, go to the **Home** tab, select **SQL** from the dropdown, and click on **New SQL query**.

    ![](./media/image76.png)

2.  In the query editor, paste the following code to create the
    view Top10CustomerView. Select **Run** to execute the query.

    ```
    CREATE VIEW dbo.Top10CustomersView
    AS
    SELECT TOP (10)
    FS.[CustomerKey],
    DC.[Customer],
    SUM(FS.TotalIncludingTax) AS TotalSalesAmount
    FROM
    [dbo].[dimension_customer] AS DC
    INNER JOIN
    [dbo].[fact_sale] AS FS ON DC.[CustomerKey] = FS.[CustomerKey]
    GROUP BY
    FS.[CustomerKey],
    DC.[Customer]
    ORDER BY
    TotalSalesAmount DESC;
    ```

    ![A screenshot of a computer Description automatically
    generated](./media/image77.png)

3.  In the **Explorer**, verify that you can see the newly created
    view **Top10CustomersView** by expanding the **View** node
    under dbo schema.

    ![A screenshot of a computer Description automatically
    generated](./media/image78.png)

4.  To save this query for reference later, right-click on the query tab
    just above the editor and select **Rename.**

    ![](./media/image79.png)

5.  In the **Rename** dialog box, under the
    **Name** field, enter +++**Top10CustomersView+++**, then click on
    the **Rename** button.

    ![](./media/image80.png)

6.  Create another new query, similar to Step 1. From the **Home** tab
    of the ribbon, select **New SQL query**.

    ![](./media/image41.png)

7.  In the query editor, paste the following code. This updates
    the **TotalIncludingTax** column value to **200000000** for the
    record which has the **SaleKey **value of **22632918.**
    Select **Run** to execute the query.

    ```
    /*Update the TotalIncludingTax value of the record with SaleKey value of 22632918*/
    UPDATE [dbo].[fact_sale]
    SET TotalIncludingTax = 200000000
    WHERE SaleKey = 22632918;
    ```

    ![](./media/image81.png)

8.  In the query editor, paste the following code.
    The CURRENT_TIMESTAMP T-SQL function returns the current UTC
    timestamp as a **datetime**. Select **Run** to execute the query.

    ```
    SELECT CURRENT_TIMESTAMP
    ```

    ![](./media/image82.png)

9.  Copy the timestamp value returned to your
    clipboard.

    ![](./media/image83.png)

10. Paste the following code **(Step-12)** in the query editor and
    replace the timestamp value with the current timestamp value
    obtained from the prior step. The timestamp syntax format
    is **YYYY-MM-DDTHH:MM:SS\[.FFF\].**

11. Remove the trailing zeroes, for
    example: **2024-04-24T20:59:06.097**.

12. The following example returns the list of top ten customers
    by **TotalIncludingTax**, including the new value
    for **SaleKey** 22632918. Replace the existing code and paste the
    following code and select **Run** to execute the query.

    ```
    /*View of Top10 Customers as of today after record updates*/
    SELECT *
    FROM [WideWorldImporters].[dbo].[Top10CustomersView]
    OPTION (FOR TIMESTAMP AS OF '2024-04-24T20:59:06.097');
    ```

    ![](./media/image84.png)

13. Paste the following code in the query editor and replace the
    timestamp value to a time prior to executing the update script to
    update the **TotalIncludingTax** value. This would return the list
    of top ten customers *before* the **TotalIncludingTax** was updated
    for **SaleKey** 22632918. Select **Run** to execute the query.

     ```
    /*View of Top10 Customers as of today before record updates*/
    SELECT *
    FROM [WideWorldImporters].[dbo].[Top10CustomersView]
    OPTION (FOR TIMESTAMP AS OF '2024-04-24T20:49:06.097');
    ```

    ![](./media/image85.png)

14. Rename this query as **Time Travel Now**.

    ![](./media/image86.png)
    
    ![](./media/image87.png)

# **Exercise 7: Create a query with the visual query builder**

## Task 1: Use the visual query builder

Create and save a query with the visual query builder in the Microsoft
Fabric portal.

1.  In the **WideWorldImporters** page, from the **Home** tab of the
    ribbon, select **New visual query**.

    ![](./media/image88.png)

2.  Right-click on **fact_sale** and select **Insert into canvas**

    ![](./media/image89.png)

    ![](./media/image90.png)

3.  Navigate to query design pane **transformations ribbon** and limit
    the dataset size by clicking on **Reduce rows** dropdown, then click
    on **Keep top rows** as shown in the below image.

    ![](./media/image91.png)

4.  In the **Keep top rows** dialog box, enter **10000** and
    Select **OK**.

    ![](./media/image92.png)

    ![A screenshot of a computer Description automatically
    generated](./media/image93.png)

5.  Right-click on **dimension_city** and select
    **Insert into canvas.**

    ![](./media/image94.png)

    ![](./media/image95.png)

6.  From the transformations ribbon, select the
    dropdown beside **Combine** and select **Merge queries as new** as
    shown in the below image.

    ![](./media/image96.png)

7.  On the **Merge** settings page enter the following details.

    - In the **Left table for merge** dropdown, choose **dimension_city**

    - In the **Right table for merge** dropdown, choose **fact_sale** (use
    horizontal and vertical scroll bar)

    - Select the **CityKey** field in the **dimension_city** table by
    selecting on the column name in the header row to indicate the join
    column.

    - Select the **CityKey** field in the **fact_sale** table by selecting
    on the column name in the header row to indicate the join column.

    - In the **Join kind** diagram selection,
    choose **Inner** and click on the **Ok** button.

    ![](./media/image97.png)

    ![](./media/image98.png)

8.  In the data preview pane, locate the
    **fact_sale** column (the last column) and select the **Expand**
    button beside it.

    ![](./media/image99.png)

9.  In the column selector dialog, select only these three
    columns: **TaxAmount**, **Profit**, and **TotalIncludingTax** and
    then select **OK**.

    ![](./media/image100.png)

10. In the **transformations ribbon,** click on
    the dropdown beside **Transform**, then select **Group by**.

    ![](./media/image101.png)

11. On the **Group by** settings page, enter the following details.

    - Select **Advanced** radio button.

    - Under **Group by** select the following:

        1.  **Country**

        2.  **StateProvince**

        3.  **City**

    - In the **New column name,** enter **SumOfTaxAmount** in **Operation**
    column field, select **Sum**, then under **Column** field, select
    **TaxAmount.** Click on **Add aggregation** to add more aggregate
    column and operation.

    - In the **New column name,** enter **SumOfProfit** in **Operation**
    column field, select **Sum**, then under **Column** field, select
    **Profit**. Click on **Add aggregation** to add more aggregate column
    and operation.

    - In the **New column name**, enter **SumOfTotalIncludingTax** in
    **Operation** column field, select **Sum**, then under **Column**
    field, **TotalIncludingTax.** 

    Click on the **OK** button

    ![](./media/image102.png)

    ![](./media/image103.png)

12. In the explorer, navigate to **Queries**
    and right-click on **Visual query 1** under **Queries**. Then,
    select **Rename**.

    ![](./media/image104.png)

13. Type +++Sales Summary+++ to change the name of the query.
    Press **Enter** on the keyboard or select anywhere outside the tab
    to save the change.

    ![A screenshot of a computer Description automatically
    generated](./media/image105.png)

14. Click on the **Refresh** icon below the **Home** tab.

    ![](./media/image106.png)

# **Exercise 8: Analyze data with a notebook**

## Task 1: Create a Lakehouse shortcut and Analyze data with an notebook

In this task, learn about how you can save your data once and then use
it with many other services. Shortcuts can also be created to data
stored in Azure Data Lake Storage and S3 to enable you to directly
access delta tables from external systems.

First, we create a new lakehouse. To create a new lakehouse in your
Microsoft Fabric workspace:

1.  On the **WideWorldImporters** page, click on **Warehouse_Fabric28**
    Workspace on the left-sided navigation menu.

    ![A screenshot of a computer Description automatically
    generated](./media/image107.png)

2.  On the **Warehouse_Fabric28** home page,
    click **+New item**, and then select **Lakehouse **under **Stored data**

    ![](./media/image108.png)

3.  ![](./media/image109.png)In the **Name** field, enter
    +++ShortcutExercise+++ and click on the **Create** button**.**

4.  The new lakehouse loads and the **Explorer** view opens up, with
    the **Get data in your lakehouse** menu. Under **Load data in your
    lakehouse**, select the **New shortcut** button.

    ![](./media/image110.png)

5.  In the **New shortcut** window, select
    **Microsoft OneLake**.

    ![](./media/image111.png)

6.  In the **Select a data source type** window, carefully navigate and
    click on the **Warehouse** named **WideWorldImporters** that you’ve
    created previously, then click on the **Next** button**.**

    ![](./media/image112.png)

7.  In the **OneLake** object browser,
    expand **Tables**, then expand the **dbo** schema, and select the
    radio button beside **dimension_customer**. Select
    the **Next** button.

    ![](./media/image113.png)

8.  In the **New shortcut** window, click on the **Create** button and
    click on the **Close** button

    ![](./media/image114.png)

    ![](./media/image115.png)

9.  Wait for a while and then click on the **Refresh** icon.

10. Then, select the **dimension_customer **in
    the **Table** list to preview the data. Notice that the lakehouse is
    showing the data from the **dimension_customer** table from the
    Warehouse.

    ![](./media/image116.png)

11. Next, create a new notebook to query
    the **dimension_customer** table. In the **Home** ribbon, select the
    drop down for **Open notebook** and choose **New notebook**.

    ![](./media/image117.png)

12. Select, then drag
    the **dimension_customer** from the **Tables** list into the open
    notebook cell. You can see a **PySpark** query has been written for
    you to query all the data
    from **ShortcutExercise.dimension_customer**. This notebook
    experience is similar to Visual Studio Code Jupyter notebook
    experience. You can also open the notebook in VS Code.

    ![](./media/image118.png)

13. In the **Home** ribbon, select the **Run
    all** button. Once the query is completed, you will see you can
    easily use PySpark to query the Warehouse tables!

    ![](./media/image119.png)

# **Exercise 9: Create cross-warehouse queries with the SQL query editor**

## Task 1: Add multiple warehouses to the Explorer

In this task, learn about how you can easily create and execute T-SQL
queries with the SQL query editor across multiple warehouses, including
joining together data from a SQL Endpoint and a Warehouse in Microsoft
Fabric.

1.  From **Notebook1** page, navigate and click on
    **Warehouse_Fabric28** Workspace on the left-sided navigation menu.

    ![](./media/image120.png)

2.  In the **Warehouse_Fabric28** view, select
    the **WideWorldImporters** warehouse.

    ![A screenshot of a computer Description automatically
    generated](./media/image121.png)

3.  In the **WideWorldImporters** page, under
    **Explorer** tab, select the **+ Warehouses** button.

    ![](./media/image122.png)

4.  In Add warehouses window, select **ShortcutExercise** and click on
    the **Confirm** button. Both warehouse experiences are added to the
    query.

    ![](./media/image123.png)

5.  Your selected warehouses now show the
    same **Explorer** pane.

    ![](./media/image124.png)

## Task 2: Execute a cross-warehouse query

In this example, you can see how easily you can run T-SQL queries across
the WideWorldImporters warehouse and ShortcutExercise SQL Endpoint. You
can write cross-database queries using three-part naming to reference
the **database.schema.table**, as in SQL Server.

1.  From the **Home** tab of the ribbon,
    select **New SQL query**.

    ![](./media/image125.png)

2.  In the query editor, copy and paste the following T-SQL code. Select
    the **Run** button to execute the query. After the query is
    completed, you will see the results.

    ```
    SELECT Sales.StockItemKey, 
    Sales.Description, 
    SUM(CAST(Sales.Quantity AS int)) AS SoldQuantity, 
    c.Customer
    FROM [dbo].[fact_sale] AS Sales,
    [ShortcutExercise].[dbo].[dimension_customer] AS c
    WHERE Sales.CustomerKey = c.CustomerKey
    GROUP BY Sales.StockItemKey, Sales.Description, c.Customer;
    ```

    ![](./media/image126.png)

3.  Rename the query for reference. Right-click
    on **SQL query** in the **Explorer** and select **Rename**.

    ![](./media/image127.png)

4.  In the **Rename** dialog box, under the **Name** field, enter
    +++**Cross-warehouse query+++**, then click on the **Rename**
    button. 

    ![A screenshot of a computer Description automatically
    generated](./media/image128.png)

# **Exercise 10: Create Power BI reports**

## Task 1: Enable azure maps visuals sign in

1.  To enable the settings to start creating azure map visuals, navigate
    to **settings** option at the top.

    ![](./media/image129.png)

2.  In **Settings** pane, scroll down to
    **Governance and insights**, then click on **Admin portal**.

    ![](./media/image130.png)

3.  In **Admin portal** pane, select **Tenant settings**, scroll down to
    **Integration settings** section, click on **Use Azure Maps
    visual**, then enable it using the **toggle** button. After **Use
    Azure Maps visual** were Enabled, click on the **Apply** button.

    ![](./media/image131.png)

4.  You will see a notification
    stating - **Applying changes.** It will take approximately 15
    minutes for implementing the tenant settings.

    ![A screenshot of a computer Description automatically
    generated](./media/image132.png)

5.  In **Admin portal** pane, select **Tenant settings**, scroll down to
    **Integration settings** section, click on **Map and filled map
    visuals**, then enable it using the **toggle** button. After **Use
    Azure Maps visual** were Enabled, click on the **Apply** button.

    ![A screenshot of a computer Description automatically
    generated](./media/image133.png)

## Task 2: Create Power BI reports

1.  In the **WideWorldImporters** page, under
    the **Home** tab, select the **Model layout** view.

    ![](./media/image134.png)

2.  From the **fact_sale** table, drag
    the **CityKey** field and drop it onto the **CityKey** field in
    the **dimension_city** table to create a relationship.

    ![](./media/image135.png)

3.  On the **Create Relationship** settings:

    - **Table 1** is populated with **fact_sale** and the column
    of **CityKey**.

    - **Table 2** is populated with **dimension_city** and the column
    of **CityKey**.

    - **Cardinality**: select **Many to one (\*:1)**.

    - **Cross filter direction**: select **Single**.

    - Check the box for **Make this relationship active**.

    - Select **Save**.

    ![](./media/image136.png)

    ![](./media/image137.png)

1.  From the **Reporting** tab of the ribbon, select **New report**.

    ![](./media/image138.png)
    
    ![](./media/image139.png)

2.  In the **Warehouse_Fabric28** page, under **Visualizations**, click
    to the **Column chart** icon to add a **Column chart** to your
    report.

    - On the **Data** pane, expand **fact_sales** and check the box next
    to **Profit**. This creates a column chart and adds the field to the
    Y-axis.

    - On the **Data** pane, expand **dimension_city** and check the box next
    to **SalesTerritory**. This adds the field to the X-axis.

    - Reposition and resize the column chart to take up the top left quarter
    of the canvas by dragging the anchor points on the corners of the
    visual.

    ![](./media/image140.png)

    ![](./media/image141.png)

    ![](./media/image142.png)

1.  Select anywhere on the blank canvas (or press the Esc key) so the
    column chart visual is no longer selected.

2.  Build a **Maps** visual,On
    the **Visualizations** pane, select the **ArcGIS Maps for Power
    BI** visual.

    ![](./media/image143.png)

3.  From the **Data** pane, drag **StateProvince** from
    the **dimension_city** table to the **Location** bucket on
    the **Visualizations** pane.

    ![](./media/image144.png)

4.  From the **Data** pane, drag **Profit** from the **fact_sale** table
    to the **Size** bucket on the **Visualizations** pane.

    ![](./media/image145.png)

5.  If necessary, reposition and resize the map to take up the bottom
    left quarter of the canvas by dragging the anchor points on the
    corners of the visual.

    ![A map of the world with blue dots Description automatically
    generated](./media/image146.png)

    ![](./media/image147.png)

6.  Select anywhere on the blank canvas (or press the Esc key) so the
    map visual is no longer selected.

7.  Build a **Table** visual on
    the **Visualizations** pane, select the **Table** visual.

    ![](./media/image148.png)

8.  From the **Data** pane, check the box next to **SalesTerritory** on
    the **dimension_city** table.

9.  From the **Data** pane, check the box next to **StateProvince** on
    the **dimension_city** table.
    
    ![](./media/image149.png)

10. From the **Data** pane, check the box next to **Profit** on
    the **fact_sale** table.

11. From the **Data** pane, check the box next
    to **TotalExcludingTax** on the **fact_sale** table.

    ![](./media/image150.png)

12. Reposition and resize the column chart to take up the right half of
    the canvas by dragging the anchor points on the corners of the
    visual.

    ![](./media/image151.png)

13. In the **Warehouse_Fabric28** page, from the ribbon,
    select **File** and select the **Save**.
    
    ![](./media/image152.png)

14. In the Save your report dialog box, enter +++**Sales Analysis+++**
    as the name of your report and click on the **Save** button.

    ![](./media/image153.png)

15. You will see a notification stating
    **Report saved.**

    ![](./media/image154.png)

## Task 3: Build a report from the OneLake data hub

Learn how to build a report with the data you ingested into your
Warehouse in the last step.

1.  In the **Sales Analysis** pane, select the **OneLake data hub** in
    the left navigation menu.

    ![](./media/image155.png)

2.  From the item list, select **WideWorldImporters** with the type
    of **Dataset (default)**.

    ![](./media/image156.png)

3.  In the **Visualize this data** section, select **Create a
    report** and select **Auto-create**. A report is generated from
    the **dimension_customer** table that was loaded in the previous
    section.

    ![](./media/image157.png)

4.  A report similar to the following image is generated.

    ![](./media/image158.png)

5.  From the ribbon, select **Save**.

    ![](./media/image159.png)

6.  In the **Save your report** dialog box, enter +++**Customer Quick
    Summary+++** in the name box. In the **Save your report** dialogue,
    select **Save**.

    ![](./media/image160.png)

7.  ![A screenshot of a phone Description automatically
    generated](./media/image161.png)You will see a notification stating
    **Report saved.**

    ![](./media/image162.png)

## Task 4: Clean up resources

You can delete individual reports, pipelines, warehouses, and other
items or remove the entire workspace. In this tutorial, you will clean
up the workspace, individual reports, pipelines, warehouses, and other
items you created as part of the lab.

1.  Select **Warehouse_Fabric28** in the navigation menu to return to
    the workspace item list.

    ![A screenshot of a computer Description automatically
    generated](./media/image163.png)

2.  In the menu of the workspace header, select **Workspace settings**.

    ![A screenshot of a computer Description automatically
    generated](./media/image164.png)

3.  In the **Workspace settings** dialog box, select **Other** and
    select the **Remove this workspace**.

    ![A screenshot of a computer Description automatically
    generated](./media/image165.png)

4.  In the **Delete workspace?** dialog box, click on the **Delete**
    button.

    ![A screenshot of a computer Description automatically
    generated](./media/image166.png)

**Summary**

This comprehensive lab walks through a series of tasks aimed at
establishing a functional data environment in Microsoft Fabric. It
starts with the creation of a workspace, essential for data operations,
and ensures the trial is enabled. Subsequently, a Warehouse named
WideWorldImporters is established within the Fabric environment to serve
as the central repository for data storage and processing. Data
ingestion into the Warehouse_Fabric28 workspace is then detailed through
the implementation of a Data Factory pipeline. This process involves
fetching data from external sources and integrating it seamlessly into
the workspace. Critical tables, dimension_city, and fact_sale, are
created within the data warehouse to serve as foundational structures
for data analysis. The data loading process continues with the use of
T-SQL, where data from Azure Blob Storage is transferred into the
specified tables. The subsequent tasks delve into the realm of data
management and manipulation. Cloning tables is demonstrated, offering a
valuable technique for data replication and testing purposes.
Additionally, the cloning process is extended to a different schema
(dbo1) within the same warehouse, showcasing a structured approach to
data organization. The lab progresses to data transformation,
introducing the creation of a stored procedure to efficiently aggregate
sales data. It then transitions to visual query building, providing an
intuitive interface for complex data queries. This is followed by an
exploration of notebooks, demonstrating their utility in querying and
analyzing data from the dimension_customer table. Multi-warehouse
querying capabilities are then unveiled, allowing for seamless data
retrieval across various warehouses within the workspace. The lab
culminates in enabling Azure Maps visuals integration, enhancing
geographical data representation in Power BI. Subsequently, a range of
Power BI reports, including column charts, maps, and tables, are created
to facilitate in-depth sales data analysis. The final task focuses on
generating a report from the OneLake data hub, further emphasizing the
versatility of data sources in Fabric. Finally, the lab provides
insights into resource management, emphasizing the importance of cleanup
procedures to maintain an efficient workspace. Collectively, these tasks
present a comprehensive understanding of setting up, managing, and
analyzing data within Microsoft Fabric.
