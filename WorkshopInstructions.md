![](https://github.com/iuliaferoli/ADF_workshop/blob/master/img/banner.png?raw=true)

## ADF introduction & data quality lab.
## Lab 1


* You can create the following resources in your sandbox resource group.
* Deploy everything in the same region, that is also closest to you geographically (example: westerneurope)
* Recommended to add your initials or another distinctive element to the naming of your resources if multiple people use the same resource group

In this lab you will:

A. Set up Azure environemnt

B. Connect to your data source

C. Create a pipeline with data transformation activities

D. Write your transformed data back to your data lake

### A.1 Create and populate data lake

1. Create a data lake (storage account with hierarchical namespace enabled) [documentation](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-create?tabs=azure-portal#create-a-storage-account)

![](https://github.com/iuliaferoli/ADF_workshop/blob/master/img/createstorage.PNG?raw=true)

2. Create two containers: `input` and `output` (where you will later write to through an ADF pipeline) [documentation](https://docs.microsoft.com/en-us/azure/data-factory/quickstart-create-data-factory-portal#create-a-blob-container)

![](https://github.com/iuliaferoli/ADF_workshop/blob/master/img/createcontainers.png?raw=true)    

3. Download the rotten tomatoes dataset from [the repository](https://github.com/iuliaferoli/ADF_workshop/blob/master/data/all_movie.csv)
4. Upload it to the `input` folder of the data lake (with an easy name)
    
### A.2 Create Azure Data Factory instance in the same resource group
1. Create an ADF instance [documentation](https://docs.microsoft.com/en-us/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory)

![](https://github.com/iuliaferoli/ADF_workshop/blob/master/img/createadf.PNG?raw=true)

2. Open the ADF Author & Monitor page.

### B. Connect to the data

![](https://github.com/iuliaferoli/ADF_workshop/blob/master/img/createinadf.png?raw=true)

3. From the `Manage` tab, create a linked service to connect to the data lake you deployed [documentation](https://docs.microsoft.com/en-us/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service)
    1. Data store type will be Data Lake Gen 2 (as the storage account you made)
    2. Select your subscription and storage account
4. From the `Author` tab create a dataset, 
    1. Data store type will be Data Lake Gen 2 (as the storage account you made)
    2. format will be CSV (Delimited Text)
    3. select the Linked Service you made in previous step
    4. choose the file you uploaded in the storage account and enable first row as header
    
![](https://github.com/iuliaferoli/ADF_workshop/blob/master/img/createdataset.PNG?raw=true)
You know have your input dataset.

### C. Create and run pipeline & dataflow

9. Go on the "Author" tab on the left of the screen and create a new Pipeline
10. Create a Data Flow activity in the pipline by selecting it from under "Data Transformation" (or searching for it) and dragging it onto the middle screen.



11. Choose create new data flow in the UI pop-up; and select Mapping Data Flow
12. Click on `Add Source` step at the beginning of the data flow.
* Name it
* Select Dataset as Source type
* Choose the dataset you created in B

![](https://github.com/iuliaferoli/ADF_workshop/blob/master/img/createdataflow.png?raw=true)

Create new steps in the data flow by clicking on the `+` at the bottom right of each step.
Create the following transformatins:
1. `Select` step to drop the `Rating` column and rename `Rotton Tomato` to `Rotten Tomato`
2. `Filter` step to only keep movies released after 1950. In the Filter box use the formula ` toInteger(year) > 1950`
3. `Derived column` step to split the current `genres` column to only the first named genre. 
    * Give the new derived column a name like `PrimaryGenre` and use this formula `iif(locate('|',genres)>1,left(genres,locate('|',genres)-1),genres)`

4. `Sink` step at the end of your data flow, and within the settings **create a new dataset** (data lake gen 2, csv, same linked service as before) that connects to the **output container** you created in section A. 

5. Select ```Publish All``` to commit all the changes you made, 
6. Run your pipeline by selecting ```Trigger Now```

![](add the link to the trigger now image here)
    
### D. Create a Data Profile with Statistics
1. Create a  ```New Branch``` from the last step in the data flow you made before the sink, and then add a ```Aggregate``` step. Within the settings, select the ```Aggregates``` option instead of Group By, press the ```+``` button and then ```Add Column Pattern```. Fill in the remaining fields as follows:

![](add picture here)

You can also copy the values from here - Each column that matches ```true()```

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




