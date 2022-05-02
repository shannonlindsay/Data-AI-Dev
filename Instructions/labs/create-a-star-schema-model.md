---
lab:
    title: 'Create a star schema model'
    module: 'Create a star schema model'
    labprofile: 'https://labondemand.com/AuthenticatedLaunch/111889?providerId=1'
---

# Create a star schema model

## Overview

**The estimated time to complete the lab is 30 minutes**

In this lab, you will use Power BI Desktop to develop a data model over the Azure Synapse Adventure Works data warehouse. The data model will allow you to publish a semantic layer over the data warehouse.

In this lab, you learn how to:

- Create a Power BI connection to an Azure Synapse Analytics SQL pool.

- Develop model queries.

- Organize the model diagram.

## Get started

In this exercise, prepare your environment.

### Load data into Azure Synapse Analytics

   > **Note**: If you have already loaded data into Azure Synapse Analytics using a git clone, you can skip this task and proceed to **Set up Power BI.**

1. Sign into the [Azure portal](https://portal.azure.com) using the login information located on the Resources tab on the right side of the VM.
2. Use the **[\>_]** button to the right of the search bar at the top of the page to create a new Cloud Shell in the Azure portal, selecting a ***PowerShell*** environment and creating storage if prompted. The cloud shell provides a command line interface in a pane at the bottom of the Azure portal, as shown here:

    ![Azure portal with a cloud shell pane](../images/cloud-shell.png)

    > **Note**: If you have previously created a cloud shell that uses a *Bash* environment, use the the drop-down menu at the top left of the cloud shell pane to change it to ***PowerShell***.

3. Note that you can resize the cloud shell by dragging the separator bar at the top of the pane, or by using the **&#8212;**, **&#9723;**, and **X** icons at the top right of the pane to minimize, maximize, and close the pane. For more information about using the Azure Cloud Shell, see the [Azure Cloud Shell documentation](https://docs.microsoft.com/azure/cloud-shell/overview).

4. In the PowerShell pane, enter the following command to clone this repo:

    ```
    git clone https://github.com/GraemeMalcolm/synapsestuff dp-500
    ```

5. After the repo has been cloned, enter the following commands to change to the **setup** folder and run the **setup.ps1** script it contains:

    ```
    cd dp-500/setup
    ./setup.ps1
    ```

6. When prompted, enter a suitable password to be set for your Azure Synapse SQL pool.

    > **Note**: Be sure to remember this password!

7. Wait for the script to complete - this typically takes around 20 minutes; but in some cases may take longer.
8. After creating the Synapse workspace and SQL Pool and loading the data, the script pauses the pool to prevent unnecessary Azure charges. When you're ready to work with your data in Azure Synapse Analytics, you'll need to resume the SQL Pool.


### Set up Power BI

In this task, you will set up Power BI.

1. To open Power BI Desktop, on the taskbar, select the **Power BI Desktop** shortcut.

	![](../images/dp500-create-a-star-schema-model-image1.png)

2. Select **X** located at the top-right of the getting started window.

	![](../images/dp500-create-a-star-schema-model-image2.png)

3. At the top-right corner of Power BI Desktop, if you're not already signed in, select **Sign In**. Use the lab credentials to complete the sign in process.

	![](../images/dp500-create-a-star-schema-model-image3.png)
4. You will be redirected to the Power BI sign-up page in Microsoft Edge. Select **Continue** to complete the sign up.

	![](../images/dp500-create-a-star-schema-model-image3b.png)

5. Enter a 10 digit phone number and select **Get started**. Select **Get started** once more. You will be redirected to Power BI.

1. At the top-right, select the profile icon, and then select **Start trial**.

	![](../images/dp500-create-a-dataflow-image3.png)

1. When prompted, select **Start trial**.

	![](../images/dp500-create-a-dataflow-image4.png)

1. Do any remaining tasks to complete the trial setup.

	*Tip: The Power BI web browser experience is known as the **Power BI service**.*
1. Select Workspaces and **Create a Workspace**.
    
    ![](../images/dp500-create-a-star-schema-model-image2a.png)

1. Create a workspace named DP500 labs and select **Save**.

    ![](../images/dp500-create-a-star-schema-model-image2b.png)

1. Navigate back to Power BI Desktop. If you see **Sign in** in the top right corner of the screen, sign-in again using the credentials provided on the Resources tab of the lab environment. If you are already signed in, proceed to the next step.

    <img width="80" alt="image" src="https://user-images.githubusercontent.com/77289548/166337862-538a1900-ec67-44d1-905f-d404c5b0a58a.png">

1. Close Power BI Desktop. Do not save your file.

	*You will open Power BI Desktop again in the next exercise.*

### Start the SQL pool

In this task, you will start the SQL pool.

1. In a Microsoft Edge, go to [https://portal.azure.com](https://portal.azure.com/).

1. Use the lab credentials to complete the sign in process.

1. Select **Azure Synapse Analytics** from Azure services. Select your Synapse workspace.

   ![](../images/dp500-create-a-star-schema-model-image3c.png)

1. Locate and select the dedicated SQL pool.

   ![](../images/dp500-create-a-star-schema-model-image3d.png)

1. Resume the SQL pool.

	![](../images/dp500-create-a-star-schema-model-image3e.png)

	*Important: The SQL pool is a costly resource. Please limit the use of this resource when working on this lab. The final task in this lab will instruct you to pause the resource.*

### Link your Power BI workspace to Azure Synapse Analytics

In this task you will link your existing Power BI workspace to your Azure Synapse Analytics workspace.
1. From the dedicated SQL pool in the Azure Portal, select **Open in Synapse Studio** from the ribbon.

1. On the home page of Azure Synapse Studio, select **Visualize** to link your Power BI workspace.

	![](../images/dp500-create-a-star-schema-model-image3f.png)

1. From the **Workspace name** dropdown, select the workspace you created in the previous task and select **Create**.

	![](../images/dp500-create-a-star-schema-model-image3g.png)

    ![](../images/dp500-create-a-star-schema-model-image3h.png)

## Develop a data model

In this exercise, you will develop a DirectQuery model to support Power BI analysis and reporting of the data warehouse reseller sales subject.

### Download a dataset file

In this task, you will download a Power BI data source file from Synapse Studio.

1. In Microsoft Edge, navigate to **Synapse Studio**.
    ![](../images/dp500-create-a-star-schema-model-image4a.png)

2. At the left, select the **Develop** hub.

	![](../images/dp500-create-a-star-schema-model-image4.png)

3. In the **Develop** pane, expand **Power BI**, then expand the workspace, and then select **Power BI datasets**.

	![](../images/dp500-create-a-star-schema-model-image5.png)

4. In the **Power BI Datasets** pane, select **New Power BI Dataset**.

	![](../images/dp500-create-a-star-schema-model-image6.png)

5. In the left pane, at the bottom, select **Start**.

	![](../images/dp500-create-a-star-schema-model-image7.png)

6. Select your SQL pool, **sqldw**, and then select **Continue**.

	![](../images/dp500-create-a-star-schema-model-image8.png)

7. To download the .pbids file, select **Download**.

	![](../images/dp500-create-a-star-schema-model-image9.png)

	*A .pbids file contains a connection to your SQL pool. It's a convenient way to start your project. When opened, it will create a new Power BI Desktop solution that already stores the connection details to your SQL pool.*

8. When the .pbids file has downloaded, open it.

	*When the file opens, it will prompt you to create queries using the connection. You will define those queries in the next task.*

### Create model queries

In this task, you will create five Power Query queries that will each load as a table to your model.

1. In Power BI Desktop, in the **SQL Server Database** window, at the left, select **Microsoft Account**.

	![](../images/dp500-create-a-star-schema-model-image10.png)

2. Select **Sign In**.

3. Sign in using the lab Azure credentials located on the Resources tab.

4. Select **Connect**.

	![](../images/dp500-create-a-star-schema-model-image11.png)

5. In the **Navigator** window, select (don't check) the **DimDate** table.

6. In the right pane, notice the preview result, which shows a subset of the table rows.

	![](../images/dp500-create-a-star-schema-model-image12.png)

7. To create queries (which will become model tables), check the following five tables:

	- DimDate

	- DimProduct

	- DimReseller

	- DimSalesTerritory

	- FactResellerSales

8. To apply transformations to the queries, at the bottom-right, select **Transform Data**.

	![](../images/dp500-create-a-star-schema-model-image13.png)

	*Transforming the data allows you to define what data will be available in your model.*


9. In the **Connection Settings** window, select the **DirectQuery** option.

	![](../images/dp500-create-a-star-schema-model-image14.png)

	*This decision is important. DirectQuery is a storage mode. A model table that uses DirectQuery storage mode doesn't store data. So, when a Power BI report visual queries a DirectQuery table, Power BI sends a native query to the data source. This storage mode can be used for large data stores like Azure Synapse Analytics (because it could be impractical or uneconomic to import large data volumes) or when near real-time results are required.*

10. Select **OK**.

	![](../images/dp500-create-a-star-schema-model-image15.png)


11. In the **Power Query Editor** window, in the **Queries** pane (located at the left), notice there is one query for each table you checked.

	![](../images/dp500-create-a-star-schema-model-image16.png)

	*You will now revise the definition of each query. Each query will become a model table when it's applied to the model. You will now rename the queries, so they're described in more friendly and concise ways, and apply transformations to deliver the columns required by the known reporting requirements.*

12. Select the **DimDate** query.

	![](../images/dp500-create-a-star-schema-model-image17.png)

13. In the **Query Settings** pane (located at the right), to rename the query, in the **Name** box, replace the text with **Date**, and then press **Enter**.

	![](../images/dp500-create-a-star-schema-model-image18.png)


14. To remove unnecessary columns, on the **Home** ribbon tab, from inside the **Manage Columns** group, select the **Choose Columns** icon.

	![](../images/dp500-create-a-star-schema-model-image19.png)

15. In the **Choose Columns** window, to uncheck all checkboxes, uncheck the first checkbox.

	![](../images/dp500-create-a-star-schema-model-image20.png)


16. Check the following five columns.

	- DateKey

	- FullDateAlternateKey

	- EnglishMonthName

	- FiscalQuarter

	- FiscalYear

	![](../images/dp500-create-a-star-schema-model-image21.png)

	*This selection of columns determine what will be available in your model.*

17. Select **OK**.

	![](../images/dp500-create-a-star-schema-model-image22.png)

18. In the **Query Settings** pane, in the **Applied Steps** list, notice that a step was added to remove other columns.

	![](../images/dp500-create-a-star-schema-model-image23.png)

	*Power Query defines steps to achieve the desired structure and data. Each transformation is a step in the query logic.*

19. To rename the **FullDateAlternateKey** column, double-click the **FullDateAlternateKey** column header.

20. Replace the text with **Date**, and then press **Enter**.

	![](../images/dp500-create-a-star-schema-model-image24.png)

21. Notice that a new applied step is added to the query.

	![](../images/dp500-create-a-star-schema-model-image25.png)

22. Rename the following columns:

	- **EnglishMonthName** as **Month**

	- **FiscalQuarter** as **Quarter**

	- **FiscalYear** as **Year**


23. To validate the query design, in the status bar (located along the bottom of the window), verify that the query has five columns.

	![](../images/dp500-create-a-star-schema-model-image26.png)

	*Important: If the query design does not match, review the exercise steps to make any corrections.*

	*The design of the **Date** query is now complete.*

24. In the **Applied Steps** pane, right-click the last step, and then select **View Native Query**.

	![](../images/dp500-create-a-star-schema-model-image27.png)

25. In the **Native Query** window, review the SELECT statement that reflects the query design.

	*This concept is important. A native query is what Power BI uses to query the data source. To ensure best performance, the database developer should ensure this query is optimized by creating appropriate indexes, etc.*

26. To close the **Native Query** window, select **OK**.

	![](../images/dp500-create-a-star-schema-model-image28.png)


27. Select the **DimProduct** query.

	![](../images/dp500-create-a-star-schema-model-image29.png)

28. Rename the query as **Product**.

	![](../images/dp500-create-a-star-schema-model-image30.png)

29. To filter the query, in the **FinishedGoodsFlag** column header, open the dropdown menu, uncheck **FALSE**.

	![](../images/dp500-create-a-star-schema-model-image31.png)

30. Select **OK**.


31. Remove all columns, except:

	- ProductKey

	- EnglishProductName

	- Color

	- DimProductSubcategory

32. To configure the query to join tables, in the **DimProductSubcategory** column header, select the **Expand** button, and then uncheck **(Select all columns)**.

	*This feature allows joining tables based on foreign key constraints in the source data. The design approach taken by this lab is to join snowflake dimension tables together to produce a denormalized representation of the data.*

33. Uncheck the **Use original column name as prefix**.

	![](../images/dp500-create-a-star-schema-model-image32.png)

34. Check the following two columns:

	- EnglishProductSubcategoryName

	- DimProductCategory

35. Select **OK**.

36. Repeat the previous steps to expand the **DimProductCategory** and introduce the **EnglishProductCategoryName** column.


37. Rename the following columns:

	- **EnglishProductName** as **Product**

	- **EnglishProductSubcategoryName** as **Subcategory**

	- **EnglishProductCategoryName** as **Category**

38. In the **Applied Steps** pane, right-click the last step, and then select **View Native Query**.

	![](../images/dp500-create-a-star-schema-model-image33.png)

39. In the **Native Query** window, review the SELECT statement that reflects the query design.

	*The statement includes nested subqueries to produce the denormalized query result.*

40. To close the **Native Query** window, select **OK**.

41. Verify that the query has five columns.

	*The design of the **Product** query is now complete.*

42. Select the **DimReseller** query.

	![](../images/dp500-create-a-star-schema-model-image34.png)

43. Rename the query as **Reseller**.

44. Remove all columns, except:

	- ResellerKey

	- BusinessType

	- ResellerName

45. Rename the following columns:

	- **BusinessType** as **Business Type** (separate with a space)

	- **ResellerName** as **Reseller**

46. Verify that the query has three columns.

	*The design of the **Reseller** query is now complete.*

47. Select the **DimSalesTerritory** query.

	![](../images/dp500-create-a-star-schema-model-image35.png)

48. Rename the query as **Territory**.

49. Remove all columns, except:

	- SalesTerritoryKey

	- SalesTerritoryRegion

	- SalesTerritoryCountry

	- SalesTerritoryGroup

50. Rename the following columns:

	- **SalesTerritoryRegion** as **Region**

	- **SalesTerritoryCountry** as **Country**

	- **SalesTerritoryGroup** as **Group**

51. Verify that the query has four columns.

	*The design of the **Territory** query is now complete.*

52. Select the **FactResellerSales** query.

	![](../images/dp500-create-a-star-schema-model-image36.png)

53. Rename the query as **Sales**.

54. Remove all columns, except:

	- ResellerKey

	- ProductKey

	- OrderDateKey

	- SalesTerritoryKey

	- OrderQuantity

	- UnitPrice

	![](../images/dp500-create-a-star-schema-model-image37.png)

55. Rename the following columns:

	- **OrderQuantity** as **Quantity**

	- **UnitPrice** as **Price**

56. To add a calculated column, on the **Add Column** ribbon tab, from inside the **General** group, select **Custom Column**.

	![](../images/dp500-create-a-star-schema-model-image38.png)


57. In the **Custom Column** window, in the **New Column Name** box, replace the text with **Revenue**.

	![](../images/dp500-create-a-star-schema-model-image39.png)

58. In the **Custom Column Formula** box, enter the following formula:

	```
	[Quantity] * [Price]
	```


59. Select **OK**.

60. To modify the column data type, in the **Revenue** column header, select **ABC123**, and then select **Decimal Number**.

	![](../images/dp500-create-a-star-schema-model-image40.png)

61. Review the native query, noticing the **Revenue** column calculation logic.

62. Verify that the query has seven columns.

	*The design of the **Sales** query is now complete.*


63. To apply the queries, on the **Home** ribbon tab, from inside the **Close** group, select the **Close &amp; Apply** icon.

	![](../images/dp500-create-a-star-schema-model-image41.png)

	*Each query is applied to create a model table. Because the data connection is using DirectQuery storage mode, only the model structure is created. No data is imported. The model now consists of one table for each query.*

64. In Power BI Desktop, when the queries have been applied, at the bottom-left corner in the status bar, notice that the model storage mode is DirectQuery.

	![](../images/dp500-create-a-star-schema-model-image42.png)

### Organize the model diagram

In this task, you will organize the model diagram to easily understand the star schema design.

1. In Power BI Desktop, at the left, select **Model** view.

	![](../images/dp500-create-a-star-schema-model-image43.png)

2. To resize the model diagram to fit to screen, at the bottom-left, select the **Fit to screen** icon.

	![](../images/dp500-create-a-star-schema-model-image44.png)

3. Drag the tables into position so that the **Sales** fact table is located at the middle of the diagram, and the remain tables, which are dimension tables, are located around the fact table.

4. If any of the dimension tables aren't related to the fact table, use the following instructions to create a relationship:

	- Drag the dimension key column (for example, **ProductKey**) and drop it on the corresponding column of the **Sales** table.

	- In the **Create Relationship** window, select **OK**.


5. Review the final layout of the model diagram.

	![](../images/dp500-create-a-star-schema-model-image45.png)

	*The creation of the star schema model is now complete. There are many modeling configurations that could now be applied, like adding hierarchies, calculations, and setting properties like column visibility.*

6. Optionally, to save the solution, at the top-left, select the disk icon.

7. In the **Save As** window, go to the **D:\DP500\Create a star schema model\Solution** folder.

8. In the **File name** box, enter **Sales Analysis**.

	![](../images/dp500-create-a-star-schema-model-image46.png)

9. Select **Save**.

10. Close Power BI Desktop.

### Stop the SQL pool

In this task, you will stop the SQL pool.

1. In a web browser, go to [https://portal.azure.com](https://portal.azure.com/).

2. Locate the SQL pool.

3. Stop the SQL pool.

	TODO: Provide an image
