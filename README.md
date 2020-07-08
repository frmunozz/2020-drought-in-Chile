# 2020-drought-in-Chile
Analysis on the current drought that is affecting Chile. [Francisco Munoz, Felipe Kruuse, Cristian Ahumada. Group 17]

# Overview
It is well know that in the past decades a mega drought has been affecting some parts of Chile, affecting severely agriculture and cattle raising business among others. The goal of this project is to check, based on historical data, how relevant it is the current drought that is affecting our country. For that purpose, precipitation data captured from stations located throughout the country will be used to compute cumulated precipitation over the years. Questions we would like to answer are: what regions are the most affected by the drought? Has any similar drought occurred in the past? [TODO: more questions]

# Data
Our exploration is done using the [daily precipitation dataset](http://www.cr2.cl/datos-de-precipitacion/) generated by The Center for Climate and Resilience Research. This dataset contains 1249 stations from Chile and other countries from South America with daily precipitation records between years 1900-2018. Every daily record corresponds to the cumulated daily precipitation in millimeters, where a missing day is represented by "-9999". The format of the data is a matrix in a text file in the form of a transposed table (data sorted in rows instead of columns) where the first 14 rows contain information such as station code, coordinates or source, and the following rows contain the observed measurements corresponding to each period in time, for all the stations considered. This dataset contains 53.794.430 records of daily precipitation, where some of them could be missing (and thus registered as -9999). This number is compacted in the form of a matrix with 1249 columns and approximately 43.080 rows. We choose this dataset because it collects data from different sources such as DGA (Dirección general del Agua), DMC (Dirección meteorológica de chile) and GHCN (Global Historical Climatology Network) resulting in a very complete dataset.

The dataset does not only contains the main file with the records, it also contains an additional file with information related to each station such as: coordinates, source, institution, date of the start of observations, etc. This information is also present in the first 14 rows of the main file, but here it is present as a normal table that will be helpful during  the data exploration.


# Methods

First of all, we need to preprocess the original data file, transpose the table (so the data are presented by columns) and collapse redundant columns. In particular, after transposing, the table will have one column for each day over the years, resulting in more than 40.000 columns. This number can be collapsed to only 2 columns, one for the date and one for cumulated precipitation. We will also delete all columns that will not be necessary. The resulting table will have the following headers:

| station code | date | cum. precip. |
|--------------|------|--------------|

This data preprocessing is done using apache spark core since the rotation of the matrix could be demanding in memory. With this resulting table we can do a filter for the missing data deleting the rows where "cum. precip == -9999", which is done in the same python spark code preprocessing.py. The resulting table after this filter has 11.454.302 rows.

Having this transformed and cleaned table, we start with the data exploration using apache Pig (since the number of rows can be handled in memory). We computed yearly accumulations when less than 5% of the days are missing. As result, we got a table with 24.082 rows and headers:

| station code | year | cum. precip. |
|--------------|------|--------------|

The code used for this computation can be seen in ----.pig.

As a next step, we have to define geographical zones to group stations and calculate a mean and standard deviation of the yearly cumulated precipitation over each zone. For simplicity, as a first exploration, we will use the [natural regions](http://countrystudies.us/chile/37.htm) of Chile as the geographical zones. To get the coordinates for each station we will use the additional file present in the original dataset with the information for each station. The pig file ---.pig contains the code used to group the stations by their corresponding geographical zone, generating a table with headers:

| station code | zone name |
|--------------|-----------|

Then, in ---.pig we join these two tables and calculate different values such as mean, standard deviation, maximum and minimum, all of them for the yearly cumulated precipitation for each geographical zone, constructing a table with headers:

| zone name | year | mean | std | max | min |
|-----------|------|------|-----|-----|-----|

Finally, we plot this data to show the behavior of them and perform the corresponding analysis. We also repeat this process for seasonal precipitation, calculating the cumulated precipitation for summer, autumn, winter and spring. [TODO: generaremos otras zonas geograficas diferentes a las regiones naturales?]

# Results
[TODO]

# Conclusions
[TODO]

