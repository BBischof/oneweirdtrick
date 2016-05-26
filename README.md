# "One Weird Trick" to Recover Suppressed Counts from CDC's **WONDER**

## Introduction
The Centers for Disease Control and Prevention's Wide-ranging Online Data for Epidemiological Research (**WONDER**) data retrieval system provides access to many types of public health information. Mortality and fertility counts for multiple years across standard geographical subdivisions--broken down by race, gender, 5-year age groups, etc.--are just some of the data sets available through this system. Though an immense assortment of data sets are available through **WONDER**, counts returned whose values fall between 0 and 9 are replaced with the word "Suppressed," generating missing values, and hindering research agendas. This article presents a new method to recover some of these suppressed counts.

### *Should this be done?*

In short, yes. While the CDC has reasons for supressing these data points beyond our desire to speculate; the kind of simple derivation found below is well within the acceptable limits of deriving obfuscated information. Because the *missing* data is contained in other queries of the CDC data set, we are merely cross-referencing queries. 

## Method
It's really quite simple to recover many counts suppressed by CDC's **WONDER**, it just takes, as the spam advertisements claim, this "one weird trick." And, as expected, the "trick" involves only simple algebra. 

To recover a set of desired data from CDC's **WONDER**, we subtract the difference between the entire set of data and the set of desired data from the entire set of data. Equations 1.a and 1.c in the following tables present this formulation informally and formally.

| Equation 1.a                                                                       |
|:-----------------------------------------------------------------------------------|
| `what you want = a lot of what they'll give - (a lot of what they'll give - what you want)`|

Consider a query `f(T,c)` where `T` are the years considered, and `c` ranges over each county. `f` takes values in the natural numbers. Now, observe that for year `x`, we have `f(x,c)` a vector a values with a set of unknowns `s_i` for `c_i` the corresponding set of counties. Then:

| Equation 1.b                                                                         |
|:-------------------------------------------------------------------------------------|
|`s_j = f(x,c_j)=f(T,c_j)-f(T-x,c_j) for c_j those counties where f(T-x,c_j) is known.`| 

As an example, let's walk through a query of infant mortality rates for all US counties in 2013 using the default method. 
* Go to CDC's **WONDER** home page and click the link [Multiple cause of death (Detailed Mortality)](http://**wonder**.cdc.gov/mcd.html). 
* Click the [Data Request](http://**wonder**.cdc.gov/mcd-icd10.html) link from the Current Multiple Cause of Death Data section. 
* Scroll down and click the "I Agree" button to agree to the terms and conditions for accessing these data. 

To make this data request, most of the default settings are fine, but let's select the additional following options: 
* From the **Organize** table layout section: select "County" from the And By menu
* From the **Select** demographics section: click the radial button next to Single-Year Ages
* From the **Select** demographics section: select "< 1 year" from the Pick between list
* From the **Select** year and month section: select "+ 2013" from the Year/Month list
* From the **Other** options section: check the box next to Show Zero Values
* From the **Other** options section: check the box next to Show Suppressed Values
* From the **Other** options section: select "4" from the Precision menu

Click the Send button and CDC's **WONDER** will return infant mortality rates for some US counties in 2013, but not all of them. This query is for the `what we want` part of Equation 1.a, and for those returned, it does provide us with the desired data. However, most of the counts returned, as you'll notice, are suppressed (2192 of them). Taking counties as our unit of analysis, this query gives us a response rate of approximately 14.86%. Figure 1 shows an example map of this data.

| Figure 1: Infant Mortality Rates among US Counties in 2013 (n=3,142) |
|:---------------------------------------------------------------------|
|![alt text](https://cloud.githubusercontent.com/assets/4267812/13888668/967684d4-ed10-11e5-9cf4-a0c420f658f7.png "Figure 1")|

In our effort to recover counts, we repeat the same steps taken to construct Figure 1, but this time we select all available years: `"+ 1999", "+ 2000", ..., "+ 2013"`. After selecting and submitting the specified years, CDC's **WONDER** will return infant mortality rates for all US counties, where available, between 1999 to 2013. As we have seen, data requests from CDC's **WONDER** can result in suppressed counts, and this is true even when counts are requested for a number of years. However, by requesting counts over multiple years, **WONDER** is less likely to suppress the returned counts (due to the counts being larger). This action gives us the `a lot of what they'll give` part of Equation 1.a. Figure 2 shows infant mortality rates for all US counties over the years 1999 to 2013. The response rate among these counties is much higher than it was in the query for year 2013 alone; representing about 81.51% of counties.

| Figure 2: Infant Mortality Rates among US Counties years 1999 to 2013 (n=3,142) |
|:--------------------------------------------------------------------------------|
|![alt text](https://cloud.githubusercontent.com/assets/4267812/13888667/967436de-ed10-11e5-92c3-efcd1ea4d828.png "Figure 2")|

Recall from Equation 1.a, the "one weird trick" involves subtracting the `(a lot of what they'll give - what you want)` part from the `a lot of what they'll give` part, the second term which we got in the construction of Figure 2. To get the `(a lot of what they'll give - what you want)` piece of the equation, we accept the default settings in **WONDER** with the following modifications:
* From the **Organize** table layout section, select "County" from the And By menu
* From the **Select** demographics section, click the radial button next to Single-Year Ages
* From the **Select** demographics section, select "< 1 year" from the Pick between list
* From the **Select** year and month section, select "+ 1999", "+ 2000", ..., "+ 2012" from the Year/Month list
* From the **Other** options section, check the box next to Show Zero Values
* From the **Other** options section, check the box next to Show Suppressed Values
* From the **Other** options section, select "4" from the Precision menu

By clicking the Send button, CDC's **WONDER** will return infant mortality rates for all US counties from 1999 to 2012. This action gives us the `(a lot of what they'll give - what you want)` part of Equation 1.a. Figure 3 shows infant mortality rates for all US counties over the years 1999 to 2012. The response rate among these counties is higher than that observed for the single example year (i.e., 2013) but lower than that for all the available years (i.e., 1999, 2000, ..., 2013), representing approximately 80.43% of US counties.

| Figure 3: Infant Mortality Rates among US Counties years 1999 to 2012 (n=3,142) |
|:--------------------------------------------------------------------------------|
|![alt text](https://cloud.githubusercontent.com/assets/4267812/13888669/9677549a-ed10-11e5-8857-f06730e88aa4.png "Figure 3")|

To recover suppressed infant mortality counts among US counties in 2013, we take the difference of the counts used to construct Figure 3 from those used to construct Figure 2 to construct Figure 4. This method greatly improves upon the naive approach and yields a higher response rate (80.43% as compared to 14.86%). 

| Figure 4: Recovered Infant Mortality Rates among US Counties 2013 (n=3,142) |
|:--------------------------------------------------------------------------------|
|![alt text](https://cloud.githubusercontent.com/assets/4267812/13888670/9677d456-ed10-11e5-9d4a-fa1b2873e93a.png "Figure 4")|
