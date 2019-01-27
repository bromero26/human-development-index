# human-development-index
The Human Development Index (HDI) is a widely-used indicator for the welfare of a society. It is produced by the United Nations Development Program (UNDP) on a yearly basis since 1990 and is an alternative to the traditional economic growth approach (GDP) to development. The index summarizes the current situation of a country's achievements in education (years of schooling), health (live expectancy) and overall economic situation (gross national income per capita). By exploring the change in the HDI we could have an idea whether the world's population is in an overall better or worse situation as it was 15 years ago.

HDI was initially proposed by the Pakistani economist Mahbub ul Haq and UNDP has taken ownership of the indicator's production and the publication of its results. The official site for the HDI can be accesed <a href="http://hdr.undp.org/en/faq-page/human-development-index-hdi">here</a> and for this project the data was retrieved from <a href="https://www.gapminder.org/data/">Gapminder</a>, a repository of economic and societal data at the country level.

A [criticism](http://hdr.undp.org/sites/default/files/hdrp_2010_35.pdf) to the HDI is that it does not take into account a country's economic inequality and that it does not relflect a country's economic insertion to the world. Furthermore, it doesn't take into account ecological footprint, which unfortunaltely seems to be positively related to a country's higher HDI. To test for these shortcomings, this project looks for relationships between the HDI and further Gapminder data about foreign direct investment (FDI) flows, CO2 emissions and inequality, as represented by a country's <a href="https://en.wikipedia.org/wiki/Gini_coefficient">GINI coefficient</a>. The data was retrieved from the Gapminder repositories in August 2018.

By using Exploratory Data Analysis with the pandas, numpy and matplotlib python libraries, this project will try to respond to the following questions:

1. According to the HDI, are people in more countries living in better conditions in 2015 than in 2000? in which proportion?
2. Is the average Human Development improving? by how much?
3. Which country and region improved the most between 2000 and 2015? Which one improved the least?
4. What features are associated with higher HDI (FDI, CO2 emissions, inequality)?
