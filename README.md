
# Project: Exploring World Human Development Index - HDI  (Gapminder data)
by [Bernardo Romero](mailto:romero.bernardo@gmail.com)
August 2018

<img src="https://www.utoronto.ca/sites/default/files/2017-07-05-faces.jpg" style="width: 600px;"/>

<a id='contents'></a>
## Table of Contents
<ul>
<li><a href="#intro">Introduction</a></li>
<li><a href="#wrangling">Data Wrangling</a></li>
<li><a href="#eda">Exploratory Data Analysis</a></li>
<li><a href="#conclusions">Conclusions</a></li>
<li><a href="#sources">Sources</a></li>
</ul>


<a id='intro'></a>
## Introduction

The Human Development Index (HDI) is a widely-used indicator for the welfare of a society. It is produced by the United Nations Development Program (UNDP) on a yearly basis since 1990 and is an alternative to the traditional economic growth approach (GDP) to development. The index summarizes the current situation of a country's achievements in education (years of schooling), health (live expectancy) and overall economic situation (gross national income per capita). By exploring the change in the HDI we could have an idea whether the world's population is in an overall better or worse situation as it was 15 years ago. 

HDI was initially proposed by the Pakistani economist Mahbub ul Haq and UNDP has taken ownership of the indicator's production and the publication of its results. The official site for the HDI can be accesed <a href="http://hdr.undp.org/en/faq-page/human-development-index-hdi">here</a> and for this project the data was retrieved from <a href="https://www.gapminder.org/data/">Gapminder</a>, a repository of economic and societal data at the country level.

A [criticism](http://hdr.undp.org/sites/default/files/hdrp_2010_35.pdf) to the HDI is that it does not take into account a country's economic inequality and that it does not relflect a country's economic insertion to the world. Furthermore, it doesn't take into account ecological footprint, which unfortunaltely seems to be positively related to a country's higher HDI. To test for these shortcomings, this project looks for relationships between the HDI and further Gapminder data about foreign direct investment (FDI) flows, CO2 emissions and inequality, as represented by a country's <a href="https://en.wikipedia.org/wiki/Gini_coefficient">GINI coefficient</a>. The data was retrieved from the Gapminder repositories in August 2018.

By using Exploratory Data Analysis with the pandas, numpy and matplotlib python libraries, this project will try to respond to the following questions:

1. According to the HDI, are people in more countries living in better conditions in 2015 than in 2000? in which proportion?
2. Is the average Human Development improving? by how much?
3. Which country and region improved the most between 2000 and 2015? Which one improved the least?
4. What features are associated with higher HDI (FDI, CO2 emissions, inequality)?



```python
# pandas, builtover the numpy library, provides the necessary tools for exploratory data analysis with python, 
# whereas matplotlib provides the plotting and charting capabilities to analyse and communicate the results

import pandas as pd
import matplotlib.pyplot as plt
%matplotlib inline
```

<a id='wrangling'></a>
## Data Wrangling


### General Properties
We start by importing four datafiles with the HDI index, GINI, FDI and CO2 emission data. Additionally, we import a general reference geography file for segmentation purposes. 


```python
# import the file with the HDI data and check its structure

hdi_df = pd.read_csv('hdi_human_development_index.csv')
hdi_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>geo</th>
      <th>1990</th>
      <th>1991</th>
      <th>1992</th>
      <th>1993</th>
      <th>1994</th>
      <th>1995</th>
      <th>1996</th>
      <th>1997</th>
      <th>1998</th>
      <th>...</th>
      <th>2006</th>
      <th>2007</th>
      <th>2008</th>
      <th>2009</th>
      <th>2010</th>
      <th>2011</th>
      <th>2012</th>
      <th>2013</th>
      <th>2014</th>
      <th>2015</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Afghanistan</td>
      <td>0.295</td>
      <td>0.300</td>
      <td>0.309</td>
      <td>0.305</td>
      <td>0.300</td>
      <td>0.324</td>
      <td>0.328</td>
      <td>0.332</td>
      <td>0.335</td>
      <td>...</td>
      <td>0.415</td>
      <td>0.433</td>
      <td>0.434</td>
      <td>0.448</td>
      <td>0.454</td>
      <td>0.463</td>
      <td>0.470</td>
      <td>0.476</td>
      <td>0.479</td>
      <td>0.479</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Albania</td>
      <td>0.635</td>
      <td>0.618</td>
      <td>0.603</td>
      <td>0.608</td>
      <td>0.616</td>
      <td>0.628</td>
      <td>0.637</td>
      <td>0.636</td>
      <td>0.646</td>
      <td>...</td>
      <td>0.703</td>
      <td>0.713</td>
      <td>0.721</td>
      <td>0.725</td>
      <td>0.738</td>
      <td>0.752</td>
      <td>0.759</td>
      <td>0.761</td>
      <td>0.762</td>
      <td>0.764</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Algeria</td>
      <td>0.577</td>
      <td>0.581</td>
      <td>0.587</td>
      <td>0.591</td>
      <td>0.595</td>
      <td>0.600</td>
      <td>0.609</td>
      <td>0.617</td>
      <td>0.627</td>
      <td>...</td>
      <td>0.690</td>
      <td>0.697</td>
      <td>0.705</td>
      <td>0.714</td>
      <td>0.724</td>
      <td>0.732</td>
      <td>0.737</td>
      <td>0.741</td>
      <td>0.743</td>
      <td>0.745</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Andorra</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.819</td>
      <td>0.819</td>
      <td>0.843</td>
      <td>0.850</td>
      <td>0.857</td>
      <td>0.858</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Angola</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>0.454</td>
      <td>0.468</td>
      <td>0.480</td>
      <td>0.488</td>
      <td>0.495</td>
      <td>0.508</td>
      <td>0.523</td>
      <td>0.527</td>
      <td>0.531</td>
      <td>0.533</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 27 columns</p>
</div>




```python
# import the data with GINI index by country and check its structure
gini_df = pd.read_csv('inequality_index_gini.csv')
gini_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>geo</th>
      <th>1979</th>
      <th>1980</th>
      <th>1981</th>
      <th>1982</th>
      <th>1983</th>
      <th>1984</th>
      <th>1985</th>
      <th>1986</th>
      <th>1987</th>
      <th>...</th>
      <th>2007</th>
      <th>2008</th>
      <th>2009</th>
      <th>2010</th>
      <th>2011</th>
      <th>2012</th>
      <th>2013</th>
      <th>2014</th>
      <th>2015</th>
      <th>2016</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Albania</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>30.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>29.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Algeria</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>27.6</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Angola</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>42.7</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Argentina</td>
      <td>NaN</td>
      <td>40.8</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>42.8</td>
      <td>45.3</td>
      <td>...</td>
      <td>46.3</td>
      <td>44.5</td>
      <td>43.9</td>
      <td>43.0</td>
      <td>42.3</td>
      <td>41.2</td>
      <td>41.0</td>
      <td>41.4</td>
      <td>NaN</td>
      <td>42.4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Armenia</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>31.2</td>
      <td>29.2</td>
      <td>28.0</td>
      <td>30.0</td>
      <td>29.4</td>
      <td>29.6</td>
      <td>30.6</td>
      <td>31.5</td>
      <td>32.4</td>
      <td>32.5</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 39 columns</p>
</div>




```python
# import the data with foreign direct investment flows (inward) by country and check its structure
fdi_df = pd.read_csv('foreign_direct_investment_net_inflows_percent_of_gdp.csv')
fdi_df.head()

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>geo</th>
      <th>1970</th>
      <th>1971</th>
      <th>1972</th>
      <th>1973</th>
      <th>1974</th>
      <th>1975</th>
      <th>1976</th>
      <th>1977</th>
      <th>1978</th>
      <th>...</th>
      <th>2007</th>
      <th>2008</th>
      <th>2009</th>
      <th>2010</th>
      <th>2011</th>
      <th>2012</th>
      <th>2013</th>
      <th>2014</th>
      <th>2015</th>
      <th>2016</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Afghanistan</td>
      <td>0.0132</td>
      <td>0.0246</td>
      <td>0.0094</td>
      <td>0.0156</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.00157</td>
      <td>-0.00813</td>
      <td>NaN</td>
      <td>...</td>
      <td>1.92</td>
      <td>0.452</td>
      <td>1.58</td>
      <td>0.34</td>
      <td>0.321</td>
      <td>0.230</td>
      <td>0.186</td>
      <td>0.211</td>
      <td>0.849</td>
      <td>0.44</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Albania</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>6.10</td>
      <td>9.680</td>
      <td>11.10</td>
      <td>9.14</td>
      <td>8.130</td>
      <td>7.450</td>
      <td>9.820</td>
      <td>8.690</td>
      <td>8.730</td>
      <td>8.80</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Algeria</td>
      <td>1.6500</td>
      <td>0.0118</td>
      <td>0.6140</td>
      <td>0.5850</td>
      <td>2.71</td>
      <td>0.765</td>
      <td>1.05000</td>
      <td>0.85100</td>
      <td>0.513</td>
      <td>...</td>
      <td>1.25</td>
      <td>1.540</td>
      <td>2.00</td>
      <td>1.43</td>
      <td>1.290</td>
      <td>0.718</td>
      <td>0.807</td>
      <td>0.703</td>
      <td>-0.243</td>
      <td>1.03</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Angola</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>-1.48</td>
      <td>1.990</td>
      <td>2.92</td>
      <td>-3.91</td>
      <td>-2.900</td>
      <td>-5.980</td>
      <td>-5.700</td>
      <td>1.520</td>
      <td>9.020</td>
      <td>4.31</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Antigua and Barbuda</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>2.84000</td>
      <td>-7.850</td>
      <td>...</td>
      <td>25.80</td>
      <td>11.600</td>
      <td>6.59</td>
      <td>8.39</td>
      <td>5.710</td>
      <td>10.700</td>
      <td>11.300</td>
      <td>3.360</td>
      <td>7.050</td>
      <td>3.35</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 48 columns</p>
</div>




```python
# import the data with CO2 emissions by country and check its structure
emissions_df = pd.read_csv('co2_emissions_tonnes_per_person.csv')
emissions_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>geo</th>
      <th>1800</th>
      <th>1801</th>
      <th>1802</th>
      <th>1803</th>
      <th>1804</th>
      <th>1805</th>
      <th>1806</th>
      <th>1807</th>
      <th>1808</th>
      <th>...</th>
      <th>2005</th>
      <th>2006</th>
      <th>2007</th>
      <th>2008</th>
      <th>2009</th>
      <th>2010</th>
      <th>2011</th>
      <th>2012</th>
      <th>2013</th>
      <th>2014</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Afghanistan</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>0.0529</td>
      <td>0.0637</td>
      <td>0.0854</td>
      <td>0.154</td>
      <td>0.242</td>
      <td>0.294</td>
      <td>0.412</td>
      <td>0.35</td>
      <td>0.316</td>
      <td>0.299</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Albania</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>1.3800</td>
      <td>1.2800</td>
      <td>1.3000</td>
      <td>1.460</td>
      <td>1.480</td>
      <td>1.560</td>
      <td>1.790</td>
      <td>1.68</td>
      <td>1.730</td>
      <td>1.960</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Algeria</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>3.2200</td>
      <td>2.9900</td>
      <td>3.1900</td>
      <td>3.160</td>
      <td>3.420</td>
      <td>3.300</td>
      <td>3.290</td>
      <td>3.46</td>
      <td>3.510</td>
      <td>3.720</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Andorra</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>7.3000</td>
      <td>6.7500</td>
      <td>6.5200</td>
      <td>6.430</td>
      <td>6.120</td>
      <td>6.120</td>
      <td>5.870</td>
      <td>5.92</td>
      <td>5.900</td>
      <td>5.830</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Angola</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>0.9800</td>
      <td>1.1000</td>
      <td>1.2000</td>
      <td>1.180</td>
      <td>1.230</td>
      <td>1.240</td>
      <td>1.250</td>
      <td>1.33</td>
      <td>1.250</td>
      <td>1.290</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 216 columns</p>
</div>




```python
# The data files imported don't provide country groupings by region. To be able to summarize results by region we also import 
# a 'geographies' file retreieved from gapminder (https://www.gapminder.org/data/geo/)  

geography = pd.read_csv('geographies.csv')
geography.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>geo</th>
      <th>name</th>
      <th>four_regions</th>
      <th>eight_regions</th>
      <th>six_regions</th>
      <th>members_oecd_g77</th>
      <th>Latitude</th>
      <th>Longitude</th>
      <th>UN member since</th>
      <th>World bank region</th>
      <th>World bank income group 2017</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>afg</td>
      <td>Afghanistan</td>
      <td>asia</td>
      <td>asia_west</td>
      <td>south_asia</td>
      <td>g77</td>
      <td>33.00000</td>
      <td>66.00000</td>
      <td>19.11.1946</td>
      <td>South Asia</td>
      <td>Low income</td>
    </tr>
    <tr>
      <th>1</th>
      <td>alb</td>
      <td>Albania</td>
      <td>europe</td>
      <td>europe_east</td>
      <td>europe_central_asia</td>
      <td>others</td>
      <td>41.00000</td>
      <td>20.00000</td>
      <td>14.12.1955</td>
      <td>Europe &amp; Central Asia</td>
      <td>Upper middle income</td>
    </tr>
    <tr>
      <th>2</th>
      <td>dza</td>
      <td>Algeria</td>
      <td>africa</td>
      <td>africa_north</td>
      <td>middle_east_north_africa</td>
      <td>g77</td>
      <td>28.00000</td>
      <td>3.00000</td>
      <td>8.10.1962</td>
      <td>Middle East &amp; North Africa</td>
      <td>Upper middle income</td>
    </tr>
    <tr>
      <th>3</th>
      <td>and</td>
      <td>Andorra</td>
      <td>europe</td>
      <td>europe_west</td>
      <td>europe_central_asia</td>
      <td>others</td>
      <td>42.50779</td>
      <td>1.52109</td>
      <td>28.7.1993</td>
      <td>Europe &amp; Central Asia</td>
      <td>High income</td>
    </tr>
    <tr>
      <th>4</th>
      <td>ago</td>
      <td>Angola</td>
      <td>africa</td>
      <td>africa_sub_saharan</td>
      <td>sub_saharan_africa</td>
      <td>g77</td>
      <td>-12.50000</td>
      <td>18.50000</td>
      <td>1.12.1976</td>
      <td>Sub-Saharan Africa</td>
      <td>Lower middle income</td>
    </tr>
  </tbody>
</table>
</div>



We now check the shape of each file:


```python
# we find out the structure of each dataframe (number of rows and columns)

print("Structure of hdi_df: " + str(hdi_df.shape))
print("Structure of emissions_df: " + str(emissions_df.shape))
print("Structure of gini_df: " + str(gini_df.shape))
print("Structure of fdi_df: " + str(fdi_df.shape))
```

    Structure of hdi_df: (187, 27)
    Structure of emissions_df: (192, 216)
    Structure of gini_df: (163, 39)
    Structure of fdi_df: (188, 48)
    

The shape of the datafiles is quite different in terms of the years for which data is available, with one having data for as far back as 1800 and other only since 1990. Because the research questions are raised for a timeframe starting in 2000, columns referring to previous years will be dropped. The file structure is similar in that each row describes data for one given country, which makes duplicated information very unlikely. 

We now check for duplicate data:


```python
# Checking for possible duplicates
sum(hdi_df.duplicated()), sum(gini_df.duplicated()), sum(fdi_df.duplicated()), sum(emissions_df.duplicated())

```




    (0, 0, 0, 0)



As shown above, we confirm that no duplicates exist in the datasets. We now verify for missing information and accuracy of datatypes:


```python
# each dataset is checked for datatypes

print("\nhdi_df"), hdi_df.info()
print("\ngini_df: "), gini_df.info()
print("\nfdi_df: "), fdi_df.info()
print("\nemissions_df"), emissions_df.info()
```

    
    hdi_df
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 187 entries, 0 to 186
    Data columns (total 27 columns):
    geo     187 non-null object
    1990    143 non-null float64
    1991    143 non-null float64
    1992    143 non-null float64
    1993    143 non-null float64
    1994    143 non-null float64
    1995    147 non-null float64
    1996    147 non-null float64
    1997    147 non-null float64
    1998    147 non-null float64
    1999    150 non-null float64
    2000    167 non-null float64
    2001    167 non-null float64
    2002    167 non-null float64
    2003    169 non-null float64
    2004    172 non-null float64
    2005    181 non-null float64
    2006    181 non-null float64
    2007    181 non-null float64
    2008    181 non-null float64
    2009    181 non-null float64
    2010    187 non-null float64
    2011    187 non-null float64
    2012    187 non-null float64
    2013    187 non-null float64
    2014    187 non-null float64
    2015    187 non-null float64
    dtypes: float64(26), object(1)
    memory usage: 39.5+ KB
    
    gini_df: 
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 163 entries, 0 to 162
    Data columns (total 39 columns):
    geo     163 non-null object
    1979    3 non-null float64
    1980    2 non-null float64
    1981    7 non-null float64
    1982    1 non-null float64
    1983    2 non-null float64
    1984    5 non-null float64
    1985    9 non-null float64
    1986    11 non-null float64
    1987    13 non-null float64
    1988    10 non-null float64
    1989    12 non-null float64
    1990    14 non-null float64
    1991    15 non-null float64
    1992    24 non-null float64
    1993    21 non-null float64
    1994    22 non-null float64
    1995    24 non-null float64
    1996    29 non-null float64
    1997    23 non-null float64
    1998    37 non-null float64
    1999    28 non-null float64
    2000    34 non-null float64
    2001    35 non-null float64
    2002    46 non-null float64
    2003    55 non-null float64
    2004    70 non-null float64
    2005    73 non-null float64
    2006    73 non-null float64
    2007    72 non-null float64
    2008    71 non-null float64
    2009    75 non-null float64
    2010    82 non-null float64
    2011    73 non-null float64
    2012    76 non-null float64
    2013    71 non-null float64
    2014    68 non-null float64
    2015    57 non-null float64
    2016    25 non-null float64
    dtypes: float64(38), object(1)
    memory usage: 49.7+ KB
    
    fdi_df: 
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 188 entries, 0 to 187
    Data columns (total 48 columns):
    geo     188 non-null object
    1970    63 non-null float64
    1971    66 non-null float64
    1972    71 non-null float64
    1973    71 non-null float64
    1974    74 non-null float64
    1975    83 non-null float64
    1976    92 non-null float64
    1977    107 non-null float64
    1978    109 non-null float64
    1979    112 non-null float64
    1980    114 non-null float64
    1981    117 non-null float64
    1982    120 non-null float64
    1983    120 non-null float64
    1984    122 non-null float64
    1985    128 non-null float64
    1986    130 non-null float64
    1987    130 non-null float64
    1988    133 non-null float64
    1989    134 non-null float64
    1990    143 non-null float64
    1991    145 non-null float64
    1992    153 non-null float64
    1993    157 non-null float64
    1994    158 non-null float64
    1995    168 non-null float64
    1996    172 non-null float64
    1997    173 non-null float64
    1998    171 non-null float64
    1999    173 non-null float64
    2000    174 non-null float64
    2001    179 non-null float64
    2002    183 non-null float64
    2003    183 non-null float64
    2004    185 non-null float64
    2005    185 non-null float64
    2006    185 non-null float64
    2007    186 non-null float64
    2008    185 non-null float64
    2009    185 non-null float64
    2010    185 non-null float64
    2011    184 non-null float64
    2012    184 non-null float64
    2013    185 non-null float64
    2014    185 non-null float64
    2015    184 non-null float64
    2016    180 non-null float64
    dtypes: float64(47), object(1)
    memory usage: 70.6+ KB
    
    emissions_df
    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 192 entries, 0 to 191
    Columns: 216 entries, geo to 2014
    dtypes: float64(215), object(1)
    memory usage: 324.1+ KB
    




    (None, None)



The results above show that the datatypes are homogenous for every dataset and dont't need to be modified, however many datapoints have missing information that will need to be corrected.


### Data Cleaning
According to the initial checking of the data, some columns need to be dropped and missing information need to be corrected. Finally, we want to rename the columns and merge all files into one before proceeding with the Exploratory Data Analysis.

Dropping unwanted columns:


```python
# droping the years up to 2000 in the HDI dataframe using its column index
hdi_df.drop(hdi_df.columns[1:11], axis=1, inplace=True)
hdi_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>geo</th>
      <th>2000</th>
      <th>2001</th>
      <th>2002</th>
      <th>2003</th>
      <th>2004</th>
      <th>2005</th>
      <th>2006</th>
      <th>2007</th>
      <th>2008</th>
      <th>2009</th>
      <th>2010</th>
      <th>2011</th>
      <th>2012</th>
      <th>2013</th>
      <th>2014</th>
      <th>2015</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Afghanistan</td>
      <td>0.340</td>
      <td>0.341</td>
      <td>0.373</td>
      <td>0.381</td>
      <td>0.396</td>
      <td>0.405</td>
      <td>0.415</td>
      <td>0.433</td>
      <td>0.434</td>
      <td>0.448</td>
      <td>0.454</td>
      <td>0.463</td>
      <td>0.470</td>
      <td>0.476</td>
      <td>0.479</td>
      <td>0.479</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Albania</td>
      <td>0.662</td>
      <td>0.670</td>
      <td>0.674</td>
      <td>0.681</td>
      <td>0.685</td>
      <td>0.696</td>
      <td>0.703</td>
      <td>0.713</td>
      <td>0.721</td>
      <td>0.725</td>
      <td>0.738</td>
      <td>0.752</td>
      <td>0.759</td>
      <td>0.761</td>
      <td>0.762</td>
      <td>0.764</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Algeria</td>
      <td>0.644</td>
      <td>0.653</td>
      <td>0.663</td>
      <td>0.673</td>
      <td>0.680</td>
      <td>0.686</td>
      <td>0.690</td>
      <td>0.697</td>
      <td>0.705</td>
      <td>0.714</td>
      <td>0.724</td>
      <td>0.732</td>
      <td>0.737</td>
      <td>0.741</td>
      <td>0.743</td>
      <td>0.745</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Andorra</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>0.819</td>
      <td>0.819</td>
      <td>0.843</td>
      <td>0.850</td>
      <td>0.857</td>
      <td>0.858</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Angola</td>
      <td>0.391</td>
      <td>0.401</td>
      <td>0.406</td>
      <td>0.415</td>
      <td>0.426</td>
      <td>0.439</td>
      <td>0.454</td>
      <td>0.468</td>
      <td>0.480</td>
      <td>0.488</td>
      <td>0.495</td>
      <td>0.508</td>
      <td>0.523</td>
      <td>0.527</td>
      <td>0.531</td>
      <td>0.533</td>
    </tr>
  </tbody>
</table>
</div>




```python
# droping the years up to 2000 in the gini dataframe using its column index
gini_df.drop(gini_df.columns[1:22], axis=1, inplace=True)
gini_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>geo</th>
      <th>2000</th>
      <th>2001</th>
      <th>2002</th>
      <th>2003</th>
      <th>2004</th>
      <th>2005</th>
      <th>2006</th>
      <th>2007</th>
      <th>2008</th>
      <th>2009</th>
      <th>2010</th>
      <th>2011</th>
      <th>2012</th>
      <th>2013</th>
      <th>2014</th>
      <th>2015</th>
      <th>2016</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Albania</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>31.7</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>30.6</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>30.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>29.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Algeria</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>27.6</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Angola</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>42.7</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Argentina</td>
      <td>51.1</td>
      <td>53.3</td>
      <td>53.8</td>
      <td>50.7</td>
      <td>48.3</td>
      <td>47.7</td>
      <td>46.6</td>
      <td>46.3</td>
      <td>44.5</td>
      <td>43.9</td>
      <td>43.0</td>
      <td>42.3</td>
      <td>41.2</td>
      <td>41.0</td>
      <td>41.4</td>
      <td>NaN</td>
      <td>42.4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Armenia</td>
      <td>NaN</td>
      <td>35.4</td>
      <td>34.8</td>
      <td>33.0</td>
      <td>37.5</td>
      <td>36.0</td>
      <td>29.7</td>
      <td>31.2</td>
      <td>29.2</td>
      <td>28.0</td>
      <td>30.0</td>
      <td>29.4</td>
      <td>29.6</td>
      <td>30.6</td>
      <td>31.5</td>
      <td>32.4</td>
      <td>32.5</td>
    </tr>
  </tbody>
</table>
</div>




```python
# droping the years up to 2000 in the FDI dataframe using its column index
fdi_df.drop(fdi_df.columns[1:31], axis=1, inplace=True)
fdi_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>geo</th>
      <th>2000</th>
      <th>2001</th>
      <th>2002</th>
      <th>2003</th>
      <th>2004</th>
      <th>2005</th>
      <th>2006</th>
      <th>2007</th>
      <th>2008</th>
      <th>2009</th>
      <th>2010</th>
      <th>2011</th>
      <th>2012</th>
      <th>2013</th>
      <th>2014</th>
      <th>2015</th>
      <th>2016</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Afghanistan</td>
      <td>NaN</td>
      <td>0.0276</td>
      <td>1.21</td>
      <td>1.26</td>
      <td>3.54</td>
      <td>4.32</td>
      <td>3.3700</td>
      <td>1.92</td>
      <td>0.452</td>
      <td>1.58</td>
      <td>0.34</td>
      <td>0.321</td>
      <td>0.230</td>
      <td>0.186</td>
      <td>0.211</td>
      <td>0.849</td>
      <td>0.44</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Albania</td>
      <td>3.940</td>
      <td>5.1000</td>
      <td>3.04</td>
      <td>3.10</td>
      <td>4.67</td>
      <td>3.22</td>
      <td>3.6200</td>
      <td>6.10</td>
      <td>9.680</td>
      <td>11.10</td>
      <td>9.14</td>
      <td>8.130</td>
      <td>7.450</td>
      <td>9.820</td>
      <td>8.690</td>
      <td>8.730</td>
      <td>8.80</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Algeria</td>
      <td>0.511</td>
      <td>2.0300</td>
      <td>1.88</td>
      <td>0.94</td>
      <td>1.03</td>
      <td>1.12</td>
      <td>1.5700</td>
      <td>1.25</td>
      <td>1.540</td>
      <td>2.00</td>
      <td>1.43</td>
      <td>1.290</td>
      <td>0.718</td>
      <td>0.807</td>
      <td>0.703</td>
      <td>-0.243</td>
      <td>1.03</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Angola</td>
      <td>9.620</td>
      <td>24.0000</td>
      <td>14.00</td>
      <td>25.20</td>
      <td>11.20</td>
      <td>-4.62</td>
      <td>-0.0902</td>
      <td>-1.48</td>
      <td>1.990</td>
      <td>2.92</td>
      <td>-3.91</td>
      <td>-2.900</td>
      <td>-5.980</td>
      <td>-5.700</td>
      <td>1.520</td>
      <td>9.020</td>
      <td>4.31</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Antigua and Barbuda</td>
      <td>5.190</td>
      <td>12.3000</td>
      <td>8.09</td>
      <td>19.40</td>
      <td>8.74</td>
      <td>21.60</td>
      <td>31.0000</td>
      <td>25.80</td>
      <td>11.600</td>
      <td>6.59</td>
      <td>8.39</td>
      <td>5.710</td>
      <td>10.700</td>
      <td>11.300</td>
      <td>3.360</td>
      <td>7.050</td>
      <td>3.35</td>
    </tr>
  </tbody>
</table>
</div>




```python
# droping the years up to 2000 in the CO2 emissions dataframe, using its column index
emissions_df.drop(emissions_df.columns[1:201], axis=1, inplace=True)
emissions_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>geo</th>
      <th>2000</th>
      <th>2001</th>
      <th>2002</th>
      <th>2003</th>
      <th>2004</th>
      <th>2005</th>
      <th>2006</th>
      <th>2007</th>
      <th>2008</th>
      <th>2009</th>
      <th>2010</th>
      <th>2011</th>
      <th>2012</th>
      <th>2013</th>
      <th>2014</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Afghanistan</td>
      <td>0.0385</td>
      <td>0.039</td>
      <td>0.0487</td>
      <td>0.0518</td>
      <td>0.0394</td>
      <td>0.0529</td>
      <td>0.0637</td>
      <td>0.0854</td>
      <td>0.154</td>
      <td>0.242</td>
      <td>0.294</td>
      <td>0.412</td>
      <td>0.35</td>
      <td>0.316</td>
      <td>0.299</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Albania</td>
      <td>0.9680</td>
      <td>1.030</td>
      <td>1.2000</td>
      <td>1.3800</td>
      <td>1.3400</td>
      <td>1.3800</td>
      <td>1.2800</td>
      <td>1.3000</td>
      <td>1.460</td>
      <td>1.480</td>
      <td>1.560</td>
      <td>1.790</td>
      <td>1.68</td>
      <td>1.730</td>
      <td>1.960</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Algeria</td>
      <td>2.8200</td>
      <td>2.670</td>
      <td>2.8100</td>
      <td>2.8300</td>
      <td>2.7000</td>
      <td>3.2200</td>
      <td>2.9900</td>
      <td>3.1900</td>
      <td>3.160</td>
      <td>3.420</td>
      <td>3.300</td>
      <td>3.290</td>
      <td>3.46</td>
      <td>3.510</td>
      <td>3.720</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Andorra</td>
      <td>8.0200</td>
      <td>7.790</td>
      <td>7.5900</td>
      <td>7.3200</td>
      <td>7.3600</td>
      <td>7.3000</td>
      <td>6.7500</td>
      <td>6.5200</td>
      <td>6.430</td>
      <td>6.120</td>
      <td>6.120</td>
      <td>5.870</td>
      <td>5.92</td>
      <td>5.900</td>
      <td>5.830</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Angola</td>
      <td>0.5800</td>
      <td>0.573</td>
      <td>0.7210</td>
      <td>0.4980</td>
      <td>0.9960</td>
      <td>0.9800</td>
      <td>1.1000</td>
      <td>1.2000</td>
      <td>1.180</td>
      <td>1.230</td>
      <td>1.240</td>
      <td>1.250</td>
      <td>1.33</td>
      <td>1.250</td>
      <td>1.290</td>
    </tr>
  </tbody>
</table>
</div>



We must now resolve the missing data in the dataframes. For the HDI,there are 20 countries with missing data. This NaN should not be interpolated row by row (they are different countries) and column by column is not possible when the NaN are all located at the left most columns. We will exclude those countries from the analysis.


```python
# dropping the rows with missing information
hdi_df.dropna(inplace=True)
hdi_df.info()
```

    <class 'pandas.core.frame.DataFrame'>
    Int64Index: 167 entries, 0 to 186
    Data columns (total 17 columns):
    geo     167 non-null object
    2000    167 non-null float64
    2001    167 non-null float64
    2002    167 non-null float64
    2003    167 non-null float64
    2004    167 non-null float64
    2005    167 non-null float64
    2006    167 non-null float64
    2007    167 non-null float64
    2008    167 non-null float64
    2009    167 non-null float64
    2010    167 non-null float64
    2011    167 non-null float64
    2012    167 non-null float64
    2013    167 non-null float64
    2014    167 non-null float64
    2015    167 non-null float64
    dtypes: float64(16), object(1)
    memory usage: 23.5+ KB
    

The summary above shows that there is no longer missing information in the dataframe. We will use this dataframe to answer the first two questions. 


```python
# rename GINI columns
gini_df.rename(columns= lambda x: x + "_gini", inplace=True)
gini_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>geo_gini</th>
      <th>2000_gini</th>
      <th>2001_gini</th>
      <th>2002_gini</th>
      <th>2003_gini</th>
      <th>2004_gini</th>
      <th>2005_gini</th>
      <th>2006_gini</th>
      <th>2007_gini</th>
      <th>2008_gini</th>
      <th>2009_gini</th>
      <th>2010_gini</th>
      <th>2011_gini</th>
      <th>2012_gini</th>
      <th>2013_gini</th>
      <th>2014_gini</th>
      <th>2015_gini</th>
      <th>2016_gini</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Albania</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>31.7</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>30.6</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>30.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>29.0</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Algeria</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>27.6</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Angola</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>42.7</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Argentina</td>
      <td>51.1</td>
      <td>53.3</td>
      <td>53.8</td>
      <td>50.7</td>
      <td>48.3</td>
      <td>47.7</td>
      <td>46.6</td>
      <td>46.3</td>
      <td>44.5</td>
      <td>43.9</td>
      <td>43.0</td>
      <td>42.3</td>
      <td>41.2</td>
      <td>41.0</td>
      <td>41.4</td>
      <td>NaN</td>
      <td>42.4</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Armenia</td>
      <td>NaN</td>
      <td>35.4</td>
      <td>34.8</td>
      <td>33.0</td>
      <td>37.5</td>
      <td>36.0</td>
      <td>29.7</td>
      <td>31.2</td>
      <td>29.2</td>
      <td>28.0</td>
      <td>30.0</td>
      <td>29.4</td>
      <td>29.6</td>
      <td>30.6</td>
      <td>31.5</td>
      <td>32.4</td>
      <td>32.5</td>
    </tr>
  </tbody>
</table>
</div>




```python
# rename FDI columns
fdi_df.rename(columns= lambda x: x + "_fdi", inplace=True)
fdi_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>geo_fdi</th>
      <th>2000_fdi</th>
      <th>2001_fdi</th>
      <th>2002_fdi</th>
      <th>2003_fdi</th>
      <th>2004_fdi</th>
      <th>2005_fdi</th>
      <th>2006_fdi</th>
      <th>2007_fdi</th>
      <th>2008_fdi</th>
      <th>2009_fdi</th>
      <th>2010_fdi</th>
      <th>2011_fdi</th>
      <th>2012_fdi</th>
      <th>2013_fdi</th>
      <th>2014_fdi</th>
      <th>2015_fdi</th>
      <th>2016_fdi</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Afghanistan</td>
      <td>NaN</td>
      <td>0.0276</td>
      <td>1.21</td>
      <td>1.26</td>
      <td>3.54</td>
      <td>4.32</td>
      <td>3.3700</td>
      <td>1.92</td>
      <td>0.452</td>
      <td>1.58</td>
      <td>0.34</td>
      <td>0.321</td>
      <td>0.230</td>
      <td>0.186</td>
      <td>0.211</td>
      <td>0.849</td>
      <td>0.44</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Albania</td>
      <td>3.940</td>
      <td>5.1000</td>
      <td>3.04</td>
      <td>3.10</td>
      <td>4.67</td>
      <td>3.22</td>
      <td>3.6200</td>
      <td>6.10</td>
      <td>9.680</td>
      <td>11.10</td>
      <td>9.14</td>
      <td>8.130</td>
      <td>7.450</td>
      <td>9.820</td>
      <td>8.690</td>
      <td>8.730</td>
      <td>8.80</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Algeria</td>
      <td>0.511</td>
      <td>2.0300</td>
      <td>1.88</td>
      <td>0.94</td>
      <td>1.03</td>
      <td>1.12</td>
      <td>1.5700</td>
      <td>1.25</td>
      <td>1.540</td>
      <td>2.00</td>
      <td>1.43</td>
      <td>1.290</td>
      <td>0.718</td>
      <td>0.807</td>
      <td>0.703</td>
      <td>-0.243</td>
      <td>1.03</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Angola</td>
      <td>9.620</td>
      <td>24.0000</td>
      <td>14.00</td>
      <td>25.20</td>
      <td>11.20</td>
      <td>-4.62</td>
      <td>-0.0902</td>
      <td>-1.48</td>
      <td>1.990</td>
      <td>2.92</td>
      <td>-3.91</td>
      <td>-2.900</td>
      <td>-5.980</td>
      <td>-5.700</td>
      <td>1.520</td>
      <td>9.020</td>
      <td>4.31</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Antigua and Barbuda</td>
      <td>5.190</td>
      <td>12.3000</td>
      <td>8.09</td>
      <td>19.40</td>
      <td>8.74</td>
      <td>21.60</td>
      <td>31.0000</td>
      <td>25.80</td>
      <td>11.600</td>
      <td>6.59</td>
      <td>8.39</td>
      <td>5.710</td>
      <td>10.700</td>
      <td>11.300</td>
      <td>3.360</td>
      <td>7.050</td>
      <td>3.35</td>
    </tr>
  </tbody>
</table>
</div>




```python
# rename emissions columns
emissions_df.rename(columns= lambda x: x + "_co2", inplace=True)
emissions_df.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>geo_co2</th>
      <th>2000_co2</th>
      <th>2001_co2</th>
      <th>2002_co2</th>
      <th>2003_co2</th>
      <th>2004_co2</th>
      <th>2005_co2</th>
      <th>2006_co2</th>
      <th>2007_co2</th>
      <th>2008_co2</th>
      <th>2009_co2</th>
      <th>2010_co2</th>
      <th>2011_co2</th>
      <th>2012_co2</th>
      <th>2013_co2</th>
      <th>2014_co2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Afghanistan</td>
      <td>0.0385</td>
      <td>0.039</td>
      <td>0.0487</td>
      <td>0.0518</td>
      <td>0.0394</td>
      <td>0.0529</td>
      <td>0.0637</td>
      <td>0.0854</td>
      <td>0.154</td>
      <td>0.242</td>
      <td>0.294</td>
      <td>0.412</td>
      <td>0.35</td>
      <td>0.316</td>
      <td>0.299</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Albania</td>
      <td>0.9680</td>
      <td>1.030</td>
      <td>1.2000</td>
      <td>1.3800</td>
      <td>1.3400</td>
      <td>1.3800</td>
      <td>1.2800</td>
      <td>1.3000</td>
      <td>1.460</td>
      <td>1.480</td>
      <td>1.560</td>
      <td>1.790</td>
      <td>1.68</td>
      <td>1.730</td>
      <td>1.960</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Algeria</td>
      <td>2.8200</td>
      <td>2.670</td>
      <td>2.8100</td>
      <td>2.8300</td>
      <td>2.7000</td>
      <td>3.2200</td>
      <td>2.9900</td>
      <td>3.1900</td>
      <td>3.160</td>
      <td>3.420</td>
      <td>3.300</td>
      <td>3.290</td>
      <td>3.46</td>
      <td>3.510</td>
      <td>3.720</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Andorra</td>
      <td>8.0200</td>
      <td>7.790</td>
      <td>7.5900</td>
      <td>7.3200</td>
      <td>7.3600</td>
      <td>7.3000</td>
      <td>6.7500</td>
      <td>6.5200</td>
      <td>6.430</td>
      <td>6.120</td>
      <td>6.120</td>
      <td>5.870</td>
      <td>5.92</td>
      <td>5.900</td>
      <td>5.830</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Angola</td>
      <td>0.5800</td>
      <td>0.573</td>
      <td>0.7210</td>
      <td>0.4980</td>
      <td>0.9960</td>
      <td>0.9800</td>
      <td>1.1000</td>
      <td>1.2000</td>
      <td>1.180</td>
      <td>1.230</td>
      <td>1.240</td>
      <td>1.250</td>
      <td>1.33</td>
      <td>1.250</td>
      <td>1.290</td>
    </tr>
  </tbody>
</table>
</div>




```python
# merging the four datasets, using the country name as key.
df_combined1 = pd.merge(hdi_df, gini_df, left_on='geo', right_on='geo_gini')
df_combined2 = pd.merge(df_combined1, fdi_df, left_on='geo', right_on='geo_fdi')
df_combined3 = pd.merge(df_combined2, emissions_df, left_on='geo', right_on='geo_co2')
df_combined3.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>geo</th>
      <th>2000</th>
      <th>2001</th>
      <th>2002</th>
      <th>2003</th>
      <th>2004</th>
      <th>2005</th>
      <th>2006</th>
      <th>2007</th>
      <th>2008</th>
      <th>...</th>
      <th>2005_co2</th>
      <th>2006_co2</th>
      <th>2007_co2</th>
      <th>2008_co2</th>
      <th>2009_co2</th>
      <th>2010_co2</th>
      <th>2011_co2</th>
      <th>2012_co2</th>
      <th>2013_co2</th>
      <th>2014_co2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Albania</td>
      <td>0.662</td>
      <td>0.670</td>
      <td>0.674</td>
      <td>0.681</td>
      <td>0.685</td>
      <td>0.696</td>
      <td>0.703</td>
      <td>0.713</td>
      <td>0.721</td>
      <td>...</td>
      <td>1.38</td>
      <td>1.28</td>
      <td>1.30</td>
      <td>1.46</td>
      <td>1.48</td>
      <td>1.56</td>
      <td>1.79</td>
      <td>1.68</td>
      <td>1.73</td>
      <td>1.96</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Algeria</td>
      <td>0.644</td>
      <td>0.653</td>
      <td>0.663</td>
      <td>0.673</td>
      <td>0.680</td>
      <td>0.686</td>
      <td>0.690</td>
      <td>0.697</td>
      <td>0.705</td>
      <td>...</td>
      <td>3.22</td>
      <td>2.99</td>
      <td>3.19</td>
      <td>3.16</td>
      <td>3.42</td>
      <td>3.30</td>
      <td>3.29</td>
      <td>3.46</td>
      <td>3.51</td>
      <td>3.72</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Angola</td>
      <td>0.391</td>
      <td>0.401</td>
      <td>0.406</td>
      <td>0.415</td>
      <td>0.426</td>
      <td>0.439</td>
      <td>0.454</td>
      <td>0.468</td>
      <td>0.480</td>
      <td>...</td>
      <td>0.98</td>
      <td>1.10</td>
      <td>1.20</td>
      <td>1.18</td>
      <td>1.23</td>
      <td>1.24</td>
      <td>1.25</td>
      <td>1.33</td>
      <td>1.25</td>
      <td>1.29</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Argentina</td>
      <td>0.771</td>
      <td>0.776</td>
      <td>0.770</td>
      <td>0.775</td>
      <td>0.780</td>
      <td>0.782</td>
      <td>0.788</td>
      <td>0.792</td>
      <td>0.794</td>
      <td>...</td>
      <td>4.14</td>
      <td>4.43</td>
      <td>4.38</td>
      <td>4.68</td>
      <td>4.41</td>
      <td>4.56</td>
      <td>4.60</td>
      <td>4.57</td>
      <td>4.46</td>
      <td>4.75</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Armenia</td>
      <td>0.644</td>
      <td>0.645</td>
      <td>0.657</td>
      <td>0.668</td>
      <td>0.679</td>
      <td>0.692</td>
      <td>0.707</td>
      <td>0.721</td>
      <td>0.725</td>
      <td>...</td>
      <td>1.46</td>
      <td>1.48</td>
      <td>1.73</td>
      <td>1.91</td>
      <td>1.51</td>
      <td>1.47</td>
      <td>1.71</td>
      <td>1.98</td>
      <td>1.90</td>
      <td>1.90</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 69 columns</p>
</div>




```python
# merging the resulting dataset with the geography table. We are interested in the world's division in eight different regions.

df_merged = pd.merge(df_combined3,geography[['name','eight_regions']],left_on='geo', right_on='name', how='left')
df_merged.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>geo</th>
      <th>2000</th>
      <th>2001</th>
      <th>2002</th>
      <th>2003</th>
      <th>2004</th>
      <th>2005</th>
      <th>2006</th>
      <th>2007</th>
      <th>2008</th>
      <th>...</th>
      <th>2007_co2</th>
      <th>2008_co2</th>
      <th>2009_co2</th>
      <th>2010_co2</th>
      <th>2011_co2</th>
      <th>2012_co2</th>
      <th>2013_co2</th>
      <th>2014_co2</th>
      <th>name</th>
      <th>eight_regions</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Albania</td>
      <td>0.662</td>
      <td>0.670</td>
      <td>0.674</td>
      <td>0.681</td>
      <td>0.685</td>
      <td>0.696</td>
      <td>0.703</td>
      <td>0.713</td>
      <td>0.721</td>
      <td>...</td>
      <td>1.30</td>
      <td>1.46</td>
      <td>1.48</td>
      <td>1.56</td>
      <td>1.79</td>
      <td>1.68</td>
      <td>1.73</td>
      <td>1.96</td>
      <td>Albania</td>
      <td>europe_east</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Algeria</td>
      <td>0.644</td>
      <td>0.653</td>
      <td>0.663</td>
      <td>0.673</td>
      <td>0.680</td>
      <td>0.686</td>
      <td>0.690</td>
      <td>0.697</td>
      <td>0.705</td>
      <td>...</td>
      <td>3.19</td>
      <td>3.16</td>
      <td>3.42</td>
      <td>3.30</td>
      <td>3.29</td>
      <td>3.46</td>
      <td>3.51</td>
      <td>3.72</td>
      <td>Algeria</td>
      <td>africa_north</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Angola</td>
      <td>0.391</td>
      <td>0.401</td>
      <td>0.406</td>
      <td>0.415</td>
      <td>0.426</td>
      <td>0.439</td>
      <td>0.454</td>
      <td>0.468</td>
      <td>0.480</td>
      <td>...</td>
      <td>1.20</td>
      <td>1.18</td>
      <td>1.23</td>
      <td>1.24</td>
      <td>1.25</td>
      <td>1.33</td>
      <td>1.25</td>
      <td>1.29</td>
      <td>Angola</td>
      <td>africa_sub_saharan</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Argentina</td>
      <td>0.771</td>
      <td>0.776</td>
      <td>0.770</td>
      <td>0.775</td>
      <td>0.780</td>
      <td>0.782</td>
      <td>0.788</td>
      <td>0.792</td>
      <td>0.794</td>
      <td>...</td>
      <td>4.38</td>
      <td>4.68</td>
      <td>4.41</td>
      <td>4.56</td>
      <td>4.60</td>
      <td>4.57</td>
      <td>4.46</td>
      <td>4.75</td>
      <td>Argentina</td>
      <td>america_south</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Armenia</td>
      <td>0.644</td>
      <td>0.645</td>
      <td>0.657</td>
      <td>0.668</td>
      <td>0.679</td>
      <td>0.692</td>
      <td>0.707</td>
      <td>0.721</td>
      <td>0.725</td>
      <td>...</td>
      <td>1.73</td>
      <td>1.91</td>
      <td>1.51</td>
      <td>1.47</td>
      <td>1.71</td>
      <td>1.98</td>
      <td>1.90</td>
      <td>1.90</td>
      <td>Armenia</td>
      <td>europe_east</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 71 columns</p>
</div>




```python
# export file
df_merged.to_csv('gapminder_merged.csv', index=False)
```

<a id='eda'></a>
## Exploratory Data Analysis


### Research Question 1: According to the HDI, are people in more countries living in better conditions in 2015 than in 2000? in which proportion? 


```python
# we calculate the change for each country's HDI between 2000 and 2015

df_merged['change_2000_2015'] = df_merged['2015'] - df_merged['2000']
df_merged.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>geo</th>
      <th>2000</th>
      <th>2001</th>
      <th>2002</th>
      <th>2003</th>
      <th>2004</th>
      <th>2005</th>
      <th>2006</th>
      <th>2007</th>
      <th>2008</th>
      <th>...</th>
      <th>2008_co2</th>
      <th>2009_co2</th>
      <th>2010_co2</th>
      <th>2011_co2</th>
      <th>2012_co2</th>
      <th>2013_co2</th>
      <th>2014_co2</th>
      <th>name</th>
      <th>eight_regions</th>
      <th>change_2000_2015</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Albania</td>
      <td>0.662</td>
      <td>0.670</td>
      <td>0.674</td>
      <td>0.681</td>
      <td>0.685</td>
      <td>0.696</td>
      <td>0.703</td>
      <td>0.713</td>
      <td>0.721</td>
      <td>...</td>
      <td>1.46</td>
      <td>1.48</td>
      <td>1.56</td>
      <td>1.79</td>
      <td>1.68</td>
      <td>1.73</td>
      <td>1.96</td>
      <td>Albania</td>
      <td>europe_east</td>
      <td>0.102</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Algeria</td>
      <td>0.644</td>
      <td>0.653</td>
      <td>0.663</td>
      <td>0.673</td>
      <td>0.680</td>
      <td>0.686</td>
      <td>0.690</td>
      <td>0.697</td>
      <td>0.705</td>
      <td>...</td>
      <td>3.16</td>
      <td>3.42</td>
      <td>3.30</td>
      <td>3.29</td>
      <td>3.46</td>
      <td>3.51</td>
      <td>3.72</td>
      <td>Algeria</td>
      <td>africa_north</td>
      <td>0.101</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Angola</td>
      <td>0.391</td>
      <td>0.401</td>
      <td>0.406</td>
      <td>0.415</td>
      <td>0.426</td>
      <td>0.439</td>
      <td>0.454</td>
      <td>0.468</td>
      <td>0.480</td>
      <td>...</td>
      <td>1.18</td>
      <td>1.23</td>
      <td>1.24</td>
      <td>1.25</td>
      <td>1.33</td>
      <td>1.25</td>
      <td>1.29</td>
      <td>Angola</td>
      <td>africa_sub_saharan</td>
      <td>0.142</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Argentina</td>
      <td>0.771</td>
      <td>0.776</td>
      <td>0.770</td>
      <td>0.775</td>
      <td>0.780</td>
      <td>0.782</td>
      <td>0.788</td>
      <td>0.792</td>
      <td>0.794</td>
      <td>...</td>
      <td>4.68</td>
      <td>4.41</td>
      <td>4.56</td>
      <td>4.60</td>
      <td>4.57</td>
      <td>4.46</td>
      <td>4.75</td>
      <td>Argentina</td>
      <td>america_south</td>
      <td>0.056</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Armenia</td>
      <td>0.644</td>
      <td>0.645</td>
      <td>0.657</td>
      <td>0.668</td>
      <td>0.679</td>
      <td>0.692</td>
      <td>0.707</td>
      <td>0.721</td>
      <td>0.725</td>
      <td>...</td>
      <td>1.91</td>
      <td>1.51</td>
      <td>1.47</td>
      <td>1.71</td>
      <td>1.98</td>
      <td>1.90</td>
      <td>1.90</td>
      <td>Armenia</td>
      <td>europe_east</td>
      <td>0.099</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 72 columns</p>
</div>




```python
# using boxplots, we compare the HDI data between 2000 and 2015. Visually, an overall improvement of the index is evident
df_merged.boxplot(column=['2000','2005','2010','2015']);
```


![png](output_33_0.png)



```python
# Counting the positive and negative changes of the Index between 2000 and 2015
df_merged['improved'] = df_merged['change_2000_2015'] > 0
df_merged['improved'].value_counts()
```




    True     146
    False      1
    Name: improved, dtype: int64




```python
# plotting the number of countries with positive change in the HDI

plt.bar(["improved", "decline"], df_merged['improved'].value_counts())
plt.title("Number of countries by change in HDI 2000 - 2015")
plt.xlabel("Type of change")
plt.ylabel("Number of Countries");
```


![png](output_35_0.png)



```python
#Almost 100% of the countries improved their HDI. The exact proportion is obtained by a simple division:

146 / 147
```




    0.9931972789115646



<a id='eda'></a>

### Research Question 2: Is the average Human Development improving? by how much?


```python
# we plot histograms for the HDI data for 2000 and 2015 to have a general idea of its evolution

fig, ax = plt.subplots(figsize=(8,6))
ax.hist(df_merged['2000'], alpha=0.5, label="2000")
ax.hist(df_merged['2015'], alpha=0.5, label="2015")
ax.set_title('HDI distribution 2000 - 2015')
ax.set_xlabel('HDI')
ax.set_ylabel('Number of countries')
ax.legend(loc='upper right')
plt.show()
```


![png](output_38_0.png)


The above histograms show an evident improvement in the index towards better living conditions


```python
# We calculate the HDI mean for 2000 and 2015 

mean_hdi_2000 = df_merged['2000'].mean()
mean_hdi_2015 = df_merged['2015'].mean()
print('The mean HDI in 2000 was: ', mean_hdi_2000)
print('The mean HDI in 2015 was: ', mean_hdi_2015)

```

    The mean HDI in 2000 was:  0.6154149659863947
    The mean HDI in 2015 was:  0.6947482993197279
    


```python
# we calculate the growth of the world's average HDI between 2000 and 2015
(mean_hdi_2015 - mean_hdi_2000)/mean_hdi_2000 *100
```




    12.89103088453119



### Research Question 3: Which country and region improved the most between 2000 and 2015? Which one improved the least?


```python
df_merged.groupby('eight_regions')['2015'].describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>count</th>
      <th>mean</th>
      <th>std</th>
      <th>min</th>
      <th>25%</th>
      <th>50%</th>
      <th>75%</th>
      <th>max</th>
    </tr>
    <tr>
      <th>eight_regions</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>africa_north</th>
      <td>5.0</td>
      <td>0.656200</td>
      <td>0.108946</td>
      <td>0.473</td>
      <td>0.64700</td>
      <td>0.6910</td>
      <td>0.72500</td>
      <td>0.745</td>
    </tr>
    <tr>
      <th>africa_sub_saharan</th>
      <td>40.0</td>
      <td>0.520250</td>
      <td>0.104644</td>
      <td>0.352</td>
      <td>0.44650</td>
      <td>0.4975</td>
      <td>0.57525</td>
      <td>0.782</td>
    </tr>
    <tr>
      <th>america_north</th>
      <td>15.0</td>
      <td>0.728133</td>
      <td>0.109164</td>
      <td>0.493</td>
      <td>0.66250</td>
      <td>0.7300</td>
      <td>0.77800</td>
      <td>0.920</td>
    </tr>
    <tr>
      <th>america_south</th>
      <td>11.0</td>
      <td>0.745545</td>
      <td>0.063062</td>
      <td>0.638</td>
      <td>0.71000</td>
      <td>0.7400</td>
      <td>0.78100</td>
      <td>0.847</td>
    </tr>
    <tr>
      <th>asia_west</th>
      <td>16.0</td>
      <td>0.665375</td>
      <td>0.111827</td>
      <td>0.482</td>
      <td>0.57375</td>
      <td>0.6565</td>
      <td>0.74800</td>
      <td>0.899</td>
    </tr>
    <tr>
      <th>east_asia_pacific</th>
      <td>19.0</td>
      <td>0.704053</td>
      <td>0.121926</td>
      <td>0.515</td>
      <td>0.62200</td>
      <td>0.7040</td>
      <td>0.73900</td>
      <td>0.939</td>
    </tr>
    <tr>
      <th>europe_east</th>
      <td>21.0</td>
      <td>0.806190</td>
      <td>0.053036</td>
      <td>0.699</td>
      <td>0.76700</td>
      <td>0.8020</td>
      <td>0.84500</td>
      <td>0.895</td>
    </tr>
    <tr>
      <th>europe_west</th>
      <td>20.0</td>
      <td>0.898050</td>
      <td>0.030579</td>
      <td>0.843</td>
      <td>0.87950</td>
      <td>0.8975</td>
      <td>0.92325</td>
      <td>0.949</td>
    </tr>
  </tbody>
</table>
</div>




```python
# we group the data by the eith regions and calculate descriptive statistics
df_merged.groupby('eight_regions')['change_2000_2015'].describe()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>count</th>
      <th>mean</th>
      <th>std</th>
      <th>min</th>
      <th>25%</th>
      <th>50%</th>
      <th>75%</th>
      <th>max</th>
    </tr>
    <tr>
      <th>eight_regions</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>africa_north</th>
      <td>5.0</td>
      <td>0.095600</td>
      <td>0.019844</td>
      <td>0.071</td>
      <td>0.07900</td>
      <td>0.1010</td>
      <td>0.11000</td>
      <td>0.117</td>
    </tr>
    <tr>
      <th>africa_sub_saharan</th>
      <td>40.0</td>
      <td>0.094925</td>
      <td>0.034899</td>
      <td>0.035</td>
      <td>0.06875</td>
      <td>0.0915</td>
      <td>0.11425</td>
      <td>0.166</td>
    </tr>
    <tr>
      <th>america_north</th>
      <td>15.0</td>
      <td>0.059933</td>
      <td>0.015845</td>
      <td>0.029</td>
      <td>0.05050</td>
      <td>0.0650</td>
      <td>0.06750</td>
      <td>0.094</td>
    </tr>
    <tr>
      <th>america_south</th>
      <td>11.0</td>
      <td>0.066636</td>
      <td>0.016621</td>
      <td>0.032</td>
      <td>0.05950</td>
      <td>0.0690</td>
      <td>0.07150</td>
      <td>0.095</td>
    </tr>
    <tr>
      <th>asia_west</th>
      <td>16.0</td>
      <td>0.077875</td>
      <td>0.046434</td>
      <td>-0.053</td>
      <td>0.04725</td>
      <td>0.0960</td>
      <td>0.10950</td>
      <td>0.130</td>
    </tr>
    <tr>
      <th>east_asia_pacific</th>
      <td>19.0</td>
      <td>0.085053</td>
      <td>0.036959</td>
      <td>0.034</td>
      <td>0.05600</td>
      <td>0.0810</td>
      <td>0.11500</td>
      <td>0.147</td>
    </tr>
    <tr>
      <th>europe_east</th>
      <td>21.0</td>
      <td>0.086048</td>
      <td>0.020314</td>
      <td>0.039</td>
      <td>0.07000</td>
      <td>0.0840</td>
      <td>0.10200</td>
      <td>0.117</td>
    </tr>
    <tr>
      <th>europe_west</th>
      <td>20.0</td>
      <td>0.054300</td>
      <td>0.013557</td>
      <td>0.023</td>
      <td>0.04550</td>
      <td>0.0575</td>
      <td>0.06525</td>
      <td>0.073</td>
    </tr>
  </tbody>
</table>
</div>




```python
# finding the country that improved the most its HDI between 2000 and 2015
df_merged.sort_values(by="change_2000_2015", ascending=False).head(1)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>geo</th>
      <th>2000</th>
      <th>2001</th>
      <th>2002</th>
      <th>2003</th>
      <th>2004</th>
      <th>2005</th>
      <th>2006</th>
      <th>2007</th>
      <th>2008</th>
      <th>...</th>
      <th>2009_co2</th>
      <th>2010_co2</th>
      <th>2011_co2</th>
      <th>2012_co2</th>
      <th>2013_co2</th>
      <th>2014_co2</th>
      <th>name</th>
      <th>eight_regions</th>
      <th>change_2000_2015</th>
      <th>improved</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>107</th>
      <td>Rwanda</td>
      <td>0.332</td>
      <td>0.343</td>
      <td>0.359</td>
      <td>0.371</td>
      <td>0.388</td>
      <td>0.404</td>
      <td>0.424</td>
      <td>0.438</td>
      <td>0.447</td>
      <td>...</td>
      <td>0.0577</td>
      <td>0.0576</td>
      <td>0.0631</td>
      <td>0.0683</td>
      <td>0.0726</td>
      <td>0.074</td>
      <td>Rwanda</td>
      <td>africa_sub_saharan</td>
      <td>0.166</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
<p>1 rows × 73 columns</p>
</div>




```python
# finding the country that performed the worst between 2000 and 2015
df_merged.sort_values(by="change_2000_2015", ascending=True).head(1)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>geo</th>
      <th>2000</th>
      <th>2001</th>
      <th>2002</th>
      <th>2003</th>
      <th>2004</th>
      <th>2005</th>
      <th>2006</th>
      <th>2007</th>
      <th>2008</th>
      <th>...</th>
      <th>2009_co2</th>
      <th>2010_co2</th>
      <th>2011_co2</th>
      <th>2012_co2</th>
      <th>2013_co2</th>
      <th>2014_co2</th>
      <th>name</th>
      <th>eight_regions</th>
      <th>change_2000_2015</th>
      <th>improved</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>126</th>
      <td>Syria</td>
      <td>0.589</td>
      <td>0.596</td>
      <td>0.599</td>
      <td>0.61</td>
      <td>0.624</td>
      <td>0.636</td>
      <td>0.644</td>
      <td>0.651</td>
      <td>0.648</td>
      <td>...</td>
      <td>2.99</td>
      <td>2.93</td>
      <td>2.73</td>
      <td>2.19</td>
      <td>1.84</td>
      <td>1.6</td>
      <td>Syria</td>
      <td>asia_west</td>
      <td>-0.053</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
<p>1 rows × 73 columns</p>
</div>



### Research Question 4: What features are associated with higher HDI (FDI, CO2 emissions, inequality)?


```python
# we build a scatter plot with the last year for which CO2 emissions data is available (2014) and the HDI for that same year
df_merged.plot(y='2014_co2', x='2014', kind='scatter');
```


![png](output_48_0.png)



```python
# we calculate the pearson correlation
corr_co2 = df_merged[['2014', '2014_co2']].copy()
corr_co2.corr()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>2014</th>
      <th>2014_co2</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2014</th>
      <td>1.000000</td>
      <td>0.643021</td>
    </tr>
    <tr>
      <th>2014_co2</th>
      <td>0.643021</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
# we build a scatter plot with Foreign Direct Investment data and the HDI index
df_merged.plot(y='2015_fdi', x='2015', kind='scatter');
```


![png](output_50_0.png)



```python
# we calculate the pearson correlation
corr_fdi  = df_merged[['2015', '2015_fdi']].copy()
corr_fdi.corr()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>2015</th>
      <th>2015_fdi</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2015</th>
      <td>1.000000</td>
      <td>0.041107</td>
    </tr>
    <tr>
      <th>2015_fdi</th>
      <td>0.041107</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
# we build a scatter plot with the gini index (inequality measurement) and the HDI index
df_merged.plot(y='2015_gini', x='2015', kind='scatter');
```


![png](output_52_0.png)


The GINI coefficient is widely used to measure inequality in an economy (income and wealth). It is expressed from 0 to 1 or in percentages from 0 (total equality) to 100 (total inequality). 


```python
# we calculate the pearson correlation
corr_gini  = df_merged[['2015', '2015_gini']].copy()
corr_gini.corr()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>2015</th>
      <th>2015_gini</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2015</th>
      <td>1.000000</td>
      <td>-0.488442</td>
    </tr>
    <tr>
      <th>2015_gini</th>
      <td>-0.488442</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>



<a id='conclusions'></a>
## Conclusions


1. According to the HDI, are people in more countries living in better conditions in 2015 than in 2000? in which proportion?
The Human Development Index has been widely used to describe, in an alternative way to the GDP per capita, the general state of a country's population. Evaluating the data available for the period between 2000 and 2015, we find that almost every country for which data is available (146 out of 147 or 99.3%) has improved the living conditions of its population.   

2. Is the average Human Development improving? by how much?
The Average Human Development index is improving, as found out visually with histograms. The mean HDI grew 12.89% between 2000 and 2015. It is important to note that this calculation excludes 20 countries for which no data was available, most of them considered developing countries.

3. Which country and region improved the most between 2000 and 2015? Which one improved the least?
Rwanda improved the most its HDI between 2000 and 2015, increasing in 0.166. As a region, North Africa improved the most in that period of time, with its mean HDI growing 9.6%. Syria was the only country where the conditions worsen in that period of time, which is explained by the ongoing civil war. As a region, Wester Europe improved the least (5.4%), arguably because of the already high levels of Human Development achieved, but being nonetheless a call for politicians to keep looking for ways to improve the quality of life of its citizens. 

4. What features are associated with higher HDI (FDI, CO2 emissions, inequality)?
High levels of CO2 emissions are strongly correlated to high levels of Human Development, which gives support to the criticism of the composition of the index. The current development path continues to be associated with emissions that cause global warming. The UNDP should indeed take note of this and incorporate good environmental policies and practices as part of the HDI composition. Foreign Direct Investment flows show no correlation to HDI, suggesting that investors are attracted by other factors beyond the general social conditions of its population. The HDI does not consider fdi for its composition and both are not correlated. The correlation between the GINI coeficient and the HDI is moderately strong. Countries with higher HDI tend to be more equal. 

<a id='sources'></a>
## Sources:

- University of Toronto (cover picture)
- https://stackoverflow.com/questions/32370281/how-to-include-image-or-picture-in-jupyter-notebook
- http://hdr.undp.org/en/content/measurement-inequality-human-development-%E2%80%93-review
- http://hdr.undp.org/en/content/review-hdi-critiques-and-potential-improvements
- https://pandas.pydata.org/pandas-docs/stable/missing_data.html
- https://www.gapminder.org/data/
- https://www.gapminder.org/data/geo/
- http://hdr.undp.org/en/faq-page/human-development-index-hdi


