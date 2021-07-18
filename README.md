# citadel-summer-datathon-2021

# Overview

What is the project? Why are we working on this? 

A 'sharing economy' is an economic model characterized by the sharing of resources between peers. In the 21st century, this is often facilitated through community-based online platforms.
No company embodies this more than Airbnb.

What started as two college grad's innovative solution to affording rent in an expensive city now poses many socio-economic questions. Who really benefits from the presence of Airbnb? And what second-order impacts does Airbnb have on the wider community?

Our report aims to explore aspects of these questions specifically in the US South region: Los Angeles, Asheville, Austin, Nashville, New Orleans. We aim to assess the presence and impact of hosts that own multiple listings and the effects of Airbnb on rental prices and gentrification.

### Problem Statement

- Non-Technical Executive Summary – What is the question that your team set out to answer? What were your key findings, and what is their significance? You must communicate your insights clearly – summary statistics and visualizations are encouraged if they help explain your thoughts.
- What is the question that your team set out to answer, and how did you choose it? Are your conclusions precise and nuanced, as opposed to blanket (over)generalizations?

# Methodology

What was your methodology/approach towards answering the questions? Describe your data manipulation and exploration process, as well as your analytical and modeling steps. Again, the use of visualizations is highly encouraged when appropriate.

## Wrangling & Cleaning Process

Did you conduct proper quality control and handle common error types? How did you transform the datasets to better use them together? What sorts of feature engineering did you perform? Please describe your process in detail within your Report.

Multiple transformed datasets: description for 1-2 paragraphs

We are given 6 datasets: **listings, calendar, demographics, econ_state, real_estate, venues**.

**listings**

This dataset contains descriptive information about Airbnb listings. A preliminary exploratory data analysis showed that the numbers of bedrooms, bathrooms, beds are positively correlated, the review scores on different criteria are positively correlated. The descriptive information in this dataset makes sense. For a basic data cleaning, we converted the `price` attribute from string to float type. 

**loc**

From the **listings** dataset, we can extract the geographic coordinate (latitude, longitude), the metropolitan, and the city of the property listed. However, the `city` attribute contains alias including abbreviations, different letter cases, or even the Chinese translation. Instead of using the `city` attribute. We extract the neighborhood with the GeoJson files (2.62MB) from [Inside Airbnb](http://insideairbnb.com/get-the-data.html). We treat each listing as a point with (latitude, longitude) coordinates and find out which neighborhood (represented by polygons in the GeoJson) the point resides in. 



or even Chinese 

Also, we extracted the neighborhood each listing belonged to

retrieve the GeoJson

## Investigative Depth

 How did you conduct your exploratory data analysis (EDA) process? What other hypothesis tests and ad-hoc studies did you perform, and how did you interpret the results of these? What patterns did you notice, and how did you use these to make subsequent decisions?

Multi/single listing vs occupancy rate/price

Multi/single listing vs venues counts

Multi/single listing vs home value

## Analytical & Modeling Rigor

 What assumptions and choices did you make, and what was your justification for them? How did you perform feature selection? If you built models, how did you analyze their performance, and what shortcomings do they exhibit? If you constructed visualizations and/or conducted statistical tests, what was the motivation behind the particular ones you built, and what do they tell you?

# Conclusions

Findings, 2nd order impact, 

Reiterate methods (Wrangling & Cleaning Process, Investigative DepthAnalytical & Modeling Rigor.

# Appendices

roadblocks encountered, caveats, future research areas, and unsuccessful analysis pathways
