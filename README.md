# Citadel Summer Invitational Datathon 2021 - Team 20
<<<<<<< HEAD
[Codes](https://github.com/cpwan/citadel-summer-datathon-2021/tree/eda/codes)
=======
[Report (HTMl version)](https://cpwan.github.io/citadel-summer-datathon-2021/)

[Codes](https://github.com/cpwan/citadel-summer-datathon-2021/tree/eda)
>>>>>>> origin/main


# 1. Executive Summary

A 'sharing economy' is an economic model characterized by the sharing of resources between peers. In the 21st century, this is often facilitated through community-based online platforms. No company embodies this more than Airbnb.

Airbnb, Inc. is an American company that operates an online marketplace for lodging, primarily homestays for vacation rentals, and tourism activities. Airbnb does not own any of the listed properties; instead, it profits by receiving commission from each booking [1].

What started as two college grad's innovative solution to affording rent in an expensive city now poses many socio-economic questions. Who really benefits from the presence of Airbnb? And what second-order impacts does Airbnb have on the wider community?

The company has been criticized for possibly driving up home rental prices and creating nuisances for those living near leased properties [1, 2]. Our report aims to explore aspects of these questions specifically in the US South region: Los Angeles, Asheville, Austin, Nashville, New Orleans. We aim to assess the presence and impact of hosts that own multiple listings and the effects of Airbnb on rental prices and gentrification.

In this report, we refer *listing*  as a property offered in the Airbnb listings; *Multiple Listing owners* as the hosts who own multple listings in Airbnb; *Muliple Listing* as a property owned by a Multiple Listing owner. We refer *Single Listing owner* and *Single Listing* in a similar fashion.

## 1.1 Problem Statement

Through the medium of this report, we aim to answer the following questions:
- Is there a difference in listings owned by individuals vs properties owned by multiple listing owners?
- Is the presence of Airbnb listings in a neighbourhood driving up rental prices?

## 1.2 Summary of findings
- Multiple Listing tends to charge higher, though there are exceptions in Austin and Los Angeles.
- Multiple Listing tends to have poorer review scores than that of Single Listing.
- Multiple Listing tends to be resided in a region with higher business activity.

---

# 2. Methodology

## 2.1 Data Wrangling & Cleaning Process

We are given 6 datasets: **listings, calendar, demographics, econ_state, real_estate, venues**. Started from these datasets, we transformed the data and obtained a couple of new datasets for our analysis. They are summarized in the following section.

<!-- ### **listings.csv**

This dataset contains descriptive information about Airbnb listings. A preliminary exploratory data analysis showed that the numbers of bedrooms, bathrooms, beds are positively correlated, the review scores on different criteria are positively correlated. The descriptive information in this dataset makes sense. For a basic data cleaning, we converted the `price` attribute from string to float type. 
  -->
### 2.1.1 **loc.csv**
From the **listings** dataset, we can extract the geographic coordinate (latitude, longitude), the metropolitan, and the city of the property listed. However, the `city` attribute contains alias including abbreviations, different letter cases, or even the Chinese translation. Instead of using the `city` attribute. We extract the neighborhood with the GeoJson files (2.62MB) from [Inside Airbnb](http://insideairbnb.com/get-the-data.html). We treat each listing as a point with ``(latitude, longitude)`` coordinates and find out which neighborhood (represented by polygons in the GeoJson) the point resides in. 
<details>
  <summary>Click to view a snapshot of the dataset</summary>
  
|  id  |  latitude |  longitude  | neighbourhood | metropolitan |
|:----:|:---------:|:-----------:|:-------------:|:------------:|
|  109 | 33.982095 | -118.384935 |  Culver City  |  los-angeles |
|  344 | 34.165616 | -118.334582 |    Burbank    |  los-angeles |
|  941 | 34.071556 | -118.350786 |    Fairfax    |  los-angeles |
| 1078 | 30.301231 |  -97.736736 |     78705     |    austin    |
| 2265 | 30.277500 |  -97.713975 |     78702     |    austin    |
  
</details>

### 2.1.2 **calendar_monthly.csv**
We extracted monthly data from the **calendar** dataset. For each `listing_id`, we compute the average `avaliable` and average `price` in each month in the **calendar** dataset. We noticed that some Airbnb listings have significant different prices in the **calendar** dataset and the **listing** dataset. In particular, the listing *969135* has a price of $256089  in **calendar** dataset but a price of $70 in **listing** dataset. We locate the property on Google Map and compare it with the neighboring hotels. It indicated that it was likely the fault of the **calendar** dataset. We marked the listings with unusal prices to be outliers.

<details>
  <summary>Click to view a snapshot of the dataset</summary>
  
| listing_id |   date  |   available  |    price    |
|:----------:|:-------:|:------------:|:-----------:|
| 344        | 2018-04 | 0            |             |
| 344        | 2018-05 | 0            |             |
| 941        | 2017-05 | 0.3          | 94          |
| 941        | 2017-06 | 1            | 117.8       |
| 941        | 2017-07 | 1            | 118.4193548 |

</details>

### 2.1.3 **calendar_geo.csv**
From the given **calendar.csv** and the **loc.csv** we created, we extracted the `number of listings`, average `occupancy rate`, and average `price` in each neighborhood in each month. The `occupancy rate` is defined as the fraction of unavailable days of the listing in a month.

<details>
  <summary>Click to view a snapshot of the dataset</summary>
  
  | metropolitan | neighbourhood |   date  | number of listings | occupancy rate | mean price  |
|:------------:|:-------------:|:-------:|:------------------:|----------------|-------------|
| asheville    | 28704         | 2016-04 | 30                 | 0.4889057239   | 89.80571429 |
| asheville    | 28704         | 2016-05 | 30                 | 0.6720430108   | 99.70769354 |
| asheville    | 28704         | 2016-06 | 30                 | 0.7177777778   | 96.51108421 |
| asheville    | 28704         | 2016-07 | 30                 | 0.6935483871   | 95.09254785 |
| asheville    | 28704         | 2016-08 | 30                 | 0.7537634409   | 104.3698989 |
</details>


### 2.1.4 **nbhd-r.csv**
We are interested in the counts of venues around the listings. For each listing, we find the number of venues in the $r$-km radius circle around the listing, where 
$$r\in \{0.125,0.25,0.5,1,2,4\}.$$ We find also the counts of Airbnb listings around each Airbnb property in the same scales of cicle. We noticed that the **venues** dataset does not cover the whole region where the Airbnb listings reside in. Hence, we marked the listings that has no venues nearby to be possible missing data. In our analysis, we focus on the listings that have at least 1 venue in the 4-km radius circles. 

<details>
  <summary>Click to view a snapshot of the dataset</summary>
  
| listing_id | accounting | airport | ... | restaurant | spa | storage | store | transit_station | ... | num_venues |
|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| 109 | 0 | 1 | ... | 48 | 3 | 3 | 255 | 42 | ... | 1538 |
| 344 | 0 | 0 | ... | 20 | 10 | 0 | 12 | 24 | ... | 343 |
| 941 | 0 | 0 | ... | 96 | 10 | 0 | 38 | 59 | ... | 1109 |
| 2404 | 0 | 0 | ... | 72 | 23 | 3 | 221 | 47 | ... | 1640 |
| 2732 | 0 | 0 | ... | 82 | 17 | 0 | 261 | 58 | ... | 1984 |
</details>

### 2.1.5 **Airbnb_by_zipcodes.csv**
from the given **listings** and **calendar** datasets, we find the `Median Airbnb price` for a listing (per guest, so as to ensure that a larger `accommodates` factor does not drive prices up) in a given zipcode along with the number of listings in that neighbourhood by executing groupby zipcodes in pandas. We are also able to extract whether the Median owner in that zipcode is one with multiple listings or just a single listing; acting as a proxy for whether Airbnb acts as a commercialised business in that zipcode or not (1 if Multiple owner, 0 otherwise). We utlise this dataset later by regressing it against the ZRI and ZHVI Home Value indices to find if our covariates influence the home value in their respective zipcodes.

<details>
  <summary>Click to view a snapshot of the dataset</summary>
  
  |    Zipcode | Airbnb Price | Volume By Zipcode | Multiple Owner |
  | --- |:------------:|:-----------------:|:--------------:|
  |  78644   |  50.0   |       1       |    0     |
  | 78652    |  13.5   |       1       |    0     |
  |   78702  |  50.5   |       1202       |    1    |
  |  78721   |  63.3   |       642       |    0     |
  |  78723   |  37.5   |       191       |    1     |
</details>

<br>


---

# 3. Analysis of Multiple Listings
 
Using the `host id` of the **listings** dataset, we were able to gather data on how many listings were under each id. To illustrate the extent of multiple listings look at the bar graph on the right of the top ten ids with the most listings and where they are situated.

Id's with largest number of listings             |  % of listings relative to number of host's listings
:-------------------------:|:-------------------------:
![](https://i.imgur.com/SdbohpR.png)  |  ![](https://i.imgur.com/WdEuS3O.png)


As a proportion of all listings, 44.8% are owned by a host with two or more listings. Given the large presence of multiple listings we analyzed differences between single and multiple listings in three key areas: the price of rental, the occupancy rate and location where a listing is based. More detail on proportions by the 5 metropolitans is given on the right.

### 3.1 Effects of Multiple Listings on Price
To investigate whether Multiple Listing owners charge a higher price on average compared to a Single Listing owner, we conducted an independent t-test assuming equal variances. The Null Hypothesis was that both categories of owners have the same mean price for their listing. The Alternative Hypothesis was that Multiple Listing owners have a greater mean price per listing compared with Single Listing owners. Taking a significance level of 5%, we achieved a p-value of 0.0000537 which is significantly below our significance level. As such, we reject the Null and can state that Multiple Listing owners do in fact charge a higher price per night for their listings on average when compared to single listing owners. The violin plot below further illustrates this pricing difference.

![](https://i.imgur.com/lrA93O9.png)


However, we must remember that the above is based on raw price per night and does not account for the capacity of a listing (how many people a listing can accommodate). Therefore, high prices of multiple capacity listings could skew results. To futher analyse the difference of pricing we looked at the price per night per person for each listing and separated by metropolitan. The figure below on the left gives the percentiles of listing price/ listing capacity. It is clear that there are some outliers at either end of the data that need to be withdrawn. The ZRI and ZHVI indexes given in the real_estate dataset represent prices of houses and rentals in the middle quantile of listings. As these indexes will be used as a proxy for rental prices later in our analysis it is reasonable to also analyze the middle quantile.

Percentiles for listing one night price/ listing capacity             |  Violin plots of price/capacity compared to metropolitan and host listings
:-------------------------:|:-------------------------:
![](https://i.imgur.com/GcGhwiI.png)  |  ![](https://i.imgur.com/oCHUosc.png)

We ran multiple t-tests conditioned on price/capacity, number of host listings and metropolitan.
The violin plots above to the right illustrate the most interesting of our observations. On further inspection of the dataset we noticed that a number of hosts with 2 listings where possibly listing rooms of same property separately. To avoid this noise and focus on the hosts that own multiple properties we conditioned on the host having 3-10 listings. The majority of our tests returned that there was no difference in price/capacity for single listings compared to multiple listings though there were exceptions for Austin and LA. Carrying out Welch t-tests we found p-values in the 2.9e-19 and 1.9e-05 ranges for Austin and LA respectively. This is to be interpretted that there is statistical evidence that hosts that have 3-10 listings offer cheaper rental prices per person than hosts with a single listings in Austin and LA. This is interesting as it bucks the broader trend that hosts with multiple properties tend to charge higher prices. 
 

### 3.2 Effects of Multiple Listings on Review/Ratings
Again, to investigate whether Multiple Listing owners have a higher rating when compared to Single Listing owners, we conduct a one sided hypothesis test stating that Multiple Listing owners have a greater mean rating when compared to Single Listing owners. The Null Hypothesis states that both groups have the same mean rating but the Alternative states that Multiple Listing owners have a higher mean rating when compared to Single Listing owners. We fail to reject the Null Hypothesis at a significance level of 5%, as such, we try to conduct another test this time the Alternative being that Single Listing owners have a higher mean rating when compared to Multiple Listing owners. In the second test, we achieve a test statistic of 12.46 and a p-value much below our significance level of 5%. As such we observe that even if Multiple Listing owners charge a higher mean price per night, their mean `review_score_rating` is actually lower when compared to Single Listing owners. 

### 3.3 Multiple listings comparison with occupancy

Using the `occupancy rate` from the wrangled **calendar_geo** dataset we are able to plot histograms of the relationship between `occupancy rate` and whether a listing is a multiple listing. As a reminder the `occupancy rate` is the average proportion of a calendar month that a listing is unavailable for. So rates closer to 1 indicate a listing is unavailable and rates closer to 0 indicate availability. The histograms below illustrate occupancy rates for our categories of total number of listings owned by a host and have `occupancy rate` on the x axis. Single listings and listings where a host has 2-5 listings seem to exhibit a bimodal distribution. 

![](https://i.imgur.com/p0ELfWD.png)


### 3.4 Effect of Multiple Listing on locational patterns

| Location of Airbnb listings in Los Angeles|
|:------------:|
![](https://i.imgur.com/O83he2Y.gif)

The above figure shows the locations of Los Angeles listings based on whether they are a multiple listing. The darkness of a point is based on its price/capacity. This figure motivates our investigation into the relationship between the spacial position of multiple listings and venues of interest surrounding them.

We analyze the locational patterns of the Single Listing owners and the Multiple Listing owners. We extract the counts of different types of venues in the $r$-km radius circle of the listings from Single Listing owners and Multiple Listing owners respectively. 

**Density of venues**
We are interested in the density of the venues against the distance to the listing. 
<details>
  <summary>Click to show the definition of density at r-km to the listing. </summary>
  
> The denisty of venue type $v$ in the smallest $0.125$-km radius circle is just:
$$
\mathrm{Density_{v}}(0,r)=\mathrm{Count_{v}}(r)/\pi r^2,\quad r=0.125
$$
However, for the counts in the $0.25$-km radius circle, we cannot use the same formula. Otherwise, we will include counts in the smaller radius circle and make the two densities dependent. Hence, we find instead the density of a torus formed by subtracting the smaller $r_1$-km radius circle from the larger $r_2$-km radius circle. The density of the torus ($r_1,r_2$) is calculated as:
$$
\mathrm{Density_{v}}(r_1,r_2)=(\mathrm{Count_v}(r_2)-\mathrm{Count_v}(r_1))/(\pi r_2^2-\pi r_1^2).
$$
</details>

<br>

The following boxplots show the density of venues at different distance away from the listing. Qualitatively, properties from Multiple Listing owners tend to be resided in a region that has more venues, or in order words, higher business activity. In the neighborhood of properties from Multiple Listing owner, there are more venues, including points of interest, stores, restaurants, and transit stations. In indicates that, the Multple Listing owners tend to hold their properties in business districts, where there are more stores, restaurants, and trans.


Density of venues across radius circle  <br> Single Listing vs Multple Listing         |  Density of venues across radius circle  <br> Bucketed comparison
:-------------------------:|:-------------------------:
![](https://i.imgur.com/ACM2IG8.png)  | ![](https://i.imgur.com/QnmNIQt.png)
![](https://i.imgur.com/MH5k4ie.png) | ![](https://i.imgur.com/T7jyBK3.png)
![](https://i.imgur.com/knajzZ2.png) |![](https://i.imgur.com/mfNPZiC.png)
![](https://i.imgur.com/G6rWrRj.png)| ![](https://i.imgur.com/SXE6PIH.png)
![](https://i.imgur.com/3v25AIT.png)|![](https://i.imgur.com/ZTIvlsZ.png)

**Counts of venues**
Apart from density, the counts of venues around the neighborhood also varies with Single Listing/ Multiple Listing.
| Counts of venues across different distances to the listings|
|:------------:|
|![](https://i.imgur.com/OT2bCJW.png)|

We conducted [Mann-Whitney U rank test](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.mannwhitneyu.html) on the two population of the counts of venues with respect to the Single Listing owners and Multiple Listing owners. The test is chosen and our data on the counts of venues satisfied its assumption:

1. The observations for Single Listing and Muliple Listing are independent to each other.
2. The count is ordinal.
3. $H_0$: At $r$-km radius circle, the population of counts for property owned by Multple Listing owner is **the same as** that for property owned by Single Listing owner.
4. $H_1$: At $r$-km radius circle, the population of counts for property owned by Multple Listing owner is **stochastically greater than** that for property owned by Single Listing owner.

The result is summarized the table:

|     $r$   | p-value |
|:--------:|:-------:|
| 0.125  |   <0.001   |
|  0.25  |  <0.001   |
|  0.5  |   <0.001   |
|   1   |   <0.001   |
|   2   |   <0.001   |
|   4   |   <0.001   |

From the table, it showed that $H_0$ is rejected in all values of $r$. It means that properties from a Mulptle Listing owner indeed have a higher chance to be resided in a region with more venues, in order words, higher business activity.

**Region-wise effect**
We also investigate if the effect of Multple Listing exists in all the five regions in US. South. The plot showed qualitatively that properties from Mulptle Listing owner tends to be resided in a region with a higher business activity. 
|Number of venues in 0.25-km radius circle of the listing |Number of venues in 1-km radius circle of the listing 
|:------------:|:------------:|
|![](https://i.imgur.com/fPHyMLC.png)|![](https://i.imgur.com/PK3jeHU.png)|

<br>

Similarly, we conducted Mann-Whitney U rank test between the counts of venues for Multiple Listing/ Single Listing in the five regions in US. South. The table summarized the test result. 

|      | los-angeles | austin | nashville | new-orleans | asheville |
|---------:|-------------|--------|-----------|-------------|-----------|
| 0.125-km |    <0.001    |  0.001 |    <0.001    |     <0.001     |   <0.001   |
|  0.25-km |    <0.001    |  0.008 |    <0.001    |     <0.001     |   <0.001  |
|  0.5-km  |    <0.001    |  0.007 |   <0.001    |     <0.001     |   0.042   |
|   1-km   |    <0.001   | <0.001 |   <0.001    |     <0.001     |   0.462   |
|   2-km   |    <0.001    |  <0.001 |   <0.001    |     <0.001     |   0.774   |
|   4-km   |    0.012    | <0.001 |   <0.001    |     <0.001    |   0.532   |


The null hypothesis was rejected for most of the cases .It showed that properties from Multple Listing owners have a higher chance to be resided in a region with higher businsess activity. For the listings in Asheville, the p-value is large when we consider the counts the in $1$-km, $2$-km, $4$-km radius circle of the listing. It makes sense since Asheville covers a relatively small area. As we increase the radius, it eventually covers all venues in Asheville.

---

# 4. Regression Analysis

Gentrification is the process by which a economically poverished area is transformed into a wealthier neighbourhood due to rapid growth and investment while simultaneously displacing current residents. 

We theorise that the rise of Airbnb listings in a neighbourhood along with the rising commericialisation of Airbnb through the owning of multiple properties by a single owner gives way to gentrification. The introduction of Airbnb listings in a geographical area introduces new revenue flows while no new capital infrastructrure needs to be set up. This increases the disparity between the actual land value of the neighbourhood when compared to the potential land value, which drives up rental prices. The lure of earning additional revenue with the help of minimal capital makes developers and landlord rent out their properties as short-term rentals instead of long term rentals, which one may argue leads to a loss of communtiy as well as belongingness.

To test our hypothesis that higher Airbnb prices along with a larger number of listings leads to higher land value and thus rentals, we conduct an OLS regression.

We assume that our Home value indices act as proxies for the rental prices in the respective zipcodes.

We first plot a heatmap of the dataframe so we can affirm that there does indeed exist a positive relationship between Airbnb covariates and our Home Value indices. We see that the Median Airbnb price per guest per night has a significant correlation with our Home Value indices.

 ![](https://i.imgur.com/xQnzRQM.png)


We further proceed to conduct two seperate OLS regression on both Home Value indices seperately. We take the natural logarithm of both indices for easier interpretability in terms of percentage. 

The equations for our model become:

$$
\mathrm{ZRI_{zipcode}}= \beta_{0} + \beta_{1}AirbnbPrice_{zipcode} + \beta_{2}VolumeByZipcode_{zipcode} + \beta_{3}MultipleOwner_{zipcode} + \epsilon
$$

and 
$$
\mathrm{ZHVI_{zipcode}}= \beta_{0} + \beta_{1}AirbnbPrice_{zipcode} + \beta_{2}VolumeByZipcode_{zipcode} + \beta_{3}MultipleOwner_{zipcode} + \epsilon
$$

|Regression Report for ZRI Index |  Regression Report for ZHVI Index|
:-------------------------:|:-------------------------:
|![](https://i.imgur.com/Jj4Nede.png =355x) | ![](https://i.imgur.com/kpYhIg7.png =355x)|

<br>

After conducting the regression, we conduct a test for endogenity using a Hausman specification test and White's test to test for the presence of Heteroskedasticity with a significance level of 5%. Both the test results indicate that the Gauss Markov assumptions are followed.

Looking at the regression reports, we conclude that the Median Airbnb price in the neighbourhood is a statistically significant variable and when the median price per guest per night increases by $1, on average, both our Home value indices should increase by around .5%, ceteris paribus.

The volume of Airbnb listing seems to be a a marginally statistically insignificant variable. The final categorical covariate that accounted for the median listing owner type in that zipcode which acts as a proxy for commercialisation of Airbnb in that zipcode turns out to be a statistically insignificant variable in explaining the Home Value in the corresponding zipcode. However, further research can be conducted with regards to lagged values of the same categorical variable so as to account for the time lag caused by construction among other procedures.

---
# 5. Conclusion

Through our report, we aimed to assess the relationship between rental prices and Airbnb listings as well analysing the effect of multiple owner properties in a given city/metropolitan area. Our findings indicate that multiple owner listed properties were, on average, more expensive as compared to individual single listed properties. Multiple listing properties have a higher concentration of venues around them as compared to single listing properties, including points of interests, stores, restaurants, and transit stations. This might indicate the rising commercialisation of the Airbnb market.

On the other front, we see that a higher volume of listings along with a higher median price in a zipcode, on average, might indicate higher home values, and therefore rental prices. The gradual increase of which might lead to the gentrification of the neighbourhood, while at the same time, threatening senses of community and neighbourhoods by leasing out short-term rentals instead of long-term ones. Whilst our report does not serve as a concrete proof of Airbnb causing gentrification, our report evidences certain indicators indicate the same.


---
# References

[1]	Wikipedia contributors, “Airbnb,” Wikipedia, The Free Encyclopedia, 17-Jul-2021. [Online]. Available: https://en.wikipedia.org/w/index.php?title=Airbnb&oldid=1034005263. [Accessed: 18-Jul-2021].

[2]	D. Wachsmuth and A. Weisler, “Airbnb and the rent gap: Gentrification through the sharing economy,” Environ. Plan. A, vol. 50, no. 6, pp. 1147–1170, 2018.

[3]	D. Guttentag, “Airbnb: disruptive innovation and the rise of an informal tourism accommodation sector,” Curr. Issues Tourism, vol. 18, no. 12, pp. 1192–1217, 2015.

[4]	J. Jiao and S. Bai, “An empirical analysis of Airbnb listings in forty American cities,” Cities, vol. 99, no. 102618, p. 102618, 2020.

[5]	G. Zervas, D. Proserpio, and J. W. Byers, “The rise of the sharing economy: Estimating the impact of Airbnb on the hotel industry,” J. Mark. Res., vol. 54, no. 5, pp. 687–705, 2017.

[6]	Y. Yang and Z. (eddie) Mao, “Welcome to my home! An empirical analysis of Airbnb supply in US cities,” J. Travel Res., vol. 58, no. 8, pp. 1274–1287, 2019.


