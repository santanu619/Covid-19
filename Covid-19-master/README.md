# Covid-19
As we all know that since early 2020, the entire world is suffering from the covid-19 outbreak and the covid-19 takes the world by storm and many families have been suffering and lost their family members due to the outbreak of covid-19. So, I have decided to do a prediction and generating a report based on covid-19 outbreak that has been terrorising the world and for that I will create a project based on covid-19 which will predict it’s outbreak and make some predictions based on that and we can generate it’s report after the prediction using power bi tool.

### Prerequisites:-
1. Azure Account.
2. Storage Account.
3. Blob Storage.
4. Data Lake Gen 2.
5. Azure Data Factory.
6. Workflow.
7. DataBricks.
8. HDInsight.
9. SQL Database.
10. Power BI.

## Data Lake
- A data lake is a storage repository that holds a vast amount of raw data in its native format until it is needed. While a hierarchical data warehouse stores data in files or folders, a data lake uses a flat architecture to store data. Each data element in a lake is assigned a unique identifier and tagged with a set of extended metadata tags. When a business question arises, the data lake can be queried for relevant data, and that smaller set of data can then be analyzed to help answer the question.
- The term data lake is often associated with Hadoop-oriented object storage. In such a scenario, an organization's data is first loaded into the Hadoop platform, and then business analytics and data mining tools are applied to the data where it resides on Hadoop's cluster nodes of commodity computers.
- Like big data, the term data lake is sometimes disparaged as being simply a marketing label for a product that supports Hadoop. Increasingly, however, the term is being used to describe any large data pool in which the schema and data requirements are not defined until the data is queried.
- Data Lake to be built with the following data to aid Data Scientists to predict the spread of the virus/mortality:- 
    - Confirmed cases 
    - Mortality 
    - Hospitalization/ICU Cases 
    - Testing Numbers 
    - Country’s population by age group
## Data Warehouse
- A data warehouse is a type of data management system that is designed to enable and support business intelligence (BI) activities, especially analytics. Data warehouses are solely intended to perform queries and analysis and often contain large amounts of historical data. The data within a data warehouse is usually derived from a wide range of sources such as application log files and transaction applications.
- A data warehouse centralizes and consolidates large amounts of data from multiple sources. Its analytical capabilities allow organizations to derive valuable business insights from their data to improve decision-making. Over time, it builds a historical record that can be invaluable to data scientists and business analysts. Because of these capabilities, a data warehouse can be considered an organization’s “single source of truth.”
- Data Warehouse to be built with the following data to aid Reporting on Trends:- 
    - Confirmed cases 
    - Mortality 
    - Hospitalization/ ICU Cases 
    - Testing Numbers

1. Here, first we need to extract data from ecdc(European Centre for Disease Prevention and Control) and we need to create a storage account and inside that, a blob storage container to save the data that we have collected. 
2. Then we need to perform ingestion of data for processing and then those data can be saved in the Azure Data Lake Storage Gen2 .
3. Then we can use two modules of data flow to analyse the data and then we can transform the data and for data transformation, we need to use Workflow, HDInsight or DataBricks and in our case, I am going to use the Workflow.
4. After the process of transformation of data, we will again save that data in the Azure Data Lake Storage Gen2 and we can predict the models using ML Models.
5. And a subset of data after transformation can be stored in the Azure SQL Database and we can use that subset of data for publishing and making reports using Power BI.

## Working Flow of the Project
- In this project, we are going to take the data from the ecdc website and we are ingesting the data from two sources i.e. blob storage and http. 
- And we are going to take the dataset such as hospital_admissions.csv, cases_and_deaths.csv, testing.csv and country_wise.csv from the ecdc website through http and we are going to take the population data from the Eurostat and will store it in the blob storage.
- In data ingestion part, first we need to perform the ingestion based on population data in blob storage and for that we need to create a linked service to the blob storage and then will create a copy data pipeline where I will copy the population data from the blob storage to the Azure Data Lake Storage gen 2. This process is just for analysing purposes and we will not be using this extracting data in the future steps.
- Then we need to perform the ingestion based on hospital_admissions data, cases_and_deaths data, testing data, country_wise data and for that we need to create a linked service to the http and we need to give the base URL over there and then after that the relative path to take the data from. Then we will create a copy data pipeline where I will copy all the data from the http to the Azure Data Lake Storage gen 2. And, we need this data from the Data Lake for the transformation which we can do using Dataflow, HDInsight or Databricks.
- After the ingestion of data, we need to send the data for transformation and for that, we will use Dataflow and then we need to transform both cases_and_deaths data and hospital_admissions data.
- So first, we need to transform cases and deaths data and for that, we will go to the Dataflow part and we need to add the source and for that, we need to connect to the Azure Data Lake Storage gen 2 and then we can do the transformation of cases_and_deaths data.
- Then, we need to transform the hospital_admissions data and for that, we will go to the data flow part and we need to add the source and for that, we need to connect to the Azure Data Lake Storage gen 2 and then we need to transform the data in two ways i.e. daily and weekly.
- Then, we can mount the Azure Data Lake Storage gen 2 account and we can do that using Databricks and doing an app registration under Azure Active Directory.
- Then, we need to copy a subset of the transform data into the Azure SQL Database and we can make reports and graphs of this data using Power BI.

## Data Ingestion
### Population Data
Ingest “population by age” for all EU Countries into the Data Lake to support the machine learning models to predict increase in Covid-19 mortality rates.
- This dataset will be used for the ingestion part and this sample data can be stored in the Blob Storage container from the Storage Account.  
- This dataset contains the country code and their geographical time and the count of the population for each country from the year 2008 till date.
- We can use this data for our understanding purposes like how the flow of the pipeline works and how we can implement that using Azure Data Factory.

For the copy activity, first we need to store the data in the azure blob storage which is a zipped file in TSV format and we can call it as the Source File.
The target file is also a TSV File which should be copied from the Azure Blob Storage using Azure Data Factory.
Then, we need to create a linked service for the source file in the blob storage and for that we have two options:-
* Either we can create that by going to the manage option and create a new linked service and give all the details.
* Or, we can create that during the creation of the pipeline.

We need to link the Source Data Set from the Azure Blob Storage in the linked service.

Then, we need to go to the pipeline option and select a new pipeline for copying the data and their we need to give the Source Dataset link which is from the Azure Blob Storage and we also need to give the sink path which is the container in the Azure Data Lake Storage gen 2 and after the completion of the task, we can see the data move to the Azure Data Lake Storage gen 2 account.

For handling the real world scenario for copying the data, following scenarios may be arises:-
* Execute the copy activity when the file becomes available.
* Execute copy activities only if file contents are as expected.
* Delete the source file on successful copy.
For this, we need to add some triggers based on our requirements and we can either create the trigger or we can add a manual trigger.

Triggers are another way that we can execute a pipeline run. Triggers represent a unit of processing that determines when a pipeline execution needs to be kicked off. Currently, Data Factory supports three types of triggers: Schedule trigger: A trigger that invokes a pipeline on a wall-clock schedule.
The following types of triggers:-
- **Schedule Trigger**
    - Runs on a calendar/Clock 
    - Supports periodic and specific times 
    - Trigger to Pipeline is Many to Many 
    - Can only be scheduled for a future time to star
 
- **Tumbling Window Trigger**
    - Runs at periodic intervals 
    - Windows are fixed sized, non-overlapping 
    - Trigger to Pipeline is one to one 
    - Can be scheduled for the past windows/ slices 
 
- **Event Trigger**
    - Runs in response to events 
    - Events can be creation or deletion of Blobs/ Files 
    - Trigger to Pipeline is Many to Many
 
A pipeline run in Azure Data Factory and Azure Synapse defines an instance of a pipeline execution. For example, say we have a pipeline that executes at 8:00 AM, 9:00 AM, and 10:00 AM. In this case, there are three separate runs of the pipeline or pipeline runs. Each pipeline run has a unique pipeline run ID. A run ID is a GUID that uniquely defines that particular pipeline run.

Pipeline runs are typically instantiated by passing arguments to parameters that we define in the pipeline. We can execute a pipeline either manually or by using a trigger.

Pipelines and triggers have a many-to-many relationship (except for the tumbling window trigger).Multiple triggers can kick off a single pipeline, or a single trigger can kick off multiple pipelines.

 
An event-based trigger runs pipelines in response to an event. There are two flavors of event based triggers.
- Storage event trigger runs a pipeline against events happening in a Storage account, such as the arrival of a file, or the deletion of a file in Azure Blob Storage account.
- Custom event trigger processes and handles custom topics in Event Grid.

### ECDC Data
The process in the ingestion of data:-
- Create Initial Pipeline 
- Pipeline Variables
- Pipeline Parameters
- Lookup Activity
- For Each Activity 
- Linked Service Parameters
- Metadata driven pipeline
#### Recent changes in ECDC data
- Granularity of the data changed from daily to weekly 
- File structure is also different as a result 
- Use GIT Repo - https://github.com/cloudboxacademy/covid19 
### Data Ingestion Requirements
- Covid-19 new cases and deaths by Country 
- Covid-19 Hospital admissions & ICU cases 
- Covid-19 Testing Numbers 
- Country Response to Covid-19
## Parameters & Variables 
- **Parameters** are external values passed into pipelines, datasets or linked services. The value cannot be changed inside a pipeline. 
- **Variables** are internal values set inside a pipeline. The value can be changed inside the pipeline using Set Variable or Append Variable Activity.

## DATA TRANSFORMATION
### Data Flow
We need to perform the following transformation in the dataflow:-
- Source Transformation 
- Pivot Transformation 
- Select Transformation 
- Sink Transformation 
- Lookup Transformation 
- Filter Transformation 
- Create Pipeline

**It supports:-**
- Code free data transformations.
- Executed on Data Factory managed Databricks Spark clusters. 
- Benefits from Data factory scheduling and monitoring capabilities. 
 
**It’s types:-**
- Data Flow
- Wrangling Data Flow

**Data Flow Limitations:-**
- Only available in some regions.
- Not suitable for very complex logic.
- Limited set of connectors available.

### Transform Cases and Deaths Data
- Initially, we will take the raw file from the ECDC data and based on some modification, we will transform the file.
- And for that, we will use source transformation and we will add the source file i.e. ECDC Cases_and_deaths data and we need to select the column that we need to update or drop using select transformations.
- In this part, we are going to keep the country column as it is, we will add a lookup file that contains country_code_2_digit in the lookup file in the data lake storage container and we will add that file for lookup transformation and we need to update the column name country_code to country_code_3_digit and we will drop the continent column and we will keep the population column as it is and we will modify the indicator and daily_count as cases_count and death_count. We will update the column named date and reported_date and we will drop the column rate_14_day.
- After performing all the transformations, we need to add a sink and then we will perform the pipeline using data flow.

### Hospital Admissions Data(Daily)
- Initially, we will take the raw file from the ECDC data and based on some modification, we will transform the file.
- And for that, we will use source transformation and we will add the source file i.e. ECDC hospital_admissions data and we need to select the column that we need to update or drop using select transformations.
- First we need to update for daily data. In this part, we are going to keep the country column as it is, we will add a lookup file that contains country_code_2_digit and country_code_3_digit in the lookup file as well as the population data in the lookup file in the data lake storage container and we will add that file for lookup transformation. We need to update the date as reported_date  and we need to add hospital_occupancy_count and ICU_occupancy_count. After performing all the transformations, we need to add a sink and then we will perform the pipeline using data flow.

### Hospital Admissions Data(Weekly)
- Initially, we will take the raw file from the ECDC data and based on some modification, we will transform the file.
- And for that, we will use source transformation and we will add the source file i.e. ECDC hospital_admissions data and we need to select the column that we need to update or drop using select transformations.
- We need to update weekly data. In this part, we are going to keep the country column as it is, we will add a lookup file that contains country_code_2_digit and country_code_3_digit in the lookup file as well as the population data in the lookup file in the data lake storage container and also we need to add reported_year_week,reported_week_start_date and reported_week_end_date in the lookup file. We will add that file for lookup transformation. We need to add hospital_occupancy_count and ICU_occupancy_count. After performing all the transformations, we need to add a sink and then we will perform the pipeline using data flow.

**The project link:-**
https://www.youtube.com/playlist?list=PL7Lq5E3eAX4A_wwGnMuMqB88cOl2MFX6U
