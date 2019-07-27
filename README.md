# Capstone Project
## I use the data files of SAS and CSV for data processing and verification
## See [Capstone Project]Capstone Project Template.ipynb for details

## DATA desc
#### Describe and Gather Data 
The I94 immigration [data](https://travel.trade.gov/research/reports/i94/historical/2016.html) comes from the US National Tourism and Trade Office. It is provided in SAS7BDAT [format](https://cran.r-project.org/web/packages/sas7bdat/vignettes/sas7bdat.pdf) which is a binary database storage format. Some relevant attributes include:

* `i94yr` = 4 digit year
* `i94mon` = numeric month
* `i94cit` = 3 digit code of origin city
* `i94port` = 3 character code of destination USA city
* `arrdate` = arrival date in the USA
* `i94mode` = 1 digit travel code
* `depdate` = departure date from the USA
* `i94visa` = reason for immigration

The temperature [data](https://www.kaggle.com/berkeleyearth/climate-change-earth-surface-temperature-data) comes from Kaggle. It is provided in csv format. Some relevant attributes include:

* `AverageTemperature` = average temperature
* `City` = city name
* `Country` = country name
* `Latitude`= latitude
* `Longitude` = longitude

#### 3.1 Conceptual Data Model

The first dimension table will contain events from the I94 immigration data. The columns below will be extracted from the immigration dataframe:
* `i94yr` = 4 digit year
* `i94mon` = numeric month
* `i94cit` = 3 digit code of origin city
* `i94port` = 3 character code of destination city
* `arrdate` = arrival date
* `i94mode` = 1 digit travel code
* `depdate` = departure date
* `i94visa` = reason for immigration

The second dimension table will contain city temperature data. The columns below will be extracted from the temperature dataframe:

* `i94port` = 3 character code of destination city (mapped from immigration data during cleanup step)
* `AverageTemperature` = average temperature
* `City` = city name
* `Country` = country name
* `Latitude`= latitude
* `Longitude` = longitude

The final table will contain information from the I94 immigration data joined with the city temperature data on `i94port`:
* `i94yr` = 4 digit year
* `i94mon` = numeric month
* `i94cit` = 3 digit code of origin city
* `i94port` = 3 character code of destination city
* `arrdate` = arrival date
* `i94mode` = 1 digit travel code
* `depdate` = departure date
* `i94visa` = reason for immigration
* `AverageTemperature` = average temperature of destination city

The tables will be saved to Parquet files partitioned by city (`i94port`). 

#### 3.2 Mapping Out Data Pipelines

The pipeline steps are described below:
1. Clean I94 data as described in step 2 to create Spark dataframe `df_immigration` for each month
2. Clean temperature data as described in step 2 to create Spark dataframe `df_temp` (already performed)
3. Create immigration dimension table by selecting relevant columns from `df_immigration` and write to parquet file partitioned by `i94port`
4. Create temperature dimension table by selecting relevant columns from `df_temp` and write to parquet file partitioned by `i94port`
5. Create final table by joining immigration and temperature dimension tables on `i94port` and write to parquet file partitioned by `i94port`



#### project runnner
- The project uses Spark stand-alone mode to process data, and uses some functions to clean up data.