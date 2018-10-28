# Effects of peer-to-peer short-term rental to the housing market in San Francisco
## Introduction
## Method
### Data sources

<details><summary>More info</summary>
<p>
    
Information about the housing options in San Francisco are available online. For this study, raw data on home prices (based on assessed property value), hotel rates, house rental rates (long-term), and peer-to-peer home rental rates were obtained from the sites shown in Table 1.

Table 1. Sources of raw data for accommodation costs in San Francisco

|Description|Website Source|Dates covered|Raw data folder|
|---|---|---|---|
|Hotel rates|[SF City Performance Scorecards](https://sfgov.org/scorecards/tourism)|Jul 2004–May 2018|[Hotel Data](https://github.com/rochiecuevas/shared_accommodations/tree/master/Hotel%20Data)|
|Long-term rental rates|[Zillow](https://www.zillow.com/san-francisco-ca/home-values/)|Nov 2010–Sep 2018|[Rent Data](https://github.com/rochiecuevas/shared_accommodations/tree/master/Rent%20Data)|
|Home prices|[Data SF](https://data.sfgov.org/Housing-and-Buildings/Assessor-Historical-Secured-Property-Tax-Rolls/wv5m-vpq2/data)|2007–2016|[Home Prices](https://github.com/rochiecuevas/shared_accommodations/tree/master/Home%20Prices)|
|Peer-to-peer short-term rental rates|[Inside Airbnb](http://insideairbnb.com/san-francisco/?neighbourhood=&filterEntireHomes=false&filterHighlyAvailable=false&filterRecentReviews=false&filterMultiListings=false)|May 2015–Dec 2017|[Airbnb Listings Data](https://github.com/rochiecuevas/shared_accommodations/tree/master/Airbnb%20Listings%20Data%20)|

</p>
</details>

### Data cleaning

<details><summary>More info</summary>
<p>
    
The raw datasets were cleaned using jupyter notebooks found inside each raw data folder. The cleaned data for each accommodation type was then written as a csv file and saved in the [Data](https://github.com/rochiecuevas/shared_accommodations/tree/master/Data) folder.

#### Hotel data
The [dataset](https://github.com/rochiecuevas/shared_accommodations/blob/master/Hotel%20Data/hotel_indicators.csv) contains average daily rates, occupancy rates, and revenue per available room. The [`hotel_rates.ipynb`](https://github.com/rochiecuevas/shared_accommodations/tree/master/Hotel%20Data) jupyter notebook was used to pre-process the data. Pre-processing involved changing the format of the date and the retention of two variables: `Average Daily Rate` and `Hotel Occupancy`. Click [here](https://github.com/rochiecuevas/shared_accommodations/blob/master/Hotel%20Data/README.md) to see a detailed description of how the hotel indicators were pre-processed.

#### Peer-to-peer short-term rental data
The [dataset](https://github.com/rochiecuevas/shared_accommodations/tree/master/Airbnb%20Listings%20Data%20) is organised into 28 csv files. The data was cleaned and the relevant metrics were merged into one csv file using the [`Airbnb_listings.ipynb`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Airbnb%20Listings%20Data%20/Airbnb_listings.ipynb) jupyter notebook. A detailed description can be found [here](https://github.com/rochiecuevas/shared_accommodations/blob/master/Airbnb%20Listings%20Data%20/README.md).

#### Long-term rental data
The [dataset](https://github.com/rochiecuevas/shared_accommodations/blob/master/Rent%20Data/rent_raw.csv) is composed of one csv file that contains monthly rental rates from November 2010 to September 2018. The [`Rent_Analysis.ipynb`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Rent%20Data/Rent_Analysis.ipynb) jupyter notebook is used to clean the data as described [here](https://github.com/rochiecuevas/shared_accommodations/blob/master/Rent%20Data/README.md). 

#### Home prices data
The dataset is not uploaded because it exceeds the file size set by GitHub. It is, however, downloadable as a csv file from [DataDF](https://data.sfgov.org/Housing-and-Buildings/Assessor-Historical-Secured-Property-Tax-Rolls/wv5m-vpq2). Only the columns of interest were included in a dataframe, using the [`DataHome.ipynb`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Home%20Prices/DataHome.ipynb) jupyter notebook. The procedure for cleaning the dataset is detailed [here](https://github.com/rochiecuevas/shared_accommodations/blob/master/Home%20Prices/README.md).

</p>
</details>

### Data processing/analyses

<details><summary>More info</summary>
<p>
    
Pandas and NumPy were used, unless otherwise stated.

#### Hotel data
The processed hotel data was stored in [`hotel_dailyrates.csv`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Data/hotel_dailyrates.csv). To find more insights from the data, it was important to convert the daily rate to monthly rate. Calculations were made more realistic by using hotel occupancy rates as a factor in correcting the monthly rates; without this correction factor, it is assumed that hotels are consistently 100% occupied. The code in `hotel_rate_analysis.ipynb` was used the calculations following these general steps:
1. Classify the months based on number of days;
2. Calculate the monthly rates by multiplying the `average daily rate` with the number of days (__Note:__ For February, further classify the entries to those in regular or in leap years);
3. Correct the monthly rates by multiplying these with the `hotel occupancy` rate.

To determine if there were trends across years, the yearly rates were calcuated by summing up the corrected monthly rates per year. Monthly rates and yearly rates were then saved into different csv files.

#### Peer-to-peer short-term rental data
The output file [`Airbnb_listings.csv`](https://github.com/rochiecuevas/shared_accommodations/tree/master/Airbnb%20Listings%20Data%20) was loaded as a dataframe. An extra data cleaning step was conducted prior to data analysis because the output file was saved with index not set to "False" (an extra column called "Unnamed:0" was added to the dataframe). This column was removed. And the dataframe was renamed.

```python
# Remove the Unnamed:0 column
airbnb_rate = airbnb_df.drop(['Unnamed: 0'], axis=1)

# Rename the dataframe 
data = airbnb_rate
```

The steps followed for data cleaning were:
1. Extract the year substring from the values in the `date` column.
2. For each year of Airbnb data, extract the relevant rows from the daframe, put the rows in a separate dataframe(e.g., " and then group the data based on neighbourhood and get the mean.
3. Rename the "Average annual rate" for clarity.
4. Merge the year dataframes on `neighbourhood`, and rename the columns.
5. Create column entitled `District`.
6. Populate the `District` column by filtering through lists of San Francisco districts.
7. Save the dataframe as a csv file.

#### Long-term rental data
Data from [`rent_raw.csv`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Rent%20Data/Rent_Analysis.ipynb) was loaded as the `rent_df` dataframe. Calculating the yearly rate for each neighbourhood was done as follows:
1. Create a user-defined function (`totals`) that automatically calculates yearly totals per neighbourhood.
2. Create a list of months in two-digit combinations. 
3. Use a list comprehension that uses the `totals` function.
4. Create the dataframe `year_rent_df` which has each year and neighbourhoods as keys.
5. Calculate the mean of Airbnb rental for each year-neighbourhood pair.
6. Add geolocation data extracted from Google Maps Geocoding API.
7. Save the dataframe as a csv file, `yearly_rent.csv`.
.

#### Merged home price and rental data
Two csv files were merged ([`combine_updated.csv`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Data/combine_updated.csv) and [`yearly_rent.csv`](https://github.com/rochiecuevas/shared_accommodations/blob/master/Data/yearly_rent.csv)) in an attempt to look at trends side-by-side for these two housing sectors. The description of the merging is found [here](https://github.com/rochiecuevas/shared_accommodations/blob/master/Merged/README.md).

</p>
</details>

### Data visualisation

<details><summary>More info</summary>
<p>

Matplotlib.pyplot and seaborn modules were used to plot the data into graphs. Graphs were generated for merged [home and long-term rental data](https://github.com/rochiecuevas/shared_accommodations/tree/master/Rent%20and%20Housing%20Visualization), [hotel trends](https://github.com/rochiecuevas/shared_accommodations/blob/master/hotel_rate_visualisation.ipynb), and [peer-to-peer short-term rental](https://github.com/rochiecuevas/shared_accommodations/blob/master/Airbnb%20Analysis/AirbnbRateVisualisation.ipynb).

Documentation of __long-term rental rates__ are found [here](https://github.com/rochiecuevas/shared_accommodations/blob/master/Rent%20and%20Housing%20Visualization/README.md).

On the other hand, __hotel rate__ trends were visualised with bar graphs and with time series line plots. The seaborn plot style was used.

```python
# graphing style
plt.style.use("seaborn")
```

*bar graphs.* The dataframe was sorted by year.

```python
# Sort the dataframe by year
year_df.sort_values("Year", inplace = True)
year_df = year_df.reset_index(drop = True)
```

And incomplete years were taken out of the dataframe.

```python
# Create a subset of complete years (incomplete: 2004 and 2018)
inc = [2004, 2018]

year_subdf = year_df[~year_df["Year"].isin(inc)]
```

The clean data was then plotted into a bar graph.

```python
# Create a bar graph to show trends in hotel yearly rates
plt.bar("Year", "Yearly Rate", data = year_subdf)
plt.xlabel("Year")
plt.ylabel("Annual Hotel Rate (USD)")
plt.ylim(0, max(year_subdf["Yearly Rate"] + 20000))
```

The output image was saved in the [Images](https://github.com/rochiecuevas/shared_accommodations/tree/master/Images) folder.

```python
# Save figure
plt.savefig("Images/hotel_yr_rates.svg")
plt.show()
```

</p>
</details>

## Results
## Conclusions
