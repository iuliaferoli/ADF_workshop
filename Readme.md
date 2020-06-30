# Workshop Day 
## ADF introduction & data quality lab.

### Lab 1

You can create the following resources in your sandbox resource group. Deploy everything in the same region, that is also closest to you geographically (example: westerneurope)

#### Create and populate data lake
1. Create a data lake (storage account with hierarchical namespace enabled) [documentation](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-create?tabs=azure-portal#create-a-storage-account)
2. Create two containers: "input" and "output" (where you will later write to through an ADF pipeline) [documentation](https://docs.microsoft.com/en-us/azure/data-factory/quickstart-create-data-factory-portal#create-a-blob-container)
3. Download the rotten tomatoes dataset from [kaggle](https://www.kaggle.com/ayushkalla1/rotten-tomatoes-movie-database/data?select=all_movie.csv)
4. Upload the csv to the input folder of the data lake (with an easy name)
    
#### Create Azure Data Factory instance in the same resource group
1. Create an ADF instance [documentation](https://docs.microsoft.com/en-us/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)
2. Open the ADF Author & Monitor page and go into the "Author" tab (pencil on the left of the page)

#### Connect to the data
3. Create a linked service to connect to the data lake you deployed [documentation](https://docs.microsoft.com/en-us/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service)
4. Create a dataset, 
    * type will be Data Lake Gen 2 (as the storage account you made)
    * format will be CSV (Delimited Text)
    * select the Linked Service you made in previous step
    * choose the file you uploaded in the storage account and enable first row as header
You know have your input dataset.

#### Create a pipeline
5. Go on the "Author" tab on the left of the screen and create a new Pipeline
6. Create a Data Flow activity in the pipline by selecting it from under "Data Transformation" (or searching for it) and dragging it onto the middle screen.
7. Choose create new data flow in the UI pop-up; and select Mapping Data Flow
8. Select the dataset you created in the previous section as input.
