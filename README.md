# AQI-by-EPA
This is a data analysis study I did on the US Environmental Protection Agency open dataset.

# Data Presentation

| **Column**                              | **Description**                                                                                                                                                                                    |
| --------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Days with AQI**                       | Total number of days in the year for which the Air Quality Index (AQI) was calculated.                                                                                                             |
| **Good Days**                           | Number of days the AQI was in the "Good" range (0–50), meaning air quality was satisfactory with little or no risk.                                                                                |
| **Moderate Days**                       | Number of days the AQI was in the "Moderate" range (51–100), which may pose a minor risk to a very small number of sensitive individuals.                                                          |
| **Unhealthy for Sensitive Groups Days** | Days when AQI was in the 101–150 range, where sensitive individuals (e.g., elderly, children, those with asthma) may experience health effects, but the general public is unlikely to be affected. |
| **Unhealthy Days**                      | Days with AQI in the 151–200 range. Everyone may begin to experience health effects, and members of sensitive groups may experience more serious effects.                                          |
| **Very Unhealthy Days**                 | Days with AQI between 201–300. Health alert: everyone may experience more serious health effects.                                                                                                  |
| **Hazardous Days**                      | Days with AQI above 300. Health warnings of emergency conditions: the entire population is more likely to be affected.                                                                             |
| **Max AQI**                             | The highest (worst) AQI value recorded for the county during the year.                                                                                                                             |
| **90th Percentile AQI**                 | The AQI value below which 90% of the daily AQI values fall. This gives an idea of how bad AQI gets on the worse days, without considering extremes.                                                |
| **Median AQI**                          | The middle AQI value for the year—half of the AQI values were above this, and half were below.                                                                                                     |
| **Days CO**                             | Number of days where carbon monoxide (CO) was the primary pollutant determining the AQI.                                                                                                           |
| **Days NO2**                            | Number of days where nitrogen dioxide (NO₂) was the primary pollutant determining the AQI.                                                                                                         |
| **Days Ozone**                          | Number of days where ground-level ozone was the dominant pollutant affecting the AQI.                                                                                                              |
| **Days PM2.5**                          | Number of days where fine particulate matter (PM2.5) was the primary pollutant.                                                                                                                    |
| **Days PM10**                           | Number of days where coarse particulate matter (PM10) was the primary pollutant.                                                                                                                   |

# Median and Max AQIs throughout the years

Since we're going to be analysing the maximum AQI value measured in the whole year, we can proceed in two ways:

1. Aggregating by Median, which is a metric way less sensitive with outliers.
2. Checking outliers and taking care of them.

We're choosing first option, but in the Jupyter Notebook I also played around option 2. So, here's the plot:
</br>
<div align="center">
  <img src="plots/AQI_trends.png"/>
</div>
</br>
We can see that the Max AQI is dropping fast, but the Median is slightly increasing. Still, it still falls under the 'Good' interval set by EPA.

# Average of Days per AQI Category throughout the years

First thing, there's a problem evidenced in the `Days with AQI` column. Some rows have a low amount of days measured, which can skew the data.

Example:
| State  | County | Year | Days with AQI | Good Days | Moderate Days | Unhealthy for Sensitive Groups Days | Unhealthy Days | Very Unhealthy Days | Hazardous Days |
| ------ | ------ | ---- | ------------- | --------- | ------------- | ----------------------------------- | -------------- | ------------------- | -------------- |
| Montana|	  Park|	 1988|	           36|	     26.0|	          7.0|                                	1.0|	           1.0|	                 0.0|            	1.0|

This data, would make us assume that in 1988, Park, Montana had either 1 Hazardous Day or even ~3% hazardous days in that year, both which can't be assumed. This issue is addressed in the code, when necessary, by weighting the number of Good/Moderate/etc.  Days with their respective Days with AQI.

Example:

| State | County | Year | Days with AQI | Good Days |
| ------ | ------ | ---- | ------------- | --------- |
| Montana|	  Park|	 1988|	           36| 26        |
| Montana| Cascade|	 1988|	          206|   178     |

Calculating:

$$X_{Waqi} = \frac{\sum_{i=1}^{n} x_{Good Days_i}}{\sum_{i=1}^{n} x_{DaysWithAqi_i}} = \frac{26+178}{36+206} = \frac{204}{242} = 0.8429752$$

Would result in one row such as:
| State  | Year | Proportion of Good Days |
| ------ | ------ | ---- |
| Montana|	 1988| 0.84        |

Back to it,

**- 1st Step:** Get the weighted AQI value of each 'State' by getting the sum() of all `County`s by `Year` and dividing by the `sum()` of `Days of measured AQI`.

**- 2nd Step:** Get the average value of the entire country by getting the average of all `State`s by `Year` (Group by `Year`, merging all `State`s and aggregating columns by the average).

**- 3rd Step:** Plot

![AQI_throughout_years Image Plot](plots/AQI_throughout_years.png)

We can observe that the average number of `Hazardous` days has been steadily increasing over the years, likely encroaching on the number of `Very Unhealthy` days, which have been on the decline.

In 1999, the balance between `Good` and `Moderate` days—which had already been fluctuating since around 1988–1989—was noticeably disrupted. According to a few sources, the EPA made some changes to its ranking what it considered a Good Day and a Moderate Day between the years of 1998 and 1999, which is probably the reason this disruption was identified.

Despite this, the proportion of `Good` to `Moderate` days, although very unstably, has since been regaining its original proportions.

The `Very Unhealthy`, `Unhealthy` and `Unhealthy for Sensitive Groups` proportions have strong similarities in their curves.

The highest AQI measured in 1984 throughout the entire country was 300, resulting in no Hazardous Air Quality measurements for that year.

# The States

Let's compare each state's air quality average over the years.
</br>
<div align="center">
  <img src="plots/AQI_by_state.png"/>
</div>
</br>

We can determine California has the worst air quality in average over the years, and Montana the best.

Here are some of the reasons California might be the worst air quality state:
1. Geography: mountain ranges trap pollution in valleys (especially the Central Valley and Los Angeles Basin).
2. Wildfires: frequent and intense wildfires, especially during the dry season.
3. Population: high population density, especially in cities like Los Angeles and San Francisco.
4. Industrial and Agricultural Activity: significant agricultural operations (especially in the Central Valley).

# California
Let’s examine how the median and maximum AQI have evolved over time in the state of California, USA.

</br>
<div align="center">
  <img src="plots/CA_AQI.png"/>
</div>
</br>

California appears to generally follow the national trend.

## Top 5 worst AQI Counties from California
The top 5 worst AQI Counties from California based on the average of the median AQI from 1980 to 2024:
</br>
<div align="center">
  <img src="plots/CA_counties_ranking.png"/>
</div>
</br>

These 5 states combined hold approximately 40% of total California population. Los Angeles alone constitutes 24%, based on January 2025 data.

## Los Angeles

### LA - Median and Max AQIs throughout the years
</br>
<div align="center">
  <img src="plots/LA_AQItrends.png"/>
</div>
</br>

We can see that the median AQI dropped significantly in the 1990s and has continued to decline steadily since then. This suggests that Los Angeles is not a major contributor to the overall decline in air quality observed in California or across the United States.

There's also a very obvious measuring error in the Max AQI plot. Any type of maximum value is very sensitive to outliers, so calculating their medians is always a good practice, whenever possible.

### LA - Average of Days per AQI Category throughout the years

![LA AQI trends](plots/LA_categorydays.png)

### LA's most dominant pollutants over the years
</br>
<div align="center">
  <img src="plots/LA_pollutant.png"/>
</div>
</br>

We can observe that there has been a shift in the pollutant rates around the 00s, highly as a result of increased regulations.

The reduction in the number of days where carbon monoxide (CO) was the dominant pollutant, for example, is a direct result of the success of public policy and technological innovation in reducing vehicle emissions, which are the primary source of CO in urban areas.

The increase in PM2.5 dominancy is probably due to the frequent wildfires that happened in the last two decades, added the fact that the other pollutants decreased in quantity.

# Conclusion
Working on this project was a valuable learning experience that helped me deepen my understanding of data analysis and my my technical proficiency with real-world data.

I had the opportunity to apply the full data science workflow—from cleaning and preprocessing raw data to uncovering meaningful patterns.
