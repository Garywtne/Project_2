<h1>Maxwell Acha, Gary Whitney, David Griliopoulos</h1>
<h1>Birmingham University Data Analytics Bootcamp</h1>
<h1>Project 2 Report:</h1>

<h2>Abstract</h2>

Our team decided that we would compile different data sources on the countries of the world, so that a user could perhaps use this to investigate any statistical connections. We decided to use different countries’ ‘World Happiness Report Rankings’ and 'Population Density'. 

This would enable us to analyse the data to find any correlation between the two variables. It would it be interesting to find whether population density has a negative or positive impact on people's happiness, or if it has no connection at all.

<h2>Extract</h2>

We extracted the data in different formats from two different sources:
A .csv regarding the ‘happiness score’ of various countries
https://www.kaggle.com/datasets/mathurinache/world-happiness-report?select=2022.csv
An html table of population density https://en.wikipedia.org/wiki/List_of_countries_and_dependencies_by_population_density 
These were both converted into Pandas DataFrames by pd.read_csv and pd.read_html respectively. We had to search through the website to find the correct table.

<h2>Transform</h2>
There was substantial cleaning and reduction that needed to be done to these two dataframes in order to make them fit together.

Both dataframes have a ‘Country’ column, however in both dataframes some countries were followed by an asterix (‘*’). 

![happiness1](https://user-images.githubusercontent.com/85430216/170824574-33dbe24d-cd9b-4bf1-8861-21386bf8281c.PNG)

![pop_dense1](https://user-images.githubusercontent.com/85430216/170824576-274707b5-6353-4325-895b-133577976f60.PNG)

This, and any following text (some countries had an extra note in brackets afterwards), was removed by reading the countries, stripping off the unnecessary characters by partitioning on them, and then writing that clean country name to a new list. This list was then concatenated with the relevant numerical data from the original dataframe to make a new clean dataframes; columns were then renamed to match the names in the SQL tables.

The Population Density dataframe has more rows than the Happiness dataframe, as it also contains dependent territories (E.g “Svalbard and Jan Mayen (Norway)”) that do not have a separate value in the happiness survey. Users would be able to inner or outer join to keep or remove these anomalies as they see fit. 

The happiness dataframe has many more rows than was necessary for our data, all of which were dropped, leaving only ‘Country’ and ‘Rank’, and Rank was also renamed to match the SQL table. 

We dropped the Happiness score itself as it was recorded as a formatted string (e.g. “7.587”) not an integer. We could have converted the string to an integer, but we felt that the rank was more of an important measure because it gave more context than the happiness score alone.

<h2>Load</h2>
We created a database in PostgreSQL to contain the data, called ‘happy_density’, and inside created two tables that with columns that matched the dataframes:
"create table happiness( country text primary key, happiness_rank int );
create table pop_dense( country text primary key, p_dense int );"
We then loaded the dataframes into PostgreSQL using sqlalchemy and the dataframe.to_sql() function.

Using the code 
"select happiness.country, happiness.happiness_rank, pop_dense.p_dense 
from happiness 
inner JOIN pop_dense 
ON happiness.country = pop_dense.country;" 
we then built the relational database with the two tables by joining on the name of the country, returning 141 matched countries. This means there are a handful of countries that did not match, due to alternative naming conventions between the two data sources. Given more time, we would manually fix these errors, however 141 matches is enough to perform statistical analyses.

![PosgresSQL](https://user-images.githubusercontent.com/85430216/170824668-4c2ee364-c55e-4457-b964-0ec53158aad1.PNG)


We elected to use a config.py file to store passwords. This file is ignored by the github repo using gitignore to keep our own personal data private.

![Config](https://user-images.githubusercontent.com/85430216/170824813-30063d85-e3f8-4859-88b7-469c1e1ab8e1.PNG)

![Gitignore](https://user-images.githubusercontent.com/85430216/170824819-22b3b95f-5d73-4e0d-ad59-67d119271ff8.PNG)
