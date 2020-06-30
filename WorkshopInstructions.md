# Workshop Day 
## ADF introduction & data quality lab.
## Lab 1

You can create the following resources in your sandbox resource group. Deploy everything in the same region, that is also closest to you geographically (example: westerneurope)
In this lab you will:

A. Set up Azure environemnt

B. Connect to your data source

C. Create a pipeline with data transformation activities

D. Write your transformed data back to your data lake

### A.1 Create and populate data lake

1. Create a data lake (storage account with hierarchical namespace enabled) [documentation](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-create?tabs=azure-portal#create-a-storage-account)
2. Create two containers: "input" and "output" (where you will later write to through an ADF pipeline) [documentation](https://docs.microsoft.com/en-us/azure/data-factory/quickstart-create-data-factory-portal#create-a-blob-container)
3. Download the rotten tomatoes dataset from [kaggle](https://www.kaggle.com/ayushkalla1/rotten-tomatoes-movie-database/data?select=all_movie.csv)
4. Upload the csv to the input folder of the data lake (with an easy name)
    
### A.2 Create Azure Data Factory instance in the same resource group
1. Create an ADF instance [documentation](https://docs.microsoft.com/en-us/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)
2. Open the ADF Author & Monitor page and go into the "Author" tab (pencil on the left of the page)

### B. Connect to the data
3. Create a linked service to connect to the data lake you deployed [documentation](https://docs.microsoft.com/en-us/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service)
4. Create a dataset, 
    1. type will be Data Lake Gen 2 (as the storage account you made)
    2. format will be CSV (Delimited Text)
    3. select the Linked Service you made in previous step
    4. choose the file you uploaded in the storage account and enable first row as header
    
You know have your input dataset.

### C. Create & run pipeline
5. Go on the "Author" tab on the left of the screen and create a new Pipeline
6. Create a Data Flow activity in the pipline by selecting it from under "Data Transformation" (or searching for it) and dragging it onto the middle screen.
7. Choose create new data flow in the UI pop-up; and select Mapping Data Flow
8. Select the dataset you created in the previous section as input.

From this Data Flow screen you can now create extra steps with the + sign at the bottom right of each activity shape. The overview of possible transformations is found [here](https://docs.microsoft.com/en-us/azure/data-factory/data-flow-transformation-overview)

9. For this lab we want to create at last 3 different transformations of your choice to fully explore the service and for an extra challenge. 
    * However, if you prefer to follow specific steps you can also recreate these steps from the [tutorial here](https://github.com/microsoft/ignite-learning-paths-training-data/tree/main/data30/demos#exercise-1-transforming-data-with-mapping-data-flow) 
