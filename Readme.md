![](https://github.com/iuliaferoli/ADF_workshop/blob/master/img/banner.png?raw=true)

## ADF introduction & data quality lab.
## Lab 1


* You can create the following resources in your sandbox resource group.
* Deploy everything in the same region, that is also closest to you geographically (example: westerneurope)
* Recommended to add your initials or another distinctive element to the naming of your resources if multiple people use the same resource group

## Tasks Summary

A. Set up Azure environment

B. Connect to your data source

C. Create a pipeline with data transformation activities

D. Write your transformed data back to your data lake

## A.1 Create and populate data lake

1. Create a data lake (storage account with hierarchical namespace enabled) [documentation](https://docs.microsoft.com/en-us/azure/storage/common/storage-account-create?tabs=azure-portal#create-a-storage-account)

![](https://github.com/iuliaferoli/ADF_workshop/blob/master/img/createstorage.PNG?raw=true)

2. Create two containers: `input` and `output` (where you will later write to through an ADF pipeline) [documentation](https://docs.microsoft.com/en-us/azure/data-factory/quickstart-create-data-factory-portal#create-a-blob-container)
3. Download the rotten tomatoes dataset from [the data folder](https://github.com/iuliaferoli/ADF_workshop/blob/master/data/all_movie.csv)
4. Upload it to the `input` folder of the data lake (with an easy name)
    
![](https://github.com/iuliaferoli/ADF_workshop/blob/master/img/createcontainers.png?raw=true)  

## A.2 Create Azure Data Factory instance in the same resource group
1. Create an ADF instance. ([documentation](https://docs.microsoft.com/en-us/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory))

![](https://github.com/iuliaferoli/ADF_workshop/blob/master/img/createadf.PNG?raw=true)

2. Open the ADF Author & Monitor page.

![](https://github.com/iuliaferoli/ADF_workshop/blob/master/img/authormonitor.png?raw=true)

## B. Connect to the data

![](https://github.com/iuliaferoli/ADF_workshop/blob/master/img/createinadf.png?raw=true)

1. From the `Manage` tab, create a linked service to connect to the data lake you deployed [documentation](https://docs.microsoft.com/en-us/azure/data-factory/quickstart-create-data-factory-portal#create-a-linked-service)
    * Data store type will be Data Lake Gen 2 (as the storage account you made)
    * Select your subscription and storage account
    
![](https://github.com/iuliaferoli/ADF_workshop/blob/master/img/linkedservice.png?raw=true)


2. From the `Author` tab create a dataset, 
    * Data store type will be Data Lake Gen 2 (as the storage account you made)
    * format will be CSV (Delimited Text)
    * select the Linked Service you made in previous step
    * choose the file you uploaded in the storage account and enable first row as header

![](https://github.com/iuliaferoli/ADF_workshop/blob/master/img/Dataset.png?raw=true)

You now have your input dataset.

### C.1 Create pipeline

1. Go on the `Author` tab on the left of the screen and create a new Pipeline
2. Create a "Data Flow" activity in the pipline by selecting it from under "Data Transformation" (or searching for it) and dragging it onto the middle screen.
3. Choose "Create new data flow" in the UI pop-up; and select Mapping Data Flow


![](https://github.com/iuliaferoli/ADF_workshop/blob/master/img/createdataflow.png?raw=true)



### C.2 Create data flow

1. Click on `Add Source` step at the beginning of the data flow.
* Name it
* Select Dataset as Source type
* Choose the dataset you created in B

#### Create the new steps in the data flow by clicking on the `+` at the bottom right of each step. 

2. `Select` step to drop the `Rating` column and rename `Rotton Tomato` to `Rotten Tomato`
3. `Filter` step to only keep movies released after 1950. In the Filter box use the formula 
```
toInteger(year) > 1950
```
4. `Derived column` step to split the current `genres` column to only the first named genre. 
* Give the new derived column a name like `PrimaryGenre` and use this formula 

```
iif(locate('|',genres)>1,left(genres,locate('|',genres)-1),genres)
```

5. `Sink` step at the end of your data flow, and within the settings:
* **create a new dataset** with properties: 
* data lake gen 2, csv, same linked service as before that connects to the **output container** you created in section A. 



![](https://github.com/iuliaferoli/ADF_workshop/blob/master/img/dataflow.PNG?raw=true)



### C.3 Publish & Run

1. Select ```Publish All``` to commit all the changes you made, 
2. Run your pipeline by selecting ```Trigger Now```



![](https://github.com/iuliaferoli/ADF_workshop/blob/master/img/triggernow.png?raw=true)
 
 

    
### D. Create a Data Profile with Statistics
1. Create a  ```New Branch``` from the last step in the data flow you made before the sink


![](https://github.com/iuliaferoli/ADF_workshop/blob/master/img/newbranch.png?raw=true)


2. Add an `Derived Column` step to convert the Year column to integer (to get the best of the statistics analysis later)
3. Add an ```Aggregate``` step. Within the settings,



![](https://github.com/iuliaferoli/ADF_workshop/blob/master/img/secondbranch.PNG?raw=true)



4. Select the ```Aggregates``` option instead of Group By, press the ```+``` button and then ```Add Column Pattern```. Fill in the remaining fields as follows for three column patterns in total:



![](https://github.com/iuliaferoli/ADF_workshop/blob/master/img/summarystatistics.png?raw=true)



You can also copy the values from here:

First Column Pattern - Each column that matches ```true()```
With the following columns and expressions below:

  | Column  | Expression |
  | ------------- | ------------- |
  | $$+'-NotNull'  | countIf(!isNull($$))  |
  | $$+'-Null'   | countIf(isNull($$)) | 

Second Column Pattern - Each column that matches ```type=='double'||type=='integer'||type=='short'||type=='decimal'```

  | Column  | Expression |
   | ------------- | ------------- |
   | $$+'-stddev'  | round(stddev($$),2)  |
   | $$+'-min'   | min($$) | 
   | $$+'-max'   | max($$) |
   | $$+'-average'   | round(avg($$),2) |    
   | $$+'-variance'   | round(variance($$),2) |
    
Third Column Pattern - Each column that matches ```type=='string'```
      
  | Column  | Expression |
   | ------------- | ------------- |
   | $$+'-MaxLength'  | max(length($$))  |


* If you have the data flow debug enabled you can directly see the generated statistics under `Data preview`   

5. Add another ```Sink``` as a last step to this branch, and write the data to a ```New Dataset``` (also a CSV file) in the same Data Lake.






