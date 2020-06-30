# Workshop Day 
## ADF introduction & data quality lab.
## Lab 1

* You can create the following resources in your sandbox resource group.
* Deploy everything in the same region, that is also closest to you geographically (example: westerneurope)
* Recommended to add your initials or another distinctive element to the naming of your resources if multiple people use the same resource group

this code 

In this lab you will:

A. Set up Azure environemnt

B. Connect to your data source

C. Create a pipeline with data transformation activities

D. Write your transformed data back to your data lake

### A.1 Create and populate data lake

1. Create a data lake (storage account with hierarchical namespace enabled) [documentation](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-create?tabs=azure-portal#create-a-storage-account)
2. Create two containers: `input` and `output` (where you will later write to through an ADF pipeline) [documentation](https://docs.microsoft.com/en-us/azure/data-factory/quickstart-create-data-factory-portal#create-a-blob-container)
3. Download the rotten tomatoes dataset from [kaggle](https://www.kaggle.com/ayushkalla1/rotten-tomatoes-movie-database/data?select=all_movie.csv)
4. Unzip the csv and upload it to the input folder of the data lake (with an easy name)
    
### A.2 Create Azure Data Factory instance in the same resource group
1. Create an ADF instance [documentation](https://docs.microsoft.com/en-us/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)
2. Open the ADF Author & Monitor page.

### B. Connect to the data
3. From the `Manage` tab, create a linked service to connect to the data lake you deployed [documentation](https://docs.microsoft.com/en-us/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service)
    1. Data store type will be Data Lake Gen 2 (as the storage account you made)
    2. Select your subscription and storage account
4. From the `Author` tab create a dataset, 
    1. Data store type will be Data Lake Gen 2 (as the storage account you made)
    2. format will be CSV (Delimited Text)
    3. select the Linked Service you made in previous step
    4. choose the file you uploaded in the storage account and enable first row as header
    
You know have your input dataset.

### C. Create and run pipeline & dataflow
9. Go on the "Author" tab on the left of the screen and create a new Pipeline
10. Create a Data Flow activity in the pipline by selecting it from under "Data Transformation" (or searching for it) and dragging it onto the middle screen.
11. Choose create new data flow in the UI pop-up; and select Mapping Data Flow
12. Select the dataset you created in the previous section as input.

From this Data Flow screen you can now create extra steps with the ```+``` button at the bottom right of each activity shape. The overview of possible transformations is found [here](https://docs.microsoft.com/en-us/azure/data-factory/data-flow-transformation-overview)

13. Follow the instructions from Task 3 in this [tutorial](https://github.com/microsoft/ignite-learning-paths-training-data/tree/main/data30/demos#exercise-1-transforming-data-with-mapping-data-flow) to create a couple of data transformations. You don't have to complete all of them. 
    
14. Add a ```sink``` at the end of your data flow, and within the settings **add a new .CSV dataset** that connects to the **output container** you created in section A. 

15. Select ```Publish All``` to commit all the changes you made, and then run your pipeline by selecting ```Trigger Now```
![](add the link to the trigger now image here)
    
### D. Create a Data Profile with Statistics
1. Create a branch from the last step in the data flow you made before the sink, and select the Aggregate transformation. Select the Aggregate option instead of Group By, and then add a column pattern below from the + button. Fill in the empty fields as shown below:
Each column that matches ```true()```

   | Column  | Expression |
    | ------------- | ------------- |
    | $$+'-NotNull'  | countIf(!isNull($$))  |
    | $$+'-Null'   | countIf(isNull($$)) | 

![](need to create image for this one)

   Add another column pattern below that matches: ```type=='double'||type=='integer'||type=='short'||type=='decimal'```
   And create the following columns with corresponding expressions:

  | Column  | Expression |
   | ------------- | ------------- |
   | $$+'-stddev'  | round(stddev($$),2)  |
   | $$+'-min'   | min($$) | 
   | $$+'-max'   | max($$) |
   | $$+'-average'   | round(avg($$),2) |    
   | $$+'-variance'   | round(variance($$),2) |
    
And another column pattern that matches: ```type=='string'```
With the following columns and expressions:
      
  | Column  | Expression |
   | ------------- | ------------- |
   | $$+'-MaxLength'  | max(length($$))  |
   
![](show one image with all of them)



