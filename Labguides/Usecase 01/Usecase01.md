---
lab:
  title: 'Use case 01: Implementing Medallion Architecture with Data Factory in Microsoft Fabric for Scalable Data Processing'
  description: You, as a Data Engineer at Contoso Retail, are tasked with implementing Medallion Architecture in Microsoft Fabric. You collaborated with the solution architect, BI developers, and governance team to deliver a unified data pipeline from ingestion to reporting with the following objectives.
  duration: 5 minutes
  level: 400
  islab: true
  primarytopics:
    - Microsoft Fabric
---

# Use case 01: Implementing Medallion Architecture with Data Factory in Microsoft Fabric for Scalable Data Processing

## Scenario: Contoso Retail

**Contoso Retail** is a multinational retail and distribution company specializing in electronics, apparel, and home goods. With a vast network of stores and a thriving e-commerce platform, Contoso generates high volumes of transactional and customer data daily.

However, Contoso is facing challenges with:

- Fragmented data across SQL databases and blob storage

- Manual data preparation for reporting

- Limited scalability in analytics workflows

To overcome these, Contoso aimed to modernize its data platform using Microsoft Fabric’s Medallion Architecture.

You, as a Data Engineer at Contoso Retail, are tasked with implementing Medallion Architecture in Microsoft Fabric. You collaborated with the solution architect, BI developers, and governance team to deliver a unified data pipeline from ingestion to reporting with the following objectives.

- Ingest raw data from Azure SQL and blob storage into a **Bronze Lakehouse.**

- Transform and structure data into a **Silver Lakehouse.**

- Curate business-ready datasets in **Gold Warehouse.**

- Automate orchestration using **Pipelines** and **dynamic expressions**.

- Demonstrate end-to-end integration of **Power BI** and **T-SQL** with Direct Lake mode.

![](./media/image1.png)

## Exercise 1: Azure SQL Mirroring and Bronze Layer Setup

In this exercise, you initiate the Medallion Architecture implementation by establishing the Bronze layer in Microsoft Fabric. This involves setting up the foundational components required to ingest data from Contoso’s retail systems. You configure the workspace, connect to key data sources, and build pipelines to automate data ingestion and organization. By the end of this phase, you ensure that raw data is efficiently captured and structured for downstream processing.

![](./media/image2.png)

### Task 1: Restoring the WideWorldImporters Sample Database Using SQL Server Management Studio (SSMS)

1.  In your Windows search box, type +++sql server management studio 21+++, then click **SQL Server Management Studio 21**.

    ![](./media/image3.png)

2.  Enter the Azure SQL Server name, select **SQL Server Authentication**, provide the **sqladminuser** credentials, and then open the **Database name** drop-down.

    ![](./media/image4.png)

3.  Sign in with your Microsoft work, school, or personal account, enter your credentials.

    ![](./media/image5.png)

    ![](./media/image6.png)

    ![](./media/image7.png)

    ![](./media/image8.png)

    ![](./media/image9.png)

    ![](./media/image10.png)

4.  Click **OK**

    ![](./media/image11.png)

5.  Select the **WideWorldImporters** database and click **Connect**.

    ![](./media/image12.png)

    ![](./media/image13.png)

6.  To import data into the database, in Object Explorer right-click the **WideWorldImporters** database, select **Tasks**, and then click **Import Flat File** to start the data import wizard.

    ![](./media/image14.png)

7.  On the **Import Flat File** wizard **Introduction** page, review the details and click **Next** to proceed.

    ![](./media/image15.png)

8.  On the **Specify Input File** page, click **Browse** to select the file to import

    ![](./media/image16.png)

9.  Browse to **C:\LabFiles\LabFiles\Lab1** on your VM, then select **Dimension_City.csv** file and click on **Open** button.

    ![](./media/image17.png)

10. Click **Next** to proceed.

    ![](./media/image18.png)

11. Click **Next** to proceed.

    ![](./media/image19.png)

12. On the **Summary** page, review the import details and click **Finish** to complete the data import.

    ![](./media/image20.png)

13. Click **Next** to proceed.

    ![](./media/image21.png)

14. On the **Results** page, verify that the operation completed successfully and click **Close** to exit the wizard.

    ![](./media/image22.png)

15. Continue repeating steps **6 through 14** to upload all the remaining files.

    ![](./media/image23.png)

    ![](./media/image24.png)

    ![](./media/image25.png)

    ![](./media/image26.png)

### Task 2: Configure Azure Storage Account with ContosoSales data

In this task, you create an Azure Storage account with hierarchical namespace enabled to support scalable, structured storage using Azure Data Lake Gen2. You set up a container named contososales and upload the ContosoSales.zip file, establishing a centralized repository for raw data. This setup forms the foundation of the Bronze layer, enabling seamless ingestion and transformation processes within the Medallion Architecture.

1.  Click on the **Azure Portal Menu** and select **Create a resource**.

    ![](./media/image27.png)

2.  On the **Create a resource** search box, type +++Storage account+++ and then click on the **storage account**.

    ![](./media/image28.png)

3.  Click the **Storage account** tile on the **Marketplace** page.

    ![](./media/image29.png)

4.  On the **Storage account** page, click **Create**.

    ![](./media/image30.png)

5.  On **Create a storage account** page, under the **Basics** tab, enter the following details to create a storage account and then click on **Next**

    | Setting | Value |
    |---|---|
    | Subscription | Select your Azure subscription |
    | Resource group       | Select your Resource group                                                           |
    | Storage account name | +++fabricstorageXXXX+++ (XXXX can be the last 4 digits of the lab instance ID)                |
    | Region               | Select the appropriate region for your storage account. In this lab, **East US** is used |
    | Primary service      | **Azure Blob Storage or Azure Data Lake Storage**                                |
    | Performance          | Standard: Recommended for most scenarios (general-purpose v2 account)                |
    | Redundancy           | Locally-redundant storage (LRS)                                                      |

    ![](./media/image31.png)

    ![](./media/image32.png)

6.  Under the **Advanced** tab, select **"Enable hierarchical namespace"** and click **Review + create**.

    ![](./media/image33.png)

7.  On the **Review + Create** page, click **Create**.

    ![](./media/image34.png)

8.  The Azure Storage account is now set up to host data for Azure Data Lake. Click **Go to resource**.

    ![](./media/image35.png)

9.  On the left-side navigation pane of your Storage Account, select **Data storage** section and then select **Containers**.

    ![](./media/image36.png)

10. Click **Add container**.

    ![](./media/image37.png)

11. On the New container pane that appears on the right side, enter the container Name as +++contososales+++ and click on **Create** button.

    ![](./media/image38.png)

    ![](./media/image39.png)

12. On **fabricstorageXXXX \| Containers** page, select **contososales** container.

    ![](./media/image40.png)

13. Now, you are going to upload the **ContosoSales.zip** file in the Container. On the **Overview** page of **contososales** container, select **Upload**.

    ![](./media/image41.png)

14. On the **Upload blob** pane, click on **Browse for file**, navigate to **C:\Labfiles** location and select **ContosoSales.zip**, then click on the **Open** button.

    ![](./media/image42.png)

15. On **Upload blob** page, click on the **Upload** button.

    ![](./media/image43.png)

    ![](./media/image44.png)

16. Go back to your Storage Account. From the left navigation, select **Access keys** under **Security + networking** group, copy **Key and Storage account name**, paste them in a notepad, and then **Save** the notepad to use the information in the upcoming task.

    ![](./media/image45.png)

### Task 3: Create a Fabric workspace

In this task, you set up a new Microsoft Fabric workspace to serve as the central hub for the Medallion Architecture implementation. This workspace brings together all essential components, including Lakehouses, Dataflows, Pipelines, Warehouses, Notebooks, and Power BI assets. By naming it appropriately—such as **Data Factory – Medallion**—you ensure a well-organized environment for managing the entire data lifecycle, from raw ingestion to curated analytics.

1.  Open your browser, navigate to the address bar, and type or paste the following URL: +++https://app.fabric.microsoft.com/+++ then press the **Enter** button.

2.  On the **Fabric Home** page click on **+ New Workspaces** as shown in the image below.

    ![](./media/image46.png)

3.  On the **Create a workspace** pane that appears to the right, enter the following details, and then click **Apply**.

    | **Setting** | **Value** |
    |---|---|
    | **Name**                   | +++Data Factory-MedallionXX+++ (XX can be a unique number, you can add more numbers) |
    | **Advanced**               | Select **Fabric**                                                                          |
    | **Default storage format** | **Small dataset storage format**                                                           |

    ![](./media/image47.png)

    ![](./media/image48.png)

4.  The Workspace is now created.

    ![](./media/image49.png)

### Task 4: Implement Azure SQL Mirroring to Bronze layer

In this task, you implement a mirrored Azure SQL Database within your Fabric workspace by selecting the Medallion task flow template. This automatically sets up a structured environment that mirrors SQL tables into the Bronze layer. The process creates a mirrored database, analytics endpoint, and storage object, ensuring continuous synchronization of SQL data. This setup helps maintain a reliable raw data copy without the need for manual exports, streamlining ingestion for downstream processing.

1.  From the empty workspace, select the option **Select a predefined task flow** to choose one of Microsoft's task flows. These predesigned task flows provide a structured approach to managing data projects.

    ![](./media/image50.png)

2.  From the **Select a predesigned task flow** pane, choose the **Medallion** option.

    This option includes the description "Organize and improve data progressively as it moves through each layer," which is crucial for data projects. The medallion architecture helps in structuring data into different layers, such as bronze, silver, and gold, to enhance data quality and accessibility.

3.  Select the **Select** option to continue.

    ![](./media/image51.png)

4.  A task flow has now been created within your workspace, which can be considered as an architectural template. This template provides a structured framework for your data project.

    ![](./media/image52.png)

5.  Select the **New item** option on the **Bronze data** block to start adding items to task flow.

    ![](./media/image53.png)

6.  On the **New item** page, the available options within Microsoft Fabric have been filtered down to **All items**. This filtering is helpful for choosing the correct items for project.

    ![](./media/image54.png)

7.  In the **Filter by item type** search box, enter +++Mirrored Azure SQL Database+++ and select the **Mirrored Azure SQL Database** item.

    ![](./media/image55.png)

8.  On the **Choose a database connection to get started** page, select **Azure SQL database**

    ![](./media/image56.png)

9.  On the **New source** tab, enter the following details, and click on the **Connect** button.

    - **Server:** Enter your SQL server URL

    - **Database:** Enter the database as WideWorldImporters

    ![](./media/image57.png)

10. On the **Choose data** tab, select **Select all** and click on **Connect** button

    ![](./media/image58.png)

11. On the **Destination** tab, click **Create mirrored database**.

    ![](./media/image59.png)

    ![](./media/image60.png)

12. Check the status of mirroring the database.

    ![](./media/image61.png)

    ![](./media/image62.png)

13. From the **Home** tab, click the **settings** icon and select *About*. Copy the mirrored SQL endpoint name displayed as **WideWorldImporters** and paste it into Notepad. We will use this name in the upcoming tasks.

    ![](./media/image63.png)

14. Select **SQL endpoint**, copy the mirrored SQL connection string, and paste it into Notepad. We will use this name in the upcoming tasks.

    ![](./media/image64.png)

15. On the left navigation, click on ***Data Factory-MedallionXX***, as shown in the image below.

    ![](./media/image65.png)

16. Within the workspace, you will notice that some items have now been created and are associated with your Mirrored SQL database. These items include the Mirrored database (storage) and SQL analytics endpoint.

    ![](./media/image66.png)

### Task 5: Build the Bronze Lakehouse and Ingest Data Using Copy Job

1.  Select the **New item** option on the **Bronze data** block to start adding items to task flow.

    ![](./media/image67.png)

2.  On the **Create an item** page, the available options within Microsoft Fabric have been filtered down to **Recommended items**. This filtering is helpful for choosing the correct items for project.

3.  Select the **Lakehouse** item for data storage.

    ![](./media/image68.png)

4.  On the New lakehouse pane, set the lakehouse name to +++bronze_Lakehouse+++ and **unselect** the lakehouses schemas. Click on the **Create** button and open the new lakehouse.

    ![](./media/image69.png)

    ![](./media/image70.png)

5.  On the left navigation, click on ***Data Factory-MedallionXX***, as shown in the image below.

    ![](./media/image71.png)

6.  Within the workspace, you will notice that three items have now been created and are associated with your lakehouse. These items include the lakehouse and the SQL analytics endpoint.

    ![](./media/image72.png)

7.  Now proceed to select and add a **New item** from the **High-volume data ingest** task.

    ![](./media/image73.png)

8.  Select the **Copy job** tile.

    ![](./media/image74.png)

9.  On the New copy job window, set the copy job name to +++bronze_copyjob+++ and then select **Create**.

    ![](./media/image75.png)

10. From the Copy job window, use the Home tab at the top if you want to set additional criteria such as filters or item types. Select **SQL Server database**

    ![](./media/image76.png)

11. On the **Connect to data source** tab, enter the following details, and click on the **Next** button.

    - **Server:** Enter the **SQL connection string** for the mirrored database **WideWorldImporters**.

    - **Database:** Enter the database as +++WideWorldImporters+++

    - **Authentication kind:** Organizational account

    ![](./media/image77.png)

12. Select all the tables and click on **Next** button

    ![](./media/image78.png)

13. On the Choose data destination tab, search for the data source starting with +++bronze_Lakehouse+++ in the search bar and then select **bronze_Lakehouse** from the OneLake catalog list.

    ![](./media/image79.png)

14. On the **Settings** step, review the Copy job mode options and then select **Next** to continue.

    ![](./media/image80.png)

15. On the **Map to destination** tab, you can review the added tables and update the schema or table names. Once reviewed, select **Next** to continue.

    ![](./media/image81.png)

16. On the **Review + save** step, deselect the **Start data transfer immediately** option and then select **Save** to continue.

    ![](./media/image82.png)

17. Now that you've reviewed the configuration, from the **Home** tab, select the **Run** option to begin copying data from the SQL mirroring database into the bronze lakehouse.

    ![](./media/image83.png)

    ![](./media/image84.png)

18. You can monitor the copy job in the **Results** tab at the bottom to confirm that it has successfully completed.

    ![](./media/image85.png)

19. On the top menu, select ***bronze_Lakehouse***, as shown in the image below.

    ![](./media/image86.png)

20. To validate the copy tables, click and select refresh on the **Tables** in the **Explorer** panel until all the tables appear in the list.

    ![](./media/image87.png)

21. Now review the list of tables

    ![](./media/image88.png)

### Task 6: Build Dynamic Data Pipelines Using Copy Activity

In this task, you create a pipeline for data ingestion. Using the Copy Data assistant, you import sample data (Public Holidays.csv) into Lakehouse. To organize the files efficiently, you enhance the pipeline with a dynamic expression that creates a date-based folder structure (yyyy/MM/dd). This setup ensures that data ingestion is automated, scalable, and well-structured, with validation confirming successful execution.

1.  On the left navigation, click on ***Data Factory-MedallionXX***, as shown in the image below.

    ![](./media/image89.png)

    ![](./media/image90.png)

2.  Now proceed to select and add a **New item** from the **High-volume data ingest** task.

    ![](./media/image91.png)

3.  On the **New item** page, the available options within Microsoft Fabric have been filtered down to **Recommended items**. Select the **Pipeline** item.

    ![](./media/image92.png)

4.  On the **New pipeline** pane, set the data pipeline name to +++samplePipeline+++ and then select **Create**.

    ![](./media/image93.png)

5.  From the data pipeline page, drop down the **Copy data** and select the **Add copy data activity** to copy the sample data.

    ![](./media/image94.png)

6.  The properties section below provides access to the configurations for Source, Destination, Settings, and more. These configurations can be edited directly to ensure the data copy activity is correctly set up and aligned with specific requirements.

    ![](./media/image95.png)

7.  Click the **Source** tab, open the **Connection** drop-down menu, and select **Browse all**.

    ![](./media/image96.png)

8.  On the **Choose data source to get started** page, select **Sample data** and then select the **Public Holidays** data source type.

    ![](./media/image97.png)

9.  A preview of the data source will now be displayed to verify the correct selection and understand the data structure. After reviewing the preview, select **OK** to proceed.

    ![](./media/image98.png)

10. Select the **Destination** tab

    ![](./media/image99.png)

11. In the **Destination** tab, open the **Connection** drop-down menu, and select **Browse all**.

    ![](./media/image100.png)

12. Select the **bronze_Lakehouse** lakehouse item as the data destination from the **OneLake catalog list**. It determines the storage location for the data.

    ![](./media/image101.png)

13. On the **Destination** tab, select the **Files** and select the File format as **Parquet**

    ![](./media/image102.png)

14. Select the **Run** option to start the pipeline and begin your data ingestion process. Running the pipeline initiates the data transfer from the source to the destination.

    ![](./media/image103.png)

15. On the **Save and run?** dialog box, click **Save and run** to execute these activities. This activity will take less than 1 min.

    ![](./media/image104.png)

16. The global properties and **Output** view will then become visible. After starting the run of your pipeline, both the **Pipeline status** and the **Activity status** should be **Succeeded**. This indicates that everything ran as intended, confirming that your data ingestion process is successful.

    ![](./media/image105.png)

17. From the top menu, select the **bronze_Lakehouse lakehouse** created in the previous task.

    ![](./media/image106.png)

18. The file has been successfully added to the **Files** section. Selecting the file provides a preview of the data, helping confirm that the ingestion process was completed correctly.

    ![](./media/image107.png)

    ![](./media/image108.png)

19. Delete the existing **Holidays** file from the lakehouse by selecting the ellipses (**...**) and then select **Delete**.

    ![](./media/image109.png)

20. A confirmation window will be displayed. Select **Delete** to confirm the removal of the file.

    ![](./media/image110.png)

21. On the top navigation, select **samplePipeline**.

    ![](./media/image111.png)

22. Select the **Copy data** activity and then go to the **Destination** tab.

    ![](./media/image112.png)

23. Select the text box input for the **File path**, where after selection you will see the text **Add dynamic content \[Alt+Shift+D\]**. Select this text to open the pipeline expression builder.

    ![](./media/image113.png)

24. On the Pipeline expression builder page, select the **Functions** tab. Here, you can explore various functions that exist within the expression library. These functions provide powerful tools for creating dynamic expressions. When you're ready, copy and paste the code block below into the expression input box. Press **Ok** when complete.

    ```
    @formatDateTime(
        convertFromUtc(
            utcnow(), 'Central Standard Time'
        ),
        'yyyy/MM/dd'
    )
    ```

    ![](./media/image114.png)

    **Note:** This expression will be used to create a folder structure within your pipeline that writes the file to nested folders based on the current year, the current month, and the current date of the run time. The forward slash "**/**" character is how the folder structure is defined. This dynamic folder structure helps in organizing your data based on the date, making it easier to manage and retrieve.

25. From the **Home** tab, select the **Validate** once again.

    ![](./media/image115.png)

26. Select the **Run** option to start the pipeline and begin data ingestion process. Running the pipeline initiates the data transfer from the source to the updated destination folder path.

    ![](./media/image116.png)

27. On the **Save and run?** dialog box, click on **Save and run** button to execute these activities. This activity will take less than 1 min.

    ![](./media/image117.png)

28. The global properties and **Output** view will then become visible. After starting the run of your pipeline, both the **Pipeline status** and the **Activity status** should be **Succeeded**. This indicates that everything ran as intended, confirming that your data ingestion process is successful.

    ![](./media/image118.png)

29. From the top navigation, select the **bronze_Lakehouse lakehouse** that has been created in the previous task.

    ![](./media/image119.png)

30. The file has been successfully added to the **Files** section, organized within a nested folder structure based on the **year, month, and date** of execution. This confirms that the dynamic file output is functioning correctly and that the data is being structured as intended.

    ![](./media/image120.png)

    **Note:** If the contents are not yet visible, navigate to the Home tab and select the Refresh icon to start the metadata sync process and update the lakehouse viewer content.

31. Select the **ellipses (...)** next to the top level year folder and then select **Delete** to remove the **sample data**

    ![](./media/image121.png)

32. A confirmation window will appear, select **Delete** to proceed with removing the contents.

    ![](./media/image122.png)

    ![](./media/image123.png)

### Task 7: Create a Data Pipeline and Establish a Connection to ADLS Gen2 Storage

In this task, you establish a connection between the Bronze Lakehouse and Azure Blob Storage (ADLS Gen2). A pipeline is created to copy and unzip the ContosoSales.zip file into the Lakehouse, preserving the original file hierarchy. Dynamic expressions are used to organize the files under a date-based folder structure, ensuring scalable and well-structured ingestion. The result is a clean and accessible Bronze layer containing raw datasets ready for further processing.

1.  On the left navigation, select ***Data Factory-MedallionXX***, as shown in the image below.

    ![](./media/image124.png)

2.  Now proceed to select and add a **New item** from the **High-volume data ingest** task.

    ![](./media/image125.png)

3.  On the **New item** page, the available options within Microsoft Fabric have been filtered down to **Recommended items**. Select the **Pipeline** item.

    ![](./media/image126.png)

4.  On the New pipeline pane, set the data pipeline name to +++getContosoSample+++ and then select **Create**.

    ![](./media/image127.png)

5.  From the new and empty data pipeline, select the **Pipeline activity** watermark option and then choose **Copy data** to add this activity to the authoring canvas.

    ![](./media/image128.png)

6.  With the **Copy data** activity selected, navigate to the **Source** tab. Within the **Connection** drop-down menu, select **Browse all** to launch the **Get data** navigator.

    ![](./media/image129.png)

    This navigator provides a comprehensive interface for connecting to various data sources, ensuring that you can easily integrate different data streams into pipeline.

7.  From the Get data navigator, select **New** from the left pane.

    ![](./media/image130.png)

8.  From the list of **New sources**, select **View more**, search for **Azure Blobs** and select it.

    ![](./media/image131.png)

9.  On the **Connect to data source** window, enter the details from the table below and select **Connect**.

    | **Property**        | **Value**                  |
    |---------------------|----------------------------|
    | Account name or URL | Enter your storage account |
    | Connection          | Create a new connection    |
    | Connection name     | +++ContosoSample+++    |
    | Data gateway        | None                       |
    | Authentication kind | Account key                |
    | Account key         | Enter storage account key  |

    ![](./media/image132.png)

    ![](./media/image133.png)

10. Select the **Copy data** activity and then the **Source** tab. On the **File path**, enter your **container name** and **File name**.

    ![](./media/image134.png)

11. Select the **Copy data** activity and then the **Source** tab. Select the **Settings** option next to the File format field.

    ![](./media/image135.png)

12. On the **Compression type** setting, choose **ZipDeflate (.zip)** and select **OK** to complete.

    ![](./media/image136.png)

13. On the Source tab, expand the **Advanced** section. Deselect the option to **Preserve zip file name as folder**. This allows you to customize the folder name for your zip contents, providing more flexibility in organizing data.

    ![](./media/image137.png)

14. On the Copy data activity, navigate to the **Destination** tab.

    ![](./media/image138.png)

15. From the list of connections, select the **Browse all**.

    ![](./media/image139.png)

16. Select the **bronze_Lakehouse** lakehouse item as the data destination from the **OneLake catalog list**. It determines the storage location for the data.

    ![](./media/image140.png)

    This step ensures that the data is being copied to the correct destination, which is essential for maintaining data integrity and organization.

17. On the Destination settings, select the **Files** option and then the **Directory** file path text input box. This will display the **Add dynamic content \[Alt+Shift+D\]** property.

    ![](./media/image141.png)

18. Select the text to open the pipeline expression builder. The expression builder allows us to create dynamic file paths, which can be customized based on various dynamic parameters such as date and time or static text values.

    ![](./media/image142.png)

19. On the Pipeline expression builder page, select the **Functions** tab. Here, you can explore various functions that exist within the expression library. In this example we'll use both date and string functions to create a dynamic folder path. Copy and paste the code block below into the expression input box. Press **Ok** when complete.

    ```
    @concat(
        'ContosoSales\',
        formatDateTime(
            convertFromUtc(
                utcnow(), 'Central Standard Time'
            ),
            'yyyy/MM/dd'
        )
    )
    ```

    ![](./media/image143.png)

20. With the Copy data activity and Destination settings selected, expand the **Advanced** section, select the drop-down for the **Copy behavior** and then choose the **Preserve hierarchy** option.

21. This option maintains the original file names as they are within the zip file, ensuring that the file structure is preserved during the copy process.

    ![](./media/image144.png)

22. Navigate to the **General** tab with the Copy data activity selected. Update the **Name** and **Description** fields with the appropriate text. This step helps in identifying and managing the activity within your pipeline, making it easier to understand its purpose and functionality.

    | **Property** | **Text**                      |
    |--------------|-------------------------------|
    | Name         | +++Get and Unzip files+++ |

    ![](./media/image145.png)

23. From the **Home** tab, select the **Validate** option to first confirm that there are no issues with your pipeline. This validation step helps in identifying any errors to be fixed before running the pipeline.

    ![](./media/image146.png)

    ![](./media/image147.png)

24. Once validated, select the **Save** option and then **Run** to start the ingestion from the data pipeline. Running the pipeline initiates the data transfer, allowing you to see the results of your configuration in action within the output window.

    ![](./media/image148.png)

    ![](./media/image149.png)

25. This action will make the global properties and **Output** view visible. After starting the run of your pipeline, both the Pipeline status and any individual Activity statuses should show a **Succeeded** status. This indicates that everything ran as intended, confirming that your data ingestion process was successful.

    ![](./media/image150.png)

26. From the top navigation, click **bronze_Lakehouse lakehouse**

    ![](./media/image151.png)

27. The contents of the **zip file** have been successfully added to the **Files** section, organized in a nested folder structure based on the data source title, year, month, and date of the pipeline run.

    ![](./media/image152.png)

## Exercise 2: Data Movement from Bronze to Silver Layer

With the Bronze layer in place, you shift your focus to the **Silver** layer, where the goal is to transform raw data into structured, analytics-ready tables. This phase emphasizes orchestrating data movement using Data Factory, leveraging dynamic expressions, variables, and conditional paths to build flexible pipelines. You set up logic to handle different data scenarios, ensuring that dimension tables are refreshed and fact tables are incrementally updated. The structured movement of data between layers enhances modularity, scalability, and overall data quality, laying the groundwork for reliable analytics.

![](./media/image153.png)

### Task 1: Build a Silver Layer Lakehouse and Integrate a New Data Pipeline with SQL Database

In this task, you create a new Lakehouse called silver_Lakehouse to store structured and cleansed data. A copy job named createContosoTables is built to copy tables from the mirrored Azure SQL Database into the Silver Lakehouse. Dimension and fact tables are ingested into the Files section, later appearing in the Tables section of the Lakehouse. This ensures that data is transitioned from raw (Bronze) to structured (Silver) format, optimized for analytical queries.

1.  On the left navigation, click on ***Data Factory-MedallionXX***, as shown in the image below.

    ![](./media/image154.png)

2.  Select the **New item** option on the **Silver data** block to add another storage item to project. Within the **Item type** selection, select **Lakehouse**.

    ![](./media/image155.png)

    ![](./media/image156.png)

3.  On the New lakehouse pane, set the lakehouse name to +++silver_Lakehouse+++ and **unselect** the lakehouses schemas. Click on the **Create** button and open the new lakehouse.

    ![](./media/image157.png)

4.  On the **Home** tab of the lakehouse, select **Get data** **\>** **New copy job**

    ![](./media/image158.png)

5.  On the **New copy job** pane, set the copy job name to +++createContosoTables+++, select **Assign to task** as **Initial process** and then select **Create**

    ![](./media/image159.png)

6.  In the **Home** tab , Select **SQL Server database**

    ![](./media/image160.png)

7.  On the **Connect to data source** tab, enter the following details, and click on the **Next** button.

    - **Server:** Enter the **SQL connection string** for the mirrored database **WideWorldImporters**.

    - **Database:** Enter the database as +++WideWorldImporters+++

    - **Authentication kind:** Organizational account

    ![](./media/image161.png)

8.  On the **Choose data** page, select the **Select all** option and then click the **Next** button

    ![](./media/image162.png)

9.  On the **Settings** page, select **Full copy**, then click **Next** to proceed.

    ![](./media/image163.png)

    ![](./media/image164.png)

10. In the Review + save tab, deselect the **Start data transfer immediately** option and then select **Save** to continue.

    ![](./media/image165.png)

11. Select the **Run** option to start the pipeline and begin your data ingestion process. Running the pipeline initiates the data transfer from the source to the destination.

    ![](./media/image166.png)

12. On the **Save and run?** dialog box, click **Save and run** to execute these activities. This activity will take less than 1 min.

    ![](./media/image167.png)

    ![](./media/image168.png)

13. From the top menu, select the **silver_Lakehouse lakehouse** created in the previous task.

    ![](./media/image169.png)

14. The tables now appear in the **Tables** section

    ![](./media/image170.png)

15. On the left navigation, click on ***Data Factory-MedallionXX***, as shown in the image below.

    ![](./media/image171.png)

16. Now proceed to select and add an **Assign item** from the **Initial process** task.

    ![](./media/image172.png)

17. Select **createContosoTables** and then click **Select**

    ![](./media/image173.png)

18. An item is assigned to the **Initial process**.

    ![](./media/image174.png)

### Task 2: Configure a Variable for File Directory

Here, you enhance pipeline flexibility by creating a variable called fileDirectory that dynamically builds a folder path based on the pipeline’s run date. This variable ensures that files are always written into date-partitioned folders (yyyy/MM/dd). Assigning such variables improves automation and modularity within pipelines, making maintenance and scaling easier.

1.  Select the **New item** option on the **Initial process** from your task flow to add another storage item to project.

    ![](./media/image175.png)

2.  On the **New item** window, the available options within Microsoft Fabric have been filtered down to **Recommended items**. Select the **Pipeline** item.

    ![](./media/image176.png)

3.  On the New pipeline window, set the data pipeline name to +++ContosoTables+++ and then select **Create**

    ![](./media/image177.png)

    ![](./media/image178.png)

    **Note:** If prompted with the **Copy data assistant** window, select **X** in the top right corner to be taken into an empty authoring canvas. This ensures that start with a clean slate for pipeline configuration.

    ![](./media/image179.png)

    ![](./media/image180.png)

4.  Select the **Activities** tab and then the **Set variable** activity to add this to your canvas. The Set variable activity allows us to define and assign values to variables that can be used throughout pipeline.

    ![](./media/image181.png)

5.  With the Set variable activity selected, navigate to the **Settings** tab. Next to the Name property, select **New** to create a variable that will be used within the pipeline.

    ![](./media/image182.png)

6.  On the **Add new variable** pane, set the **Name** value to +++fileDirectory+++ and ensure the Type remains as a string before selecting **Confirm**.

    ![](./media/image183.png)

7.  Select the **Value** text input box. This will display the **Add dynamic content** \[Alt+Shift+D\] property. Select this text to open the pipeline expression builder.

    ![](./media/image184.png)

8.  On the Pipeline expression builder page, **copy and paste** the code block below into the expression input box. Press **Ok** when complete.

    ```
    @concat(
        'ContosoSales\',
        formatDateTime(
            convertFromUtc(
                utcnow(), 'Central Standard Time'
            ),
            'yyyy/MM/dd'
        )
    )
    ```

    ![](./media/image185.png)

    ![](./media/image186.png)

9.  Navigate to the **General** tab with the Set variable activity selected. Update the **Name** field with the text +++Set file directory+++.

    ![](./media/image187.png)

    This step helps in identifying and managing the activity within your pipeline in subsequent steps, making it easier to understand its purpose and functionality.

### Task 3: Retrieve Metadata Using Dynamic Path

In this task, you add a Get Metadata activity to the pipeline, which retrieves details of files located in the dynamically generated directory path. By referencing the *fileDirectory* variable, the activity dynamically identifies child files in the Bronze Lakehouse. This setup ensures the pipeline can automatically discover and process incoming files without manual configuration

1.  Navigate to the **Activities** tab and select the **Get metadata** activity to add it to your canvas.

    ![](./media/image188.png)

    ![](./media/image189.png)

2.  On the **Settings** options, set the **Connection** to **bronze_Lakehouse** from the available connection options.

3.  Choose the **Files** option and then click on the **Directory** file path text input box. This will display the **Add dynamic content \[Alt+Shift+D\]**. Click on this text to open the pipeline expression builder.

    ![](./media/image190.png)

4.  On the Pipeline expression builder window, select the **Variables** option. Within the available variable list, select **fileDirectory** and then **OK**.

5.  This step ensures that the file path for the metadata retrieval is dynamically set based on the value of the fileDirectory variable.

    ![](./media/image191.png)

    ![](./media/image192.png)

6.  Select **Get metadata** activity, navigate to the **Settings** tab. From the **Field list** select **New**.

    ![](./media/image193.png)

7.  From the drop-down menu, configure the value as **Child items**. This ensures that the metadata activity retrieves information about the child items and their names within the specified directory.

    ![](./media/image194.png)

8.  Select the **Get Metadata** activity and update the **Name** field with the text +++Get items in folder+++.

9.  This step aids in identifying and managing the activity within the pipeline, enhancing clarity around its purpose and functionality.

    ![](./media/image195.png)

10. Create a conditional path by dragging and dropping the **On completion** option between the **Set file directory** activity and the **Get items in folder** activity.

    ![](./media/image196.png)

11. Select **Validate** on the **Home** tab to ensure there are no errors within the pipeline.

    ![](./media/image197.png)

    ![](./media/image198.png)

12. After validation, select **Run** to start the pipeline.

    ![](./media/image199.png)

    ![](./media/image200.png)

13. Both the Pipeline status and the Activity status should display status as **Succeeded**, indicating that the execution completed successfully.

    ![](./media/image201.png)

14. From the **'Get items in folder**' activity, select the last column labeled '**Output**' to review the activity's contents. This step verifies that the filenames from the directory have been correctly retrieved and included in the output.

    ![](./media/image202.png)

### Task 4: Implement conditional paths (Dimension vs Fact logic)

In this step, you implement business logic to differentiate how dimension and fact tables are processed. A ForEach loop iterates through all discovered files. If the file name starts with Dim, the pipeline overwrites the corresponding Silver table (since dimensions are refreshed completely). For fact tables, the pipeline appends new data (since facts grow incrementally). This conditional handling ensures the Silver layer is properly maintained and aligned with real-world data warehouse practices.

1.  Select the **Activities** tab and then the **ForEach** activity to add this to your canvas. This activity allows you to iterate over a collection of items, performing a set of actions for each item in the collection.

    ![](./media/image203.png)

2.  With the **ForEach** activity selected, navigate to the **General** tab and update the **Name** field with the text +++For each file+++.

    ![](./media/image204.png)

3.  Next, create a conditional path by dragging and dropping the **On success** option between the **Get items in folder activity** and the **For each file activity**.

    ![](./media/image205.png)

4.  Select **For each file activity**, navigate to the **Settings** tab. Select the **Items** text input box. This will display the **Add dynamic content \[Alt+Shift+D\]** property. Select this text to open the pipeline expression builder. The sequential order ensures that the items are processed one after another, maintaining the order of execution.

    ![](./media/image206.png)

5.  On the Pipeline expression builder page, select the **Activity outputs** section. Then, choose the **Get items in folder** output of **childItems**. The full option title is **Get items in folder childItems**. This step ensures that the ForEach activity iterates over the child items retrieved from the specified directory.

    ![](./media/image207.png)

6.  Select the **add** option on the **For each activity** and then select **Copy data**. This step will allow us to repeatedly execute the copy data activity for each item in the array.

    ![](./media/image208.png)

7.  Select the **Edit** option on the **For each activity** to drill into the nested authoring canvas. This step allows you to configure the activities that will be executed for each item in the collection.

    ![](./media/image209.png)

8.  Navigate to the **General** tab with the **Copy data activity** selected. Update the **Name** field with the text +++Copy tables+++.

    ![](./media/image210.png)

9.  With the **Copy data** activity selected, configure the following options in the **Source** tab. Once complete select the **Directory** text input box. This will display the **Add dynamic content \[Alt+Shift+D\]** property.

    | **Property** | **Value**                                                        |
    |--------------|------------------------------------------------------------------|
    | Connection   | Select the previously configured **bronze_Lakehouse** lakehouse. |
    | Root folder  | Files                                                            |
    | File path type | File path                                                    |
    | File format  | Parquet                                                          |

    ![](./media/image211.png)

10. Select this text to open the pipeline **Add dynamic content \[Alt+Shift+D\]** property expression builder.

    ![](./media/image212.png)

11. Select the variable **fileDirectory** within the expression builder's **Variables** section and **OK** to continue.

    ![](./media/image213.png)

12. Select the **File name** text input box. This will display the **Add dynamic content \[Alt+Shift+D\]** property. Select this text to open the pipeline expression builder.

    ![](./media/image214.png)

13. Select the item **For each file** within the expression builder's **ForEach iterator** section. Within the expression builder, add the suffix ".name" to access the name property of the current items array and then **OK** to continue once complete.

14. Copy and paste the code block below into the expression input box.

    +++@item().name+++

    ![](./media/image215.png)

15. With the **Copy data activity** selected, configure the following options in the **Destination** tab. Once complete select the Table text input box. This will display the Add dynamic content \[Alt+Shift+D\] property. Select this text to open the pipeline expression builder.

    | Property    | Value                                                            |
    |-------------|------------------------------------------------------------------|
    | Connection  | Select the previously configured **silver_Lakehouse** lakehouse. |
    | Destination | Tables                                                           |

    ![](./media/image216.png)

    ![](./media/image217.png)

16. In the **Pipeline expression builder**, select **Functions** and then choose **split** from the **String functions group** to divide each item name using the period delimiter **'.'**. From the resulting array, select the first item. Once complete, select '**OK**' to proceed

17. Copy and paste the code block below into the expression input box.

    +++@split(item().name, '.')[0]+++

    ![](./media/image218.png)

    ![](./media/image219.png)

18. On the **Destination** tab, select **Append** from the **Table action** values to display the **Add dynamic content \[Alt+Shift+D\]** property. Select this text to open the **Pipeline expression builder**.

    ![](./media/image220.png)

19. In the expression builder, we'll use the if condition from the logical **functions** group and the startswith function from the string functions group to determine if the string starts with the prefix of **Dim** for our dimension tables. If true, we'll set the value to **Overwrite**, if false **Append**. Once complete select **OK** to continue.

20. Copy and paste the code block below into the expression input box.

    ```
    @if(
        startswith(item().name, 'Dim'),
        'Overwrite',
        'Append'
    )
    ```

    ![](./media/image221.png)

    ![](./media/image222.png)

    ![](./media/image223.png)

21. Select the **Main canvas** option from the breadcrumb trail to return to the top level of pipeline.

    ![](./media/image224.png)

22. From the **Home** tab, select the **Validate** option to first confirm that there are no issues within the pipeline.

    ![](./media/image225.png)

    ![](./media/image226.png)

23. Once validated, select **Run** to start the ingestion from the data pipeline.

    ![](./media/image227.png)

24. Confirm by selecting **Save and run**.

    ![](./media/image228.png)

25. Running the pipeline initiates the data transfer, allowing you to see the results of your configuration in action within the output window.

    ![](./media/image229.png)

    ![](./media/image230.png)

    ![](./media/image231.png)

26. The data pipeline runs multiple activities sequentially, writing to the Silver data layer as delta parquet tables. v-Order optimized tables enhance performance, reduce storage costs, and support efficient querying and data updates—ideal for analytical workloads in Microsoft Fabric.

    ![](./media/image232.png)

## Exercise 3: Initial Gold Layer Preparation

With structured data in place, you turn your attention to curating datasets for business intelligence in the **Gold** layer, the destination for analytics and reporting. This phase involves transforming and loading data using Data Factory and Power Query Online, supported by Fabric’s scalable compute and storage architecture. You build Dataflow Gen2 to apply final transformations and configure pipelines to route data accurately. You also create a semantic model to define relationships across key tables, enabling intuitive reporting. The process concludes with a streamlined data ingestion setup and a Power BI dashboard, completing the end-to-end data lifecycle.

![](./media/image233.png)

### Task 1: Create Gold Warehouse

In this task, you create a dedicated Warehouse named gold_Warehouse that serves as the curated Gold layer. This warehouse is optimized for analytics and reporting, offering a structured and performant environment for business intelligence workloads.

1.  On the left navigation, click on ***Data Factory-MedallionXX***, as shown in the image below.

    ![](./media/image234.png)

    ![](./media/image235.png)

2.  Select the **New item** option on the **Gold data** from task flow to add another storage item.

    ![](./media/image236.png)

3.  On the **Create an item** tab, select **Warehouse**.

    ![](./media/image237.png)

4.  On the New **warehouse** window, set the warehouse name to +++gold_Warehouse+++ and then select **Create**.

    ![](./media/image238.png)

### Task 2: From Silver to Gold: Persisting Mirrored SQL Tables

In this task, you build a copy job named *Copydata_silver to gold* to copy all structured Silver Lakehouse tables into the Gold Warehouse. This step ensures persistence, centralization, and optimized availability of refined data for analytics.

1.  From the **Home** tab of the warehouse, select **Get data** from **Ingest data with Copy job**.

    ![](./media/image239.png)

2.  On the **New copy job** window, set the copy job name to +++Copydata_silver to gold+++ and then select **Create**

    ![](./media/image240.png)

3.  On the **Copy job** window, select **OneLake catalog** and then select **silver_Lakehouse** lakehouse

    ![](./media/image241.png)

4.  From the **Copy job** page, select all tables and then click the **Next** button

    ![](./media/image242.png)

5.  Click **Next** again.

    ![](./media/image243.png)

6.  Click **Next** on the **Map to destination** tab.

    ![](./media/image244.png)

7.  On the **Review + save** tab, click **Save + Run**

    ![](./media/image245.png)

    ![](./media/image246.png)

    ![](./media/image247.png)

8.  On the top navigation menu, navigate and click on ***gold_Warehouse***, as shown in the below image.

    ![](./media/image248.png)

9.  The tables now appear in the **dbo** section

    ![](./media/image249.png)

### Task 3: Create initial Dataflow Gen2 structure

Here, you create a Dataflow Gen2 object called *PrepContoso* using Power Query Online. This Dataflow is designed to apply transformations and prepare data for downstream analytics by leveraging Fabric’s staging architecture for large-scale compute

1.  On the left navigation, click on ***Data Factory-MedallionXX***, as shown in the image below.

    ![](./media/image250.png)

2.  From the task flow, select the **Further transform** task and click on the **New item** option. Within the Create an item pane display properties, change the toggle to the **All items** option.

    ![](./media/image251.png)

3.  From the list of available items, select the **Dataflow Gen2** item

    ![](./media/image252.png)

4.  On the **New Dataflow Gen2** window, click on **Dataflow1** and rename it to +++PrepContoso+++

    ![](./media/image253.png)

    ![](./media/image254.png)

### Task 4: Connecting to warehouse tables

In this task, you connect the Dataflow to the *gold_Warehouse* and select relevant tables (dimensions and facts).

1.  From the **Home** tab, select **Get data** and then the **More...** option.

    ![](./media/image255.png)

2.  On the **Get data** explorer's search bar, type +++gold_Warehouse+++ to locate the gold Warehouse item. Select the **gold_Warehouse** item within the OneLake catalog's returned results.

    ![](./media/image256.png)

3.  From the Get data table navigator, select the tables listed below to perform data transformation operations and merge the tables for our downstream business intelligence projects.

    | **Table Name**                 |
    |--------------------------------|
    | dbo_Dimension_City             |
    | dbo_Dimension_Customer         |
    | dbo_Dimension_Date             |
    | dbo_Dimension_Employee         |
    | dbo_Dimension_Payment_Method   |
    | dbo_Dimension_Supplier         |
    | dbo_Dimension_Transaction_Type |
    | dbo_Fact_Order                 |
    | dbo_Fact_Purchase              |
    | dbo_Fact_Sale                  |
    | dbo_Fact_Stock_Holding         |
    | dbo_Fact_Transaction           |

    ![](./media/image257.png)

    ![](./media/image258.png)

4.  Select the **Fact_Sale** table and from the Home tab, navigate to the **Merge queries** option and select **Merge queries as new**.

    Note that there are two options: Merge queries, which will merge a table with the existing query, or Merge queries as new, which will create a new query in your editor. This step allows you to combine data from different tables, enhancing the comprehensiveness of your dataset.

    ![](./media/image259.png)

5.  From the Merge query window, set the **Right table for merge** to **dbo_Fact_Order**. In the top right corner, select the lightbulb which has detected a possible column match. In this example, both tables contain a column titled **City Key**. Select this option to set the columns to be merged on. For the join kind, select **Inner** and then **OK** to proceed. This activity ensures that the data is accurately combined based on matching columns.

    ![](./media/image260.png)

    ![](./media/image261.png)

    ![](./media/image262.png)

6.  Navigate to the far right for the **Merge** table and select the joined **dbo_Fact_Orders** table column's top right corner to expand the table, from the available column selections deselect **City Key** and **Stock Item Key** since this column is what we used to merge on and already exists in the dataset before selecting **OK** to continue

    ![](./media/image263.png)

    ![](./media/image264.png)

7.  Select the **Merge** query and from the **Home** tab, select **Add data destination** and then choose the **Warehouse** option.

    ![](./media/image265.png)

8.  On the **Connect to data destination** dialog box, your connection should already be selected. Select **Next** to continue.

    ![](./media/image266.png)

9.  On the **Choose destination target** dialog, browse to the warehouse where you wish to load the data and name the new table, then select **Next** again.

    ![](./media/image267.png)

10. On the **Choose destination settings** dialog, you can use the automatic settings or deselect the automatic settings and leave the default **Replace** update method, double check that your columns are mapped correctly, and select **Save settings**.

    ![](./media/image268.png)

    ![](./media/image269.png)

11. On the Home window, select **Save & run** and click on **Save & run** button

    ![](./media/image270.png)

12. On the left navigation, select ***Data Factory-MedallionXX***, as shown in the image below.

    ![](./media/image271.png)

13. On the Fabric workspace, select the more options ellipsis icon next to the **PrepContoso** dataflow and select **Check validation**.

    ![](./media/image272.png)

    ![](./media/image273.png)

14. On the top menu, select **gold_Warehouse** that you have created in the previous task.

    ![](./media/image274.png)

### Task 5: Build a report (optional)

In this task, you build a Power BI Report.

1.  On gold_Warehouse page, select the **Home** tab and then select **New semantic model**.

    ![](./media/image275.png)

2.  On the *New Semantic Model* tab, enter the name as +++Sample model+++, select the **dbo** schema, choose the below tables, and then click **Confirm**

    | **Table Name**                 |
    |--------------------------------|
    | dbo_Dimension_City             |
    | dbo_Dimension_Customer         |
    | dbo_Dimension_Date             |
    | dbo_Dimension_Employee         |
    | dbo_Dimension_Payment_Method   |
    | dbo_Dimension_Supplier         |
    | dbo_Dimension_Transaction_Type |
    | dbo_Fact_Order                 |
    | dbo_Fact_Purchase              |
    | dbo_Fact_Sale                  |
    | dbo_Fact_Stock_Holding         |
    | dbo_Fact_Transaction           |

    ![](./media/image276.png)

3.  On the left navigation, select ***Data Factory-MedallionXX***, and then select ***Sample model***.

    ![](./media/image277.png)

4.  On the sample model home page, click **Open Semantic Model** to access the model.

    ![](./media/image278.png)

    ![](./media/image279.png)

5.  Ensure from the top-right corner that the data model designer is selected in the **Editing** mode. This should change the drop-down text to “Editing”.

    ![](./media/image280.png)

    ![](./media/image281.png)

6.  From the **dbo_fact_sale** table, drag the **CityKey** field and drop it on the **CityKey** field in the **dbo_dimension_city** table to create a relationship. The **Create Relationship** dialog box appears.

    ![](./media/image282.png)

    In the **Create Relationship** dialog box:

    - **Table 1** is populated with **dbo_fact_sale** and the column of **CityKey**.

    - **Table 2** is populated with **dbo_dimension_city** and the column of **CityKey**.

    - Cardinality: **Many to one (\*:1)**

    - Cross filter direction: **Single**

    - Leave the box next to **Make this relationship active** selected.

    - Select the box next to **Assume referential integrity**.

    - Select **Save**.

    ![](./media/image283.png)

    ![](./media/image284.png)

7.  Next, add a relationship with the same **Create Relationship** settings as shown above, but with the following tables and columns:

    - **Salespersonkey(dbo_Fact_Sale)** - **EmployeeKey(dbo_Dimension_Employee)**

    ![](./media/image285.png)

    ![](./media/image286.png)

8.  Create a relationship between the following tables and columns by using the same steps:

    - **CustomerKey(dbo_Fact_Sale)** - **CustomerKey(dbo_Dimension_Customer)**

    ![](./media/image287.png)

    ![](./media/image288.png)

    ![](./media/image289.png)

9.  Create a relationship between the following tables and columns by using the same steps:

    - **InvoiceDateKey(dbo_Fact_Sale)** - **Date(dbo_Dimension_Date)**

    ![](./media/image290.png)

    ![](./media/image291.png)

    ![](./media/image292.png)

10. After you add these relationships, your data model is ready for reporting.

11. From the top ribbon, select **File** and select **Create new report** to start creating reports/dashboards in Power BI.

    ![](./media/image293.png)

    ![](./media/image294.png)

12. On the **Data** pane, expand **dbo_fact_sales** and check the box next to **Profit**. This selection creates a column chart and adds the field to the Y-axis.

    ![](./media/image295.png)

13. With the bar chart selected, select the **Card** visual in the visualization pane.

    ![](./media/image296.png)

    ![](./media/image297.png)

14. Click anywhere on the blank canvas (or press the Esc key) so the Card that we just placed is no longer selected.

15. Add a stacked area chart: on the **Visualizations** pane, select the **Stacked area chart** visual.

16. On the **Data** pane, expand **Fact_Sales** and check the box next to **Profit**. This selection creates a column chart and adds the field to the Y-axis.

17. Expand **Dimension_Date** and check the box for **FiscalMonthNumber**. This selection adds the field to the X-axis. This selection creates a filled line chart showing profit by fiscal month.

    ![](./media/image298.png)

18. On the **Data** pane, expand **Dimension_Stock_Item** and drag **BuyingPackage** into the Legend field well. This selection adds a line for each of the Buying Packages

    ![](./media/image299.png)

    ![](./media/image300.png)

19. With the bar chart selected, select the **Clustered bar chart** visual in the visualization pane. This selection converts the column chart into a bar chart.

20. On the **Data** pane, expand **Fact_Sales** and check the box next to **Profit**. This selection creates a column chart and adds the field to the Y-axis.

21. Expand **Dimension_Employee** and check the box for **Employee**. This selection adds the field to the Y-axis.

    ![](./media/image301.png)

22. Click anywhere on the blank canvas (or press the Esc key) so the chart is no longer selected.

23. From the ribbon, select **File** \> **Save**.

    ![](./media/image302.png)

24. Enter the name of your report as +++Profit Reporting+++. Select **Save**.

    ![](./media/image303.png)

    ![](./media/image304.png)

25. On the left navigation, select ***Data Factory-MedallionXX***, as shown in the image below.

    ![](./media/image305.png)

26. From the task flow, select the **Data visualize** task and click on the paper clip icon to assign a previously created item.

    ![](./media/image306.png)

27. Select the **Profit Reporting** item and then press **Select**.

    ![](./media/image307.png)

    ![](./media/image308.png)

### Task 6: Clean up resources

1.  From the top-right corner of the Fabric Workspace page, select **Workspace settings**. If you do not see this option, select the **...** option at the top right of the page and then select **Workspace settings**.

    ![](./media/image309.png)

2.  Select **General** and then select **Remove this workspace**.

    ![](./media/image310.png)

3.  Click **Delete** to delete the workspace.

    ![](./media/image311.png)

**Summary**

This usecase demonstrates how to implement the **Medallion Architecture** in Microsoft Fabric by building an end-to-end data lifecycle across Bronze, Silver and Gold layers. In the **Bronze layer**, raw data from Azure SQL and ContosoSales files is ingested into a Lakehouse using pipelines and mirrored databases. The **Silver layer** transforms and structures this raw data into dimension and fact tables with dynamic pipelines, metadata-driven discovery, and conditional logic to handle overwrites for dimensions and appends for facts. Finally, the **Gold layer** curates business-ready datasets by persisting Silver tables into a warehouse and applying further transformations through Dataflow Gen2 and Power Query, producing enriched entities like DimCustomers and Fact Sales for analytics. Finally, the workspace resources are cleaned up to maintain cost efficiency.
