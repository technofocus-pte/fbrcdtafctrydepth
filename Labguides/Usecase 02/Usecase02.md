---
lab:
  title: 'Use Case 2: Standalone Dataflow and Deployment Components'
  description: In this lab, you explored the full lifecycle of building, automating, and deploying data solutions in Microsoft Fabric using Dataflow Gen2, pipelines, deployment pipelines, and Git-based CI/CD integration. You began by creating a Fabric workspace, ingesting sample NYC Taxi data into a lakehouse, and building a Dataflow Gen2 with transformations, merges, parameters, and automated refreshes. Next, you advanced to pipelines, configuring copy jobs, building parameterized templates, integrating Apache Airflow, scheduling runs, and monitoring execution results. Variable Libraries were then introduced to dynamically manage environment-specific values for Dev, Test, and Prod, enhancing pipeline flexibility and deployment readiness. The lab continued with connecting Fabric to GitHub, creating repositories and tokens, and synchronizing workspaces for version control, laying the foundation for CI/CD workflows. You further extended Git integration by setting up Azure DevOps projects, linking them to Fabric workspaces, and practicing pull request–based branching strategies. Finally, you implemented deployment pipelines to move content across Development, Test, and Production stages in a controlled, versioned, and auditable process. Through these exercises, you gained hands-on experience in data ingestion, transformation, and preparation with Dataflow Gen2, orchestration and automation with pipelines and Airflow, flexible deployments using variable libraries, enterprise-grade CI/CD with GitHub and Azure DevOps, and controlled release management via deployment pipelines. Collectively, the lab provided a comprehensive understanding of how Microsoft Fabric supports modern data engineering, automation, and DevOps practices—from raw ingestion to production deployment.
  duration: 5 minutes
  level: 400
  islab: true
  primarytopics:
    - Azure
    - Azure DevOps
    - GitHub
    - Microsoft Fabric
---
# Use Case 2: Standalone Dataflow and Deployment Components

## Scenario: Contoso Retail

**Contoso Retail** is a multinational retail and distribution company specializing in electronics, apparel, and home goods. With a vast network of stores and a thriving e-commerce platform, Contoso generates massive volumes of transactional, operational, and promotional data every day.

Contoso decided to modernize its data platform using Microsoft Fabric’s Data Factory capabilities. The company aimed to build a unified, automated, and scalable solution using Dataflow Gen2, Pipelines, and deployment components.

As part of its data platform modernization initiative, Contoso identified the need to incorporate external urban mobility data to enhance its location strategy, customer behavior analysis, and logistics optimization. To achieve this, the company selected the NYC Taxi dataset—a rich, publicly available dataset capturing millions of taxi trips across New York City—as the core dataset for its pilot implementation.

You, as a Data Engineer at Contoso Retail, were tasked with setting up the entire flow—from data ingestion and transformation, to monitoring, parameterization, and automated deployment across multiple environments. You collaborated with the solution architect to build a data transformation solution with the following objectives.

- Ingest raw NYC Taxi data and discount files into a Lakehouse

- Transform and merge datasets using Dataflow Gen2 and Power Query

- Automate refreshes and orchestrate workflows using Pipelines

- Enable dynamic deployments using Variable Libraries

- Integrate GitHub and Azure DevOps for CI/CD and version control

- Promote content across Dev, Test, and Prod using Deployment Pipelines

## Exercise 1: Dataflow Gen2 Using Sample Data

In this exercise, you begin by setting up the foundational data architecture in Microsoft Fabric. You create a dedicated workspace and lakehouse, ingest NYC Taxi datasets, and build Dataflow Gen2 to automate data ingestion and transformation. You apply Power Query steps to clean, filter, merge, and enrich the data, preparing it for downstream analytics and reporting.

### Task 1: Create a Fabric workspace

In this task, you create a Fabric workspace. The workspace contains all the items needed for this lakehouse tutorial, which includes lakehouse, dataflows, Data Factory pipelines, notebooks, Power BI datasets, and reports.

1.  Open your browser, navigate to the address bar, and type or paste the following URL: +++https://app.fabric.microsoft.com/+++ then press the **Enter** button.

2.  On the **Microsoft Fabric** sign-in page, enter your credentials, and click on the **Submit** button.

    | | |
    |----|----|
    | Username | +++@lab.CloudPortalCredential(User1).Username+++ |
    | Password | +++@lab.CloudPortalCredential(User1).Password+++ |

    ![](./media/image1.png)

3.  Enter your password and click **Sign in**.

    ![](./media/image2.png)

4.  On **Stay signed in?** page, click **Yes**.

    ![](./media/image3.png)

5.  You'll be directed to Fabric Home page.

    ![](./media/image4.png)

6.  On the **Home** page click on **+ New Workspaces** as shown in the below image. This creates a dedicated workspace where you can organize all Fabric items.

    ![](./media/image5.png)

7.  On the **Create a workspace** pane that appears on the right side, enter the following details, and click **Apply**.

    | **Setting** | **Value** |
    |----|----|
    | **Name**                   | +++Data Factory-XX+++ (XX can be a unique number, you can add more numbers) |
    | **Advanced**               | Select Fabric                                                                     |
    | **Default storage format** | **Small dataset storage format**                                                  |

    ![](./media/image6.png)

    ![](./media/image7.png)

8.  The Workspace is now created.

    ![](./media/image8.png)

### Task 2: Create a lakehouse and Ingest sample data

In this task, you set up a lakehouse and ingest the NYC Taxi sample data along with additional CSV files. This establishes your raw dataset foundation inside Fabric, enabling you to start transformations and queries later.

1.  Create a new lakehouse by clicking on the **+New item** button in the navigation bar.

    ![](./media/image9.png)

2.  On the **Filter by item type** search box, enter +++Lakehouse+++ and select the lakehouse item.

    ![](./media/image10.png)

3.  On the **New lakehouse** dialog box, enter +++datafactory_lakehouse+++ in the **Name** field, click on the **Create** button and open the new lakehouse.

    **Note:** Ensure to remove space before **datafactory_lakehouse**.

    ![](./media/image11.png)

    ![](./media/image12.png)

4.  Wait for the notification stating **Successfully created SQL endpoint**.

    ![](./media/image13.png)

5.  From the **lakehouse** home page, select **Upload files**

    ![](./media/image14.png)

6.  On the Upload files tab, click on the **folder** under the Files

    ![](./media/image15.png)

7.  Browse to [https://github.com/technofocus-pte/Fabric-Labfiles/tree/main/Labfile](https://github.com/technofocus-pte/Fabric-Labfiles/tree/main/Labfile), download the **NYCTaxi.zip** file to your VM, extract it, select all the extracted files, and then click **Open**.

8.  Click and select refresh on the **Files**. The file appears.

    ![](./media/image16.png)

9.  **Close** the Upload files pane.

    ![](./media/image17.png)

10. Click and select refresh on the **Files**. The file appears.

    ![](./media/image18.png)

11. In the **Lakehouse** page, navigate and click on **Analyze data with** drop in the command bar, then select **Notebook \> New notebook**

    ![](./media/image19.png)

12. Update the code in the **cell** with the following code and click on **▷ Run cell** that appears to the left of the cell upon hover.

    ```python
    # Mapping of parquet files to table names
    file_table_mapping = {
        "Files/part-00000-29741df6-8120-43d8-87b9-cde998ce283d-c000.snappy.parquet": "green_tripdata_2020",
        "Files/part-00000-8601e1f7-764e-4adb-908e-55bb04c295bf-c000.snappy.parquet": "green_tripdata_2021",
        "Files/part-00000-90045a1e-33b0-441f-a499-ad290b5d7c1f-c000.snappy.parquet": "green_tripdata_2019",
        "Files/part-00000-907cea6d-0f54-4639-9a14-042dc04185ef-c000.snappy.parquet": "green_tripdata_2017",
        "Files/part-00000-93b796dc-08a7-425c-a8a7-f91632812ae9-c000.snappy.parquet": "green_tripdata_2022",
        "Files/part-00000-d0796a0d-acb6-427f-a974-ccad81f964da-c000.snappy.parquet": "green_tripdata_2018"
    }

    # Loop through all mappings
    for file_path, table_name in file_table_mapping.items():
        print(f"Loading {file_path} -> {table_name}")

        # Read parquet file
        df = spark.read.parquet(file_path)

        # Write as Delta Lakehouse table
        df.write.format("delta") \
            .mode("overwrite") \
            .saveAsTable(table_name)

        print(f"Created table: {table_name}")

    print("All parquet files loaded successfully.")
    ```

    ![](./media/image20.png)

    ![](./media/image21.png)

13. After executing the notebook, refresh the **Tables** section in the Lakehouse Explorer to view the newly created tables from the Parquet files.

    ![](./media/image22.png)

    ![](./media/image23.png)

14. From the left menu, select workspace icon and then select **datafactory_lakehouse** lakehouse name.

    ![](./media/image24.png)

    ![](./media/image25.png)

15. On the **Lakehouse** page, under the Explorer pane, select **Files**. Now, hover your mouse to **NYC-Taxi-Green-Discounts.csv** file. Click on the horizontal ellipses **(…)** beside **NYC-Taxi-Green-Discounts**.csv. Navigate and click on **Load Table**, then select **New table**.

    ![](./media/image26.png)

    ![](./media/image27.png)

16. On the **Load file to new table** dialog box, and click on the **Load** button.

    ![](./media/image28.png)

    ![](./media/image29.png)

    ![](./media/image30.png)

### Task 3: Create new Dataflow Gen2 with sample data

In this task, you build a new Dataflow Gen2 object that connects to the lakehouse tables. Dataflows allow you to automate ingestion, transformation, and preparation of data for downstream analytics.

1.  From the left navigation select ***Data Factory-XX***, as shown in the image below.

    ![](./media/image31.png)

    ![](./media/image32.png)

2.  Create a new Dataflow Gen2 by clicking on the **+New item** button in the navigation bar. From the list of available items select the **Dataflow Gen2** item

    ![](./media/image33.png)

3.  On the **New Dataflow Gen2** dialog box, click on **Dataflow1** and rename it to +++NYC_Taxi_Dataflow+++

    ![](./media/image34.png)

    ![](./media/image35.png)

4.  From the **Home** tab, select **Get data** and then the **More...** option to upload the tables into Dataflow Gen2

    ![](./media/image36.png)

5.  Within the **Get data** explorer's search bar, type +++datafactory_lakehouse+++ to locate the lakehouse item. Select the **datafactory_lakehouse** item within the OneLake catalog's returned results.

    ![](./media/image37.png)

6.  Click on the **Connect** button.

    ![](./media/image38.png)

7.  From the Get data table navigator, select the tables listed below to perform data transformation operations and merge the tables for our downstream business intelligence projects.

    - green_tripdata_2022

    - nyc_taxi_green_discounts

8.  Click **Create**.

    ![](./media/image39.png)

    ![](./media/image40.png)

### Task 4: Implement Power Query transformations

This task demonstrates how to clean and shape your dataset using Power Query. You adjust data types, filter rows, unpivot columns, and calculate new fields like discounts. These transformations prepare the data for meaningful analysis.

1.  Select the **green_tripdata_2022** table and from the **Home** tab, select the data type icon in the column header of the second column, **IpepPickupDatetime**, to display a dropdown menu and select the data type from the menu to convert the column from the **Date/Time** to **Date** type.

    ![](./media/image41.png)

    ![](./media/image42.png)

2.  Select the **storeAndFwdFlag** column's filter and sort dropdown menu. (If you see a warning **List may be incomplete**, select **Load more** to see all the data.)

    ![](./media/image43.png)

3.  Select 'Y' to show only rows where a discount was applied, and then select **OK**.

    ![](./media/image44.png)

    ![](./media/image45.png)

4.  Select the **IpepPickupDatetime** column sort and filter dropdown menu, then select **Date filters**, and choose the **Between...** filter provided for Date and Date/Time types

    ![](./media/image46.png)

5.  On the **Filter rows** dialog box, select dates between January 1, 2022, and January 31, 2022, then select **OK**.

    ![](./media/image47.png)

    ![](./media/image48.png)

6.  Select the **nyc_taxi_green_discounts** table and from the **Home** tab, notice that the headers appear to be in the first row. Promote them to headers by selecting the table's context menu at the top left of the preview grid area to select **Use first row as headers**.

    ![](./media/image49.png)

    ![](./media/image50.png)

    **Note:** After you promote the headers, a new step that changes the data types of your columns is added to the **Applied steps** pane.

7.  Select the **nyc_taxi_green_discounts** table and from the **Home** tab, right-click the **VendorID** column, and from the context menu displayed, select the option **Unpivot other columns**. This allows you to transform columns into attribute-value pairs, where columns become rows.

    ![](./media/image51.png)

    ![](./media/image52.png)

8.  With the table unpivoted, rename the *Attribute* and *Value* columns using the formula bar. Replace the existing script with:

    +++Table.UnpivotOtherColumns(#"Changed column type", {"VendorID"}, "Date", "Discount")+++

    ![](./media/image53.png)

    ![](./media/image54.png)

    ![](./media/image55.png)

    **Note:** To rename the Attribute and Value columns, you can also double-click them and change 'Attribute' to 'Date' and 'Value' to 'Discount'.

    ![](./media/image56.png)

    ![](./media/image57.png)

9.  Change the data type of the Date column by selecting the data type menu to the left of the column name and choosing **Date**.

    ![](./media/image58.png)

10. Select the **Discount** column and then select the **Transform** tab on the menu. Select **Number column**, and then select **Standard** numeric transformations from the submenu, and choose **Divide**.

    ![](./media/image59.png)

11. On the **Divide** dialog box, enter the value 100.

    ![](./media/image60.png)

    ![](./media/image61.png)

### Task 5: Merging queries

In this task, you join multiple tables (trip data and discounts) to enrich your dataset. Merging helps consolidate related information into a single unified view for reporting and analytics.

1.  Select your original data query (in this example, **green_tripdata_2022**), and on the **Home** tab, Select the **Combine** menu and choose **Merge queries**, then **Merge queries as new**.

    ![](./media/image62.png)

2.  From the Merge query window, set the **Right table for merge** to **nyc-taxi-green-discounts**. On the top right corner, select the **lightbulb** which has detected a possible column match. In this example, both tables contain a column titled **VendorID**. Select this option to set the columns to be merged on. Click on **OK** to proceed. This activity ensures that the data is accurately combined based on matching columns.

    ![](./media/image63.png)

    ![](./media/image64.png)

    ![](./media/image65.png)

3.  Navigate to the far right of the **green_tripdata_2022** table. Select the expand icon on the joined **nyc_taxi_green_discounts** column. From the available column selections, deselect **VendorID**—since it was used as the merge key and already exists in the dataset—then click **OK** to continue.

    ![](./media/image66.png)

    ![](./media/image67.png)

4.  With the discount value at the row level, we can create a new column to calculate the total amount after discount. To do so, select the **Add column** tab at the top of the editor, and choose **Custom column** from the **General** group.

    ![](./media/image68.png)

5.  On the **Custom column** dialog, you can use the [Power Query formula language (also known as M)](https://learn.microsoft.com/en-us/powerquery-m) to define how your new column should be calculated. Enter +++TotalAfterDiscount+++ for the **New column name**, select **Currency** for the **Data type**, and provide the following M expression for the **Custom column formula**:

    ```
    if [tolls_amount] > 0 then [tolls_amount] * ( 1 - [Discount] ) else [tolls_amount]
    ```

    Then select **OK**.

    ![](./media/image69.png)

    ![](./media/image70.png)

6.  Select the newly created **TotalAfterDiscount** column and then select the **Add column** tab at the top of the editor window. On the **Number column** group, select the **Rounding** drop down and then choose **Round...**.

    ![](./media/image71.png)

7.  On the **Round** dialog box, enter 2 for the number of decimal places and then select **OK**.

    ![](./media/image72.png)

    ![](./media/image73.png)

8.  Change the data type of the **IpepPickupDatetime** from **Date** to **Date/Time**.

    ![](./media/image74.png)

    ![](./media/image75.png)

9.  Finally, expand the **Query settings** pane from the right side of the editor if it isn't already expanded, and rename the query from **Merge** to +++Output+++.

    ![](./media/image76.png)

### Task 6: Load the output query to a table in the Lakehouse

After transformations, you save the output query into the lakehouse as a structured table. This provides a curated dataset ready for analytics or reporting.

1.  Select the **Output** merge query that has been created previously. Then select the **Home** tab on the editor window, and **Add data destination** from the **Query** grouping, to select a **Lakehouse** destination.

    ![](./media/image77.png)

2.  On the **Connect to data destination** dialog box, your connection should already be selected. Select **Next** to continue.

    ![](./media/image78.png)

3.  On the **Choose destination target** dialog, browse to the Lakehouse where you wish to load the data and name the new table, then select **Next** again.

    ![](./media/image79.png)

4.  On the **Choose destination settings** dialog, you can use the automatic settings or deselect the automatic settings and leave the default **Replace** update method, double check that your columns are mapped correctly, and select **Save settings**.

    ![](./media/image80.png)

5.  On the main editor window, confirm that you see your output destination on the **Query settings** pane for the **Output** table under **Data destination**, and then select **Save**.

    ![](./media/image81.png)

### Task 7: Choose columns

In this task, you refine your dataset by selecting only the necessary columns. This improves performance, reduces clutter, and ensures downstream reports focus only on relevant data.

1.  On the Home tab, go to the **Output** table, and in the Manage columns group, select **Choose columns**

    ![](./media/image82.png)

2.  The **Choose columns** dialog appears, containing all the available columns in your table. You can select all the fields that you want to keep and remove specific fields by clearing their associated check box. For this example, you want to remove the **GUID** and **Report created by** columns, so you clear the check boxes for those fields.

    ![](./media/image83.png)

3.  After selecting **OK**, create a table that only contains the selected columns.

    ![](./media/image84.png)

### Task 8: Remove columns

In this task, you remove unnecessary columns using different techniques. This ensures the final dataset remains lean and tailored for business needs.

1.  When you select **Remove columns** from the **Home** tab, you have two options:

    - **Remove columns**: Removes the selected columns.

    - **Remove other columns**: Removes all columns from the table *except* the selected ones.

    ![](./media/image85.png)

2.  You can also select the columns you want to remove in the table, then select and hold (or right-click) the column and choose **Remove columns** in the shortcut menu. This method of removing columns is demonstrated in the next section.

    **Remove selected columns**

    Starting from the sample table, select the **passenger_count** and the **Report created** columns. Select and hold (or right-click) either of the selected column headings. A new shortcut menu appears, where you can select the **Remove columns** command.

    ![](./media/image86.png)

3.  After selecting **Remove columns**, you create a table that only contains the columns.

    ![](./media/image87.png)

### Task 9: Group and Aggregate Data by Column(s) in Power Query

In this task, you aggregate data by VendorID, pickup time, and fare amount. Grouping and aggregation generate summarized insights that can answer key business questions like total tips per vendor.

1.  Select **Output** and select **Group by** on the **Home** tab.

    ![](./media/image88.png)

2.  On the **Group by** settings page, enter the following details.

    - Select **Advanced** radio button.

    - Under **Group by** select the following:

        - **VendorID**

        - **Ipep_pickup_datetime**

        - **Fare_amount**

3.  In **New column name**, enter +++Count+++. In **Operation**, select **Sum**, and in **Column**, select **tip_amount**. Click **Add aggregation** to add another aggregation.

4.  In **New column name**, enter +++File+++. In **Operation**, select **Sum**, and in **Column**, select **Source_file**.

5.  Click **OK**.

    ![](./media/image89.png)

    ![](./media/image90.png)

    ![](./media/image91.png)

### Task 10: Creating a Parameter and Using It in a Filter

This task introduces parameterization in Power Query. By creating dynamic filters (e.g., PUlocationID), you make your transformations flexible and reusable across environments or scenario

1.  On the Home tab, select **green_tripdata_2022**

2.  On the **Power Query Home** tab, select **Manage Parameters**, then choose **New Parameter.**

    ![](./media/image92.png)

3.  On the **Manage Parameters** dialog, enter the following details: set **Name** to +++PUlocationID+++, **Type** to **Decimal Number**, and **Current Value** to +++74+++, and then click **OK** to save the parameter.

    ![](./media/image93.png)

    ![](./media/image94.png)

4.  Select the **PUlocationID** column sort and filter dropdown menu, then select **Number filters**, and choose **Equals...**.

    ![](./media/image95.png)

5.  On the **Filter rows** dialog, click the pencil icon next to the value field, then select **Select a parameter** to dynamically filter rows based on a predefined parameter.

    ![](./media/image96.png)

6.  Choose the parameter **PUlocationID** from the dropdown list to filter rows dynamically based on the selected parameter value. Click on **Ok** button

    ![](./media/image97.png)

7.  The **green_tripdata_2022** query now shows only the rows with the **PUlocationID** value that you specified.

    ![](./media/image98.png)

8.  To modify the parameters, select the **PUlocationID** parameter, enter +++244+++ as the **Current Value**, and then click the **Apply** button

    ![](./media/image99.png)

    ![](./media/image100.png)

9.  In the **Queries** pane, select **green_tripdata_2022**.

10. Select the **PUlocationID** column sort and filter dropdown menu, then select **Number filters**, and choose **Greater than...**.

    ![](./media/image101.png)

11. In the **Filter rows** dialog, click the pencil icon next to the value field, then click **Select a parameter** and select it. Click **Ok**

    ![](./media/image102.png)

    ![](./media/image103.png)

12. On the Home window, select **Save & run** and click on **Save & run** button

    ![](./media/image104.png)

    ![](./media/image105.png)

13. On the left navigation select ***Data Factory-XX***, as shown in the image below.

    ![](./media/image106.png)

**Optional: Link parameters to other queries**

You can use the same parameter to filter related tables, such as **nyc_taxi_green_discounts**. This helps create **interactive reports** where users can select a value and see related data.

### Task 11: Schedule a refresh

Finally, you automate the refresh of your dataflow on a recurring schedule. This ensures your curated datasets remain updated without manual intervention.

1.  On the Fabric workspace, select the more options ellipsis icon next to the dataflow and select **Schedule**.

    ![](./media/image107.png)

2.  Configure the dataflow refresh schedule to run **once daily at 8:00 AM until the end of the year**. This frequency is more typical for production environments, balancing data freshness with system performance and resource efficiency.

    ![](./media/image108.png)

3.  Select **Close**

    ![](./media/image109.png)

4.  To check the status, select the ellipsis icon next to the dataflow, and then select **Recent runs**.

    ![](./media/image110.png)

    ![](./media/image111.png)

    **Note:** You can view the **Recent runs** directly within the Dataflow editor—located just after the **Save and run** and **Check validation** options. This allows you to monitor execution history without leaving the Dataflow interface

    ![](./media/image112.png)

    ![](./media/image113.png)

## Exercise 2: Pipeline Templates and Monitoring

In this exercise, you design and orchestrate data movement using Fabric Pipelines. You configure copy jobs to move curated data from lakehouse to warehouse, and build parameterized pipeline templates for reusability. You also monitor pipeline executions to ensure reliability, performance, and traceability.

### Task 1: Copy job into the warehouse

You configure a copy job to move tables from the lakehouse into a dedicated warehouse. This step demonstrates how to stage curated data into a high-performance environment for analytics.

1.  On ***Data Factory-XX*** workspace page, navigate and click on **+ New item** button and select the **Warehouse** tile.

    ![](./media/image114.png)

2.  On the **New warehouse** dialog box, enter +++dataFactory_warehouse+++ in the **Name** field, click on the **Create** button and open the new warehouse.

    ![](./media/image115.png)

    ![](./media/image116.png)

3.  On the left navigation, select ***Data Factory-XX***, as shown in the image below.

    ![](./media/image117.png)

4.  On workspace page, click **New item** and select the **Copy job** tile.

    ![](./media/image118.png)

5.  On the New copy job window, set the copy job name to +++CopyNYCTaxi+++ and then select **Create**.

    ![](./media/image119.png)

6.  In the **Copy job** window, on the **OneLake** tab, select **datafactory_lakehouse**.

    ![](./media/image120.png)

7.  Now that you're on the **Choose data** step, select the following tables:

    - green_tripdata_2017

    - green_tripdata_2019

    - nyc_taxi_green_discount

    ![](./media/image121.png)

8.  On the Choose data destination tab, search for +++dataFactory_warehouse+++ in the search bar and then select **datafactory_warehouse** from the OneLake catalog list.

    ![](./media/image122.png)

9.  On the **Settings** step, review the Copy job mode options and then select **Next** to continue.

    ![](./media/image123.png)

10. On the **Map to destination** tab, you can review the added tables and update the schema or table names. Once reviewed, select **Next** to continue.

    ![](./media/image124.png)

11. On the **Review + save** step, deselect the **Start data transfer immediately** option and then select **Save** to continue.

    ![](./media/image125.png)

    ![](./media/image126.png)

12. Now that you've reviewed the configuration, from the **Home** tab, select the **Run** option to begin copying data from the lakehouse into the warehouse.

    ![](./media/image127.png)

    ![](./media/image128.png)

13. You can monitor the copy job in the **Results** tab at the bottom to confirm that it has successfully completed.

    ![](./media/image129.png)

14. Hover over the copy job on the left side-rail and select the **X** to close it.

15. On the top menu, select **datafactory_warehouse**, as shown in the image below.

    ![](./media/image130.png)

16. To verify the tables in the warehouse, navigate to the warehouse explorer and review the list of tables

    ![](./media/image131.png)

17. On the left navigation, select **Data Factory-XX**, as shown in the image below.

    ![](./media/image132.png)

### Task 2: Create parameterized pipeline template

In this task, you design a reusable pipeline template with parameters. Parameterization makes pipelines flexible and reusable across multiple datasets, environments, or scenarios.

1.  Select the **New item** option on the workspace page. Select **Pipeline** from the new item flyout menu.

    ![](./media/image133.png)

2.  Provide a Pipeline Name as +++sample_pipeline+++ and then select **Create**.

    ![](./media/image134.png)

### Task 3: Use a Copy activity in the pipeline to load sample data to a data Lakehouse

In this task you configure a pipeline activity of moving and transforming a sample data (Public Holidays dataset) into a lakehouse.

1.  From the **sample_pipeline**  pipeline page, drop down the **Copy data** and select the **Add copy data activity** to copy the sample data.

    ![](./media/image135.png)

2.  The properties section below provides access to the configurations for Source, Destination, Settings, and more. These configurations can be edited directly to ensure the data copy activity is correctly set up and aligned with specific requirements.

    ![](./media/image136.png)

3.  Click the **Source** tab, open the **Connection** drop-down menu, and select **Browse all**.

    ![](./media/image137.png)

4.  You will be landed on the **Copy data** page. On the **Choose data source** tab, select **Sample data** section and then select the **Public Holidays** data source type.

    ![](./media/image138.png)

5.  A preview of the data source will now be displayed to verify the correct selection and understand the data structure. After reviewing the preview, select **OK** to proceed.

    ![](./media/image139.png)

6.  Select the **Destination** tab

    ![](./media/image140.png)

7.  In the **Destination** tab, open the **Connection** drop-down menu, and select **Browse all**.

    ![](./media/image141.png)

8.  Select the **datafactory_lakehouse** lakehouse item as the data destination from the **OneLake catalog list**. It determines the storage location for the data

    ![](./media/image142.png)

9.  On the **Destination** tab of the **Copy data** activity, click **+New** to create a new destination table for loading the data.

    ![](./media/image143.png)

10. Enter the table name as +++holidaydatacontainer+++ and click on **Create**

    ![](./media/image144.png)

### Task 4: Run and view the results of your Copy activity

1.  Select the **Run** option to start the pipeline and begin your data ingestion process. Running the pipeline initiates the data transfer from the source to the destination.

    ![](./media/image145.png)

2.  On the **Save and run?** dialog box, click **Save and run** to execute these activities. This activity will take less than 1 min.

    ![](./media/image146.png)

    ![](./media/image147.png)

    ![](./media/image148.png)

3.  You can monitor the run and check the results on the **Output** tab below the pipeline canvas. Select the **activity name** to view the run details.

    ![](./media/image149.png)

4.  The run details show rows Data read and Data written.

    ![](./media/image150.png)

5.  Expand the **Duration breakdown** section to see the duration of each stage of the Copy activity. After reviewing the copy details, select **Close**.

    ![](./media/image151.png)

### Task 5: Add an Office 365 Outlook activity

1.  Select the **Activities** tab in the pipeline editor and find the **Office 365 Outlook** activity.

    ![](./media/image152.png)

2.  Select and drag the **On success path** (a green checkbox on the top right side of the activity in the pipeline canvas) from your Copy activity to your new Office 365 Outlook activity.

    ![](./media/image153.png)

3.  Select the Office 365 Outlook activity from the pipeline canvas, then select the **Settings** tab of the property area below the canvas to configure the email. Click the **Connection** drop-down and select **Browse all**.

    ![](./media/image154.png)

4.  On the **Choose a data source** window, select **Office 365 Email** source.

    ![](./media/image155.png)

5.  Sign in with the account from which you want to send the email. You can use the existing connection with the already signed in account.

    ![](./media/image156.png)

    ![](./media/image157.png)

6.  Click on **Connect** to proceed.

    ![](./media/image158.png)

7.  Select the Office 365 Outlook activity on the pipeline canvas, and then on the **Settings** tab below the canvas, enter your email address in the **To** box. To use several addresses, separate them with **;**.

    ![](./media/image159.png)

8.  For the **Subject**, select the field so that the **Add dynamic content** option appears, and then select it to open the pipeline expression builder. Enter the following expression, and then select **OK**:

    +++@concat('DI in an Hour Pipeline Succeeded with Pipeline Run Id', pipeline().RunId)+++

    ![](./media/image160.png)

9.  For the **Body**, select the field again and choose the **View in expression builder** option when it appears below the text area. Add the following expression again in the **Pipeline expression builder** dialog that appears, then select **OK**:

    +++@concat('RunID = ', pipeline().RunId, ' ; ', 'Data written (bytes) = ', activity('Copy data1').output.dataWritten, ' ; ','Throughput ', activity('Copy data1').output.throughput)+++

    **Note:** Replace **Copy data1** with the name of your own Copy activity (for example, **Copy_2ph**).

    ![](./media/image161.png)

    ![](./media/image162.png)

10. Finally, select the **Home** tab at the top of the pipeline editor and choose **Run**. Then select **Save and run** on the confirmation dialog to execute these activities.

    ![](./media/image163.png)

    ![](./media/image164.png)

11. After the pipeline runs successfully, check your email to find the confirmation email sent from the pipeline.

    ![](./media/image165.png)

    ![](./media/image166.png)

### Task 6: Schedule pipeline execution

In this task, you automate pipeline execution with a schedule. Scheduled runs help ensure data freshness without manual effort

1.  On the **Home** tab of the pipeline editor window, select **Schedule**.

    ![](./media/image167.png)

2.  Select **+ Add schedule** and configure the schedule as required then select **Save** and close the **Schedule** panel.

    **Note:** The example here schedules the pipeline to execute daily at 8:00 PM until the end of the year.

    ![](./media/image168.png)

    ![](./media/image169.png)

    ![](./media/image170.png)

### Task 7: Add a Dataflow activity to the pipeline

In this task, you integrate a Dataflow into your pipeline, chaining activities together. This creates end-to-end workflows where multiple data processing steps are executed in sequence.

1.  Hover over the green line connecting the **Copy activity** and the **Office 365 Outlook** activity on your pipeline canvas and select the **+** button to insert a new activity.

    ![](./media/image171.png)

2.  Choose **Dataflow** from the menu that appears.

    ![](./media/image172.png)

3.  The newly created Dataflow activity is inserted between the Copy activity and the Office 365 Outlook activity, and selected automatically, showing its properties in the area below the canvas. Select the **Settings** tab on the properties area, and then select your dataflow created.

    ![](./media/image173.png)

4.  Select the **Home** tab at the top of the pipeline editor and choose **Run**. Then select **Save and run** again on the confirmation dialog to execute these activities.

    ![](./media/image174.png)

    ![](./media/image175.png)

    ![](./media/image176.png)

5.  Select your workspace, the ***Data Factory-XX*** from the left-hand navigation menu. It opens the workspace item view.

    ![](./media/image177.png)

### Task 8: Monitor data pipeline runs

In this task, you monitor the data pipeline runs.

1.  To monitor your data pipeline runs, hover over your pipeline in your workspace. Doing so brings up three dots to the right of your pipeline name.

2.  Select the three dots to find a list of options. Then select **Recent runs**. This action opens a fly-out on the right side of your screen with all your recent runs and run statuses.

    ![](./media/image178.png)

    ![](./media/image179.png)

3.  Select **Go to monitoring hub** from the prior screenshot to view more details and filter results. Use the filter to find specific data pipeline runs based on several criteria.

    ![](./media/image180.png)

    ![](./media/image181.png)

4.  Select one of your pipelines runs to view detailed information. You're able to view what your pipeline looks like and view more properties like Run ID or errors if your pipeline run failed.

    ![](./media/image182.png)

    ![](./media/image183.png)

5.  If you have greater than 2000 activity runs in your pipelines, select **Load more** to see more results in the same monitoring page.

    ![](./media/image184.png)

    ![](./media/image185.png)

6.  Use the **Filter** to filter by activity status or **Column Options** to edit the columns viewed in the monitoring view.

    ![](./media/image186.png)

    ![](./media/image187.png)

7.  Select the column option and select the columns and click on **Apply** button

    ![](./media/image188.png)

    ![](./media/image189.png)

8.  Select the **sample_pipeline**

    ![](./media/image190.png)

    ![](./media/image191.png)

9.  You can also search for an activity name, activity type, or activity run ID with the **Filter by keyword** box.

    ![](./media/image192.png)

    ![](./media/image193.png)

    ![](./media/image194.png)

10. If you want to export your monitoring data, select **Export to CSV**.

    ![](./media/image195.png)

    ![](./media/image196.png)

11. To find additional information on your pipeline runs **Input** and **Output**, select the input or output links to the right of the relevant row in the Activity Runs.

    ![](./media/image197.png)

12. You can select **Update pipeline** to make changes to your pipeline from this screen. This selection takes you back to the pipeline canvas.

    ![](./media/image198.png)

13. You can also **Rerun** your data pipeline. You can choose to rerun the entire pipeline or only rerun the pipeline from the failed activity.

    ![](./media/image199.png)

14. Select the **Rerun entire data pipeline**

    ![](./media/image200.png)

15. Select **Ok**

    ![](./media/image201.png)

    ![](./media/image202.png)

    ![](./media/image203.png)

## Exercise 3: Implementing CI/CD and ALM Solution Architectures for Dataflow Gen2 in Microsoft Fabric

In this exercise you’ll explore how to leverage variable-references within Dataflow Gen2 to build more flexible and maintainable pipelines in the context of CI/CD and Application Lifecycle Management (ALM). Specifically, you’ll learn how to centralize configuration by using variable libraries, and how dataflows can reference those variables rather than hard-coded values — enabling smoother deployments across environments. For more information, see [Use variable references in Dataflow Gen2](https://learn.microsoft.com/en-us/fabric/data-factory/dataflow-gen2-variable-references).

You’ll work through a scenario where the source, logic (filters), and destination of a dataflow are all driven by variables (such as workspace IDs, lakehouse IDs, filter values) instead of static values.

1.  On the **Data Factory-XX** dev workspace, select the **datafactory_lakehouse** lakehouse.

    ![](./media/image204.png)

2.  Copy the **workspace ID** and the **lakehouse object ID** in the URL and save it in the Notepad.

    ![](./media/image205.png)

3.  On the left navigation, select **Data Factory-XX**, as shown in the image below.

    ![](./media/image206.png)

4.  Repeat the preceding steps for **datafactory_warehouse** to get the warehouse ID.

    ![](./media/image207.png)

    ![](./media/image208.png)

5.  Navigate and click on ***Data Factory-XX*** dev workspace on the left-sided navigation menu.

    ![](./media/image209.png)

6.  In the Fabric Dev workspace, select **+ New item**, search for +++Variable library+++, and select the **Variable Library** tile.

    ![](./media/image210.png)

7.  Name the library +++dataflow-variables+++, and then select **Create**.

    ![](./media/image211.png)

8.  Select **New variable**.

    ![](./media/image212.png)

9.  Create the following variables:

    | **Variable name** | **Type** | **Default value set**                            |
    |-------------------|----------|--------------------------------------------------|
    | WorkspaceId       | Guid     | \<Data Factory-XX (Dev) workspace ID\> |
    | LakehouseId       | Guid     | \<datafactory_lakehouse ID\>    |
    | WarehouseId       | Guid     | \<datafactory_warehouse ID\>   |
    | Region            | String   | +++Sweden+++                                     |

    ![](./media/image213.png)

10. Select **Save** \> **Agree**.

    ![](./media/image214.png)

11. Navigate and click on ***Data Factory-XX*** Workspace on the left navigation

    ![](./media/image215.png)

12. Create a new Dataflow Gen2 by clicking on the **+New item** button in the navigation bar. From the list of available items select the **Dataflow Gen2** item

    ![](./media/image216.png)

13. In the **New Dataflow Gen2** window, click on **Dataflow1** and rename it to +++Variable_Dataflow+++

    ![](./media/image217.png)

14. From the **Home** tab, select **Get data** and then the **More...** option.

    ![](./media/image218.png)

15. Within the **Get data** explorer's search bar, type +++datafactory_lakehouse+++ to locate the lakehouse item. Select the **datafactory_lakehouse** item within the OneLake catalog's returned results.

    ![](./media/image219.png)

    ![](./media/image220.png)

16. From the Get data table navigator, select the table **holidaydatacontainer** and click on **Create**

    ![](./media/image221.png)

    ![](./media/image222.png)

17. From the **Home** tab, select **Get data** and then the **Blank query** option.

    ![](./media/image223.png)

    ![](./media/image224.png)

18. Once your query is created and visible in the dataflow, rename it to **WorkspaceId** and update the formula in the **Source** step with the following expression and click on **Next** button

    ```
    let
        Source = Variable.ValueOrDefault("$(/**/dataflow-Variables/WorkspaceId)", "Your Workspace ID")
    in
        Source
    ```

    ![](./media/image225.png)

    ![](./media/image226.png)

19. In the **Power Query** editor, right-click the existing query (for example, **Query**) from the **Queries** pane, select **Rename**, and change its name to **WorkspaceId**

    ![](./media/image227.png)

    ![](./media/image228.png)

20. From the **Home** tab, select **Get data** and then the **Blank query** option.

    ![](./media/image229.png)

21. Once your query is created and visible in the dataflow, rename it to **LakehouseId** and update the formula in the **Source** step with the following expression and click on **Next** button

    ```
    let
        Source = Variable.ValueOrDefault("$(/**/dataflow-Variables/LakehouseId)", "Your Lakehouse ID")
    in
        Source
    ```

    ![](./media/image230.png)

    ![](./media/image231.png)

22. In the **Power Query** editor, right-click the existing query (for example, **Query**) from the **Queries** pane, select **Rename**, and change its name to **LakehouseId**

    ![](./media/image232.png)

    ![](./media/image233.png)

23. Select the **holidaydatacontainer** table. Once your query is created and visible in the Dataflow, navigate to the 'Applied Steps' pane on the right side and select the 'Step' option to open the Query script and modify the formula as required.

    ![](./media/image234.png)

    ![](./media/image235.png)

    ![](./media/image236.png)

24. Once both queries are created, update the query script to reference them instead of using hardcoded values by manually replacing the original values in the formula bar with the **WorkspaceId** and **LakehouseId** query references.

    ![](./media/image237.png)

    The data preview still evaluates correctly, and the diagram view shows the references between the queries.

25. From the **Home** tab, select **Get data** and then the **Blank query** option.

    ![](./media/image238.png)

26. Once your query is created and visible in the dataflow, rename it to **Region** and update the formula in the **Source** step with the following expression and click on **Next** button

    ```
    let
        Source = Variable.ValueOrDefault("$(/**/dataflow-Variables/Region)", "Sweden")
    in
        Source
    ```

    ![](./media/image239.png)

    ![](./media/image240.png)

27. In the **Power Query** editor, right-click the existing query (for example, **Query**) from the **Queries** pane, select **Rename**, and change its name to **Region**

    ![](./media/image241.png)

    ![](./media/image242.png)

28. On the Home tab, go to the **holidaydatacontainer** table, and select **CountryOrRegion** column

    ![](./media/image243.png)

29. Select the **CountryOrRegion** column sort and filter dropdown menu, then select **Text filters**, and choose **Equals...**.

    ![](./media/image244.png)

30. In the **Filter rows** dialog, click the pencil icon next to the value field, then select **Select a query** to dynamically filter rows based on the **Region** query.

    ![](./media/image245.png)

31. Select the **Region** and click on **OK** button

    ![](./media/image246.png)

    ![](./media/image247.png)

32. In the Home window, select **Save** button

    ![](./media/image248.png)

33. In the Home window, select **Check validation Dataflow**

    ![](./media/image249.png)

    ![](./media/image250.png)

34. Once the query script has been updated, navigate to the **Data destination** pane on the right side, click the **+** icon, and select **Lakehouse** as the destination to load the transformed data

    ![](./media/image251.png)

35. On the **Connect to data destination** dialog, your connection should already be selected. Select **Next** to continue.

    ![](./media/image252.png)

36. On the **Choose destination target** dialog, browse to the Lakehouse where you wish to load the data and name the new table, then select **Next** again.

    ![](./media/image253.png)

37. Click on **Save settings**

    ![](./media/image254.png)

    ![](./media/image255.png)

38. In the Home window, select **Save & run** and click on **Save & run** button

    ![](./media/image256.png)

39. In the left-sided navigation menu, navigate and click on ***Data Factory-XX***, as shown in the below image

    ![](./media/image257.png)

    ![](./media/image258.png)

    ![](./media/image259.png)

40. Select the Warehouse

    ![](./media/image260.png)

    ![](./media/image261.png)

## Exercise 4: Enable GitHub Integration for Source Control and CI/CD in Microsoft Fabric

In this exercise, you integrate Microsoft Fabric with GitHub to enable source control and CI/CD workflows. You create repositories, generate secure tokens, and sync workspace items with Git. This ensures version control, collaborative development, and traceable changes across your data engineering lifecycle.

### Task 1: Create a GitHub account

In this task, you create a new **Github account** with the same tenant credentials that you have used in this lab.

1.  Navigate to the GitHub with this link +++https://github.com/+++ and click on **Sign up** to proceed further.

    ![](./media/image262.png)

2.  Now, to create a new GitHub account, enter the **email**, **password** and a unique **username** and click on **Continue** button.

    ![](./media/image263.png)

3.  Start the **verification** **puzzle** by following the instruction on the screen. Click **Submit**.

4.  Enter the **verification** **code** you’ve received on your mail.

    ![](./media/image264.png)

5.  Now, with your credentials sign-in to GitHub and click **Sign in**.

    ![](./media/image265.png)

6.  You have successfully created a new account on GitHub.

    ![](./media/image266.png)

### Task 2: Create a Repository in GitHub

1.  After successfully setting up GitHub account login to your account, click on **Create Repository** under create your first project.

    ![](./media/image267.png)

2.  After clicking **new repository** option, fill up the required fields like **Repository name**, **Choose the visibility, Add a README file** etc. After performing these steps click **Create Repository** button.

    ![](./media/image268.png)

    ![](./media/image269.png)

3.  After selecting **Create repository**, you will be directed to **CopyJobDev** page. Right now, the only file we have is a **readme** file.

    ![](./media/image270.png)

4.  To create a **new folder** in GitHub, you must first create a new file and add that file to your new folder at the same time. Do this by navigating to your **repository page**.

    Next, click the **Add file** drop-down menu on the right, and then select **Create new file**.

    ![](./media/image271.png)

5.  To create a folder, provide a name to this **folder** **(Copyjob-Test)** as follows. The folder name will automatically generate a new folder. Now you can give your **file a name** **(CJ-Contents).**
    Type the **folder name followed by /** and hit Enter.

    ![](./media/image272.png)

6.  You'll need to **commit** your changes. Give a commit message and **select commit directly to the main branch** option. Click on **Commit changes**.

    ![](./media/image273.png)

### Task 3: Generate a fine-grained token with *read* and *write* permissions for Contents, under repository permissions

1.  Go to your **GitHub profile settings** and select **settings** option.

    ![](./media/image274.png)

2.  Navigate to **Developer settings**.

    ![](./media/image275.png)

3.  Select **Personal access tokens** and Choose **Fine-grained tokens**. Click on **Generate new token.**

    ![](./media/image276.png)

4.  Provide a descriptive **name** and optional **description** for the token. Set an **expiration** **date** for the token. Choose the **resource owner** as your GitHub username.

    ![](./media/image277.png)

5.  Choose the **specific repository** that you have created as **Copyjob-Dev** you want the token to access.

    ![](./media/image278.png)

6.  **Specify Access Permissions:**

    - **Contents:** Select "Read and write" access for the "Contents" permission.

    - **Other Permissions:** You can also configure other permissions as needed, such as "Issues" or "Pull Requests".

    ![](./media/image279.png)

    ![](./media/image280.png)

7.  Click **Generate token**.

    ![](./media/image281.png)

    ![](./media/image282.png)

    **Important:** Copy and securely store the generated token, as it will not be displayed again.

    ![](./media/image283.png)

### Task 4: Connect to a Git repository

To use Git integration with Copy job in Fabric, you first need to connect to a Git repository, as described here.

1.  Sign-in into the Fabric portal and navigate to dev workspace as **Data Factory-XX** to connect to Git. Select **Workspace settings**.

    ![](./media/image284.png)

2.  Select **Git integration**. Select your Git provider. Currently, Fabric only supports *Azure DevOps* or *GitHub*. If you use GitHub, you need to select **Add account** to connect your GitHub account.

    ![](./media/image285.png)

3.  In Add **GitHub account page**, provide a **display name** for your account. In **personal access token**, paste the copied **fine-grained token** that we have generated from the GitHub and at last, in **repository URL**, copy the URL from the GitHub. Click on **Add**.

    ![](./media/image286.png)

4.  After you sign in, select **Connect** to allow Fabric to access your GitHub account.

    ![](./media/image287.png)

### Task 5: Connect to a workspace

Once you connect to a Git repository, you need to connect to a workspace, as described here.

1.  From the dropdown menu, specify the following details about the branch you want to connect to:

    - **Branch**: Specify the branch as ***main***.

    - **Folder**: the GitHub folder that you created earlier, +++Copyjob-Test+++

    Select **Connect and sync**.

    ![](./media/image288.png)

    ![](./media/image289.png)

    ![](./media/image290.png)

2.  After you connect, the Workspace displays information about **source control** that allows users to view the connected branch, the status of each item in the branch, and the time of the last sync

3.  You can also verify the committed changes in your GitHub repository to ensure all items have been pushed successfully

    ![](./media/image291.png)

## Exercise 5: Setting Up Azure DevOps and Git Repository (Optional)

In this exercise, you configure Azure DevOps as an alternative Git provider for enterprise-grade CI/CD. You create DevOps organizations, projects, and repositories, connect Fabric workspaces, and manage branching strategies and pull requests to streamline development and deployment workflows.

### Task 1: Creating the Repository

1.  Open your browser, navigate to the address bar, and type or paste the following URL: +++https://dev.azure.com/+++ then press the **Enter** button.

2.  Select **Sign in**

    ![](./media/image292.png)

3.  On the **Microsoft Azure** window, enter your credentials.

    ![](./media/image293.png)

4.  In the **Azure portal** search bar, type +++Azure DevOps+++ and click on **Azure DevOps organizations**.

    ![](./media/image294.png)

5.  On the **Azure DevOps Home** page, select **View my Organization**

    ![](./media/image295.png)

    ![](./media/image296.png)

6.  Select **Create new organization**

    ![](./media/image297.png)

7.  On the **Azure DevOps** tab, click on the **Continue** button

    ![](./media/image298.png)

8.  Enter the Name your Azure DevOps organization and click on **Continue** button

    ![](./media/image299.png)

9.  On the **Create a project to get started** tab, enter the Project name as +++DataFactory+++ and click on **+ Create project** button

    ![](./media/image300.png)

10. To create a new repository in Azure DevOps, navigate to **Repos \> Files \> New repository dropdown \> New repository**

    ![](./media/image301.png)

11. On the **Create a repository** tab, enter the Repository name as +++DevOps-git+++ and select **Create**.

    ![](./media/image302.png)

    ![](./media/image303.png)

12. On the left navigation, navigate to **Repos** section, then click on **Branches**.

    ![](./media/image304.png)

13. Click on **New branch** from the top-right corner.

    ![](./media/image305.png)

14. On the **Create a branch** window, set the name to +++Datafactory-devbranch+++ and then select **Create**.

    ![](./media/image306.png)

    ![](./media/image307.png)

### Task 2: Integrating the Fabric feature workspace with the Azure Devops Repo

1.  Sign in to Microsoft Fabric and navigate to the development workspace (*Data Factory-XX*) to connect to Git. Then, select *Workspace settings*.

    ![](./media/image308.png)

2.  Select **Git integration**. Select your **Git provider**. Currently, Fabric only supports *Azure DevOps* or *GitHub*. Select **Azure DevOps** and select **Connect** to connect your Azure DevOps account.

    ![](./media/image309.png)

3.  Select your **Organization, Project, Git repository, Branch** and **Git folder**. Click on **Connect and Sync**

    ![](./media/image310.png)

    ![](./media/image311.png)

    ![](./media/image312.png)

    ![](./media/image313.png)

4.  For us to submit a pull request, we need to navigate to the **Source control** pane and then click on **View repository**.

    ![](./media/image314.png)

### Task 3: Merge Fabric Data Factory Changes to Main Branch

1.  Once your changes are pushed to the feature branch (e.g., Datafactory-devbranch), click on **'Create a pull request'** to begin merging your Fabric Data Factory changes into the main branch.

    ![](./media/image315.png)

2.  Provide **a Title and Description** for the pull request summarizing your changes (e.g., 'Committing 1 item from workspace'), then click on 'Create' to initiate the merge into the main branch

    ![](./media/image316.png)

    ![](./media/image317.png)

3.  Select **Complete**

    ![](./media/image318.png)

4.  In the **Complete pull request** page, ensure that 'Complete associated work items after merging' is selected, then click on 'Complete merge' to finalize the integration into the main branch.

    ![](./media/image319.png)

    ![](./media/image320.png)

5.  Select **Branches** to see the merged branch.

    ![](./media/image321.png)

    ![](./media/image322.png)

## Exercise 6: Get Started with deployment pipelines for Git

In this exercise, you configure Git-synced deployment pipelines to manage content promotion across Development, Test, and Production stages. This ensures consistent, version-controlled deployments aligned with Git workflows, enabling traceable and auditable release cycles across your data platform.

Before you get started, be sure to set up the following prerequisites:

- An active Microsoft Fabric subscription.

- Admin access to a Fabric workspace.

### Task 1: Deploy content from one workspace to another

1.  From the **Data Factory-XX** Workspaces page, select **View** **deployment pipeline**.

    ![](./media/image323.png)

2.  Name the pipeline +++Data Factory-Pipeline+++, and then select **Next**.

    ![](./media/image324.png)

3.  On the deployment pipeline, select **Create and continue**.

    ![](./media/image325.png)

4.  For the **Development** stage, in the drop-down list, select **Data Factory-XX** for the workspace. Then select the **Assign** check mark.

    ![](./media/image326.png)

    ![](./media/image327.png)

5.  Select the **Test** stage, choose all the items, and click **Deploy**.

    ![](./media/image328.png)

6.  Click on **Deploy** button

    ![](./media/image329.png)

    ![](./media/image330.png)

7.  The **Test workspace** has been successfully created, and the data has been deployed

    ![](./media/image331.png)

8.  Select the **Production** stage, choose **all the items**, and click the **Deploy** button

    ![](./media/image332.png)

9.  Click on **Deploy** button

    ![](./media/image333.png)

    ![](./media/image334.png)

10. The **Production workspace** has been successfully created, and the data has been deployed

    ![](./media/image335.png)

11. To review the Test and Production workspaces, click on the workspace in the left navigation pane.

    ![](./media/image336.png)

### Task 2: Create the data_movement_pipeline pipeline

1.  Navigate and click on ***Data Factory-XX*** dev workspace on the left-sided navigation menu.

    ![](./media/image337.png)

2.  On the **Fabric** page, navigate to +**New item** section and then filter by, and select, **Pipeline** to create pipeline.

    ![](./media/image338.png)

3.  Enter the name +++data_movement_pipeline+++, and then select **Create**.

    ![](./media/image339.png)

4.  Select **Copy data** \> **Add to canvas**.

    ![](./media/image340.png)

    ![](./media/image341.png)

### Task 3: Get the workspace IDs and object IDs for lakehouses

In this task, you get the unique identifiers to use in your variable library.

1.  On the **Data Factory-XX** dev workspace, select the **datafactory_lakehouse** lakehouse.

    ![](./media/image342.png)

2.  Copy the workspace ID and the lakehouse object ID in the URL and save it in the Notepad.

    ![](./media/image343.png)

3.  Repeat the preceding steps for the **Data Factory-XX \[Test\]** and **Data Factory-XX \[Production\]** workspace IDs, including their lakehouse IDs.

    ![](./media/image344.png)

    ![](./media/image345.png)

    ![](./media/image346.png)

    ![](./media/image347.png)

    ![](./media/image348.png)

    ![](./media/image349.png)

### Task 4: Create a variable library with variables

Now, create the variable library

1.  Navigate and click on ***Data Factory-XX*** dev workspace on the left-sided navigation menu.

    ![](./media/image350.png)

2.  In the Fabric Dev workspace, select **+ New item**, search for +++Variable library+++, and select the **Variable Library** tile.

    ![](./media/image351.png)

3.  Name the library +++WS variables+++, and then select **Create**.

    ![](./media/image352.png)

4.  Select **New variable**.

    ![](./media/image353.png)

5.  Create the following variables:

    | **Name**                 | **Type** | **Default value set**                                  |
    |--------------------------|----------|--------------------------------------------------------|
    | Source_Lakehouse_ID      | String   | \<datafactory_lakehouse ID (Dev)\>  |
    | Source_Workspace_ID      | String   | \<Data Factory-XX (Dev) workspace ID\>       |
    | Destination_Lakehouse_ID | String   | \<datafactory_lakehouse ID (Test)\> |
    | Destination_Workspace_ID | String   | \<Data Factory-XX (Test) workspace ID\>      |
    | SourceTable_Name         | String   | green_tripdata_2017                                    |
    | DestinationTable_Name    | String   | TestCopiedData                                         |

    ![](./media/image354.png)

6.  On the **WS Variables** variable library page, select **Add value set**.

    ![](./media/image355.png)

7.  Enter +++Prod VS+++ for the name, and then select **Create**.

    ![](./media/image356.png)

8.  Create the following variables:

    | **Name**                 | **Type** | **Default value set**                                        |
    |--------------------------|----------|--------------------------------------------------------------|
    | Source_Lakehouse_ID      | String   | \<datafactory_lakehouse ID (Test)\>       |
    | Source_Workspace_ID      | String   | \<Data Factory-XX (Test) workspace ID\>            |
    | Destination_Lakehouse_ID | String   | \<datafactory_lakehouse ID (Production)\> |
    | Destination_Workspace_ID | String   | \<Data Factory-XX (Production) workspace ID\>      |
    | SourceTable_Name         | String   | TestCopiedData                                               |
    | DestinationTable_Name    | String   | ProdCopiedData                                               |

    ![](./media/image357.png)

9.  Select **Save** \> **Agree**.

    ![](./media/image358.png)

10. Select **data_movement_pipeline**.

    ![](./media/image359.png)

### Task 5: Declare library variables in the data_movement_pipeline pipeline

1.  Navigate and click on ***Data Factory-XX*** dev workspace on the left-sided navigation menu.

    ![](./media/image360.png)

2.  Select **data_movement_pipeline**

    ![](./media/image361.png)

3.  Select the canvas so that the focus is off **Copy data**.

4.  Select **Library variables (preview)**.

    ![](./media/image362.png)

5.  Select **Source_Lakehouse_ID** variable and click **Select variable**.

    ![](./media/image363.png)

6.  Click on **+ New** and repeat the above step to select all the variables.

    ![](./media/image364.png)

    ![](./media/image365.png)

    ![](./media/image366.png)

### Task 6: Configure the source connection for the data_movement_pipeline pipeline

1.  On the canvas, select **Copy data** so that the focus is on **Copy data**.

2.  Select **Source** and open the **Connection** drop-down menu. Select **Browse all**.

    ![](./media/image367.png)

3.  Configure **Source_Lakehouse_ID**:

    1. Under **Source** \> **Lakehouse**, select **Add dynamic content**.

    2. Select the ellipsis (**...**), and then select **Library variables**.

    3. Select **Source_Lakehouse_ID**. It populates the box with `@pipeline().libraryVariables.WSvariables_Source_Lakehouse_ID`. Select **OK**.

    ![](./media/image368.png)

    ![](./media/image369.png)

    ![](./media/image370.png)

4.  Configure **Source_Workspace_ID**:

    1. Under **Source** \> **Workspace ID**, select **Add dynamic content**.

    2. Select the ellipsis (**...**), and then select **Library variables**.

    3. Select **Source_Workspace_ID**. It populates the box with `@pipeline().libraryVariables.WSvariables_Source_Workspace_ID`. Select **OK**.

    ![](./media/image371.png)

    ![](./media/image372.png)

    ![](./media/image373.png)

5.  Configure **SourceTable_Name**:

    1. Under **Source** \> **Table**, select **Enter manually**, select **Table name**, and then select **Add dynamic content**.

    2. Select the ellipsis (**...**), and then select **Library variables (preview)**.

    3. Select **SourceTable_Name**. It populates the box with `@pipeline().libraryVariables.WSvariables_SourceTable_Name`. Select **OK**.

    ![](./media/image374.png)

    ![](./media/image375.png)

6.  Now that the source connection is set up, you can test it. Select **Preview data**, and then select **OK** on the flyout. After the data is populated, you can close the data preview.

    ![](./media/image376.png)

    ![](./media/image377.png)

### Task 7: Configure the destination connection for the data_movement_pipeline pipeline

In this task, you configure the destination connection for your pipeline.

1.  On the canvas, select **Copy data** so that the focus is on **Copy data**.

2.  Select **Destination**.

    ![](./media/image378.png)

3.  Configure **Destination_Lakehouse_ID**:

    1.  Under **Destination** \> **Connection**, select **Add dynamic content**.

    2.  Select the ellipsis (**...**), and then select **Library variables (preview)**.

    3.  Select **Destination_Lakehouse_ID**. It populates the box with `@pipeline().libraryVariables.WSvariables_Destination_Lakehouse_ID`. Select **OK**.

    ![](./media/image379.png)

    ![](./media/image380.png)

    ![](./media/image381.png)

    ![](./media/image382.png)

4.  Configure **Destination_Workspace_ID**:

    1. Under **Destination** \> **Workspace ID**, select **Add dynamic content**.

    2. Select the ellipsis (**...**), and then select **Library variables (preview)**.

    3. Select **Destination_Workspace_ID**. It populates the box with `@pipeline().libraryVariables.WSvariables_Destination_Workspace_ID`. Select **OK**.

    ![](./media/image383.png)

    ![](./media/image384.png)

5.  Configure **DestinationTable_Name**:

    1. Under **Destination** \> **Table**, select **Enter manually**, select **Table name**, and then select **Add dynamic content**.

    2. Select the ellipsis (**...**), and then select **Library variables (preview)**.

    3. Select **DestinationTable_Name**. It populates the box with `@pipeline().libraryVariables.WSvariables_DestinationTable_Name`. Select **OK**.

    ![](./media/image385.png)

    ![](./media/image386.png)

6.  Select **Validate**.

    ![](./media/image387.png)

    ![](./media/image388.png)

7.  Now that the destination connection is set up, save the pipeline and select **Run**. Confirm that it successfully runs.

    ![](./media/image389.png)

    ![](./media/image390.png)

    ![](./media/image391.png)

8.  In the left navigation pane, select *Workspaces*, and then choose ***Data Factory-XX \[Test\]***.

    ![](./media/image392.png)

9.  Switch to the **datafactory_lakehouse** lakehouse.

    ![](./media/image393.png)

10. To validate the created tables, click and select refresh on the **Tables** in the **Explorer** panel until all the tables appear in the list.

    ![](./media/image394.png)

11. Confirm that the **TestCopiedData** table appears under **test lakehouse**.

    ![](./media/image395.png)

### Task 8: Set the variable library's active set for each stage

1.  On the top navigation menu, navigate and click on ***WS variables***, as shown in the below image.

    ![](./media/image396.png)

2.  Select the Prod-VS ellipsis (**...**), and then select **Set as active**. Select the **Set as Active** button.

    ![](./media/image397.png)

    ![](./media/image398.png)

3.  Select **Save** \> **Agree**.

    ![](./media/image399.png)

4.  On the top navigation menu, navigate and click on ***data_movement_pipeline***, as shown in the below image

    ![](./media/image400.png)

5.  On the Copy Activity page, review the variables for both the Source and Destination

    ![](./media/image401.png)

6.  Then, click the **Run** button to execute the activity.

    ![](./media/image402.png)

    ![](./media/image403.png)

7.  Pipeline successfully executed

    ![](./media/image404.png)

8.  To verify the data in the production lakehouse, select the ***Data Factory-XX \[Production\]*** workspace on the left navigation menu.

    ![](./media/image405.png)

9.  Select the production lakehouse

    ![](./media/image406.png)

10. To validate the created tables, click and select refresh on the **Tables** in the **Explorer** panel until all the tables appear in the list.

    ![](./media/image407.png)

11. Confirm that the **ProdCopiedData** table appears under **datafactory_lakehouse**

    ![](./media/image408.png)

12. Navigate and click on the ***Data Factory-XX*** dev workspace on the left navigation menu.

    ![](./media/image409.png)

13. In **Source control**, select the required items (such as the pipeline and workspace variables), enter an optional commit message, and click **Commit** to save the changes.

    ![](./media/image410.png)

    ![](./media/image411.png)

### Task 9: Deploy content from one stage to another

1.  From the **Data Factory-XX** Workspaces page, select **View** **deployment pipeline**.

    ![](./media/image412.png)

    ![](./media/image413.png)

2.  You can review the **deployment history** to see the last time content was deployed to each stage. To examine the differences between the two pipelines before you deploy, see and compare the content in different deployment stages.

    ![](./media/image414.png)

    ![](./media/image415.png)

### Task 10: Clean up resources

1.  Open the **Data Factory-Pipeline** deployment pipeline, select the **Settings** icon, and then select **Delete pipeline**.

    ![](./media/image416.png)

2.  In the left navigation pane, select **Workspaces**, and then select ***Data Factory-XX \[Test\]***.

    ![](./media/image417.png)

3.  Select **Workspace settings**.

    ![](./media/image418.png)

4.  Select **General**, and then select **Remove this workspace**.

    ![](./media/image419.png)

5.  Select **Delete** to confirm.

    ![](./media/image420.png)

6.  Repeat steps 2 to 5 for the ***Data Factory-XX \[Production\]*** workspace.

    ![](./media/image421.png)

    ![](./media/image422.png)

    ![](./media/image423.png)

7.  Repeat steps 2 to 5 for the ***Data Factory-XX*** workspace.

    ![](./media/image424.png)

    ![](./media/image425.png)

    ![](./media/image426.png)

    ![](./media/image427.png)

**Summary**

In this lab, you explored the full lifecycle of building, automating, and deploying data solutions in Microsoft Fabric using Dataflow Gen2, pipelines, deployment pipelines, and Git-based CI/CD integration. You began by creating a Fabric workspace, ingesting sample NYC Taxi data into a lakehouse, and building a Dataflow Gen2 with transformations, merges, parameters, and automated refreshes. Next, you advanced to pipelines, configuring copy jobs, building parameterized templates, scheduling runs, and monitoring execution results. Variable Libraries were then introduced to dynamically manage environment-specific values for Dev, Test, and Prod, enhancing pipeline flexibility and deployment readiness. The lab continued with connecting Fabric to GitHub, creating repositories and tokens, and synchronizing workspaces for version control, laying the foundation for CI/CD workflows. You further extended Git integration by setting up Azure DevOps projects, linking them to Fabric workspaces, and practicing pull request–based branching strategies. Finally, you implemented deployment pipelines to move content across Development, Test, and Production stages in a controlled, versioned, and auditable process. Through these exercises, you gained hands-on experience in data ingestion, transformation, and preparation with Dataflow Gen2, orchestration and automation with pipelines, flexible deployments using variable libraries, enterprise-grade CI/CD with GitHub and Azure DevOps, and controlled release management via deployment pipelines. Collectively, the lab provided a comprehensive understanding of how Microsoft Fabric supports modern data engineering, automation, and DevOps practices—from raw ingestion to production deployment.
