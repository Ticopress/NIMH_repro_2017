---
title: "Pandas"
teaching: 30
exercises: 30
questions:
- ""
objectives:
- ""
- ""
keypoints:
- ""
- ""
- ""
- ""
- ""
---


~~~
import pandas as pd
df_pheno = pd.read_csv('https://raw.githubusercontent.com/OpenNeuroLab/metasearch/master/docs/data/phenotype_mri.csv')
~~~
{: .python}

~~~
%save load_pheno.py N # enter the number of the previous command
~~~
{: .python}

~~~
The following commands were written to file `load_pheno_data.py`:
import pandas as pd
df_pheno = pd.read_csv('https://raw.githubusercontent.com/OpenNeuroLab/metasearch/master/docs/data/phenotype_mri.csv')
~~~
{: .output}

We can confirm that the script ran successfully by checking our environment: 

~~~
Variable   Type         Data/Info
---------------------------------
df_pheno   DataFrame                    project s<...>[11045 rows x 20 columns]
pd         module       <module 'pandas' from '/u<...>ages/pandas/__init__.py'>
~~~
{: .output}



We can get a quick summary of the data object using one of its data descriptors, `dtypes`:

~~~
df_pheno.dtypes
~~~
{: .python}

~~~
output
~~~
{: .output}

We can also use the method `describe()`:

~~~
df_pheno.describe()
~~~
{: .python}

~~~
output
~~~
{: .output}


Three import components of the dataframe are the raw values, the column labels,
and the row labels. This can be accessed using the following attributes
respectively:

~~~
df_pheno.values
df_pheno.columns
df_pheno.index
~~~
{: .python}

~~~
output
~~~
{: .output}

To obtain subsets of these values we will look at four approaches. Integer
based indexing, label based indexing, attribute based indexing, and dictionary
based indexing. 

Using the iloc  for the implicit indices of the dataframe
include referring to the columns (as attributes of the dataframe), loc for the
explicit indices of the dataframe, and .


The columns can be accessed and used conveniently through the attributes of the dataframe:

~~~
df_pheno.age
df_pheno.age > 50 # returns a pandas series object containing boolean values
~~~
{: .python}

~~~
output
~~~
{: .output}


An equivalent syntax is to index the columns in the same way as a dictionary.
This takes more typing and impedes autocompletion:
~~~
df_pheno['age']
df_pheno['age'] > 50
~~~
{: .python}

~~~
output
~~~
{: .output}

This alternative syntax has the advantage of not causing subtle bugs occurring
due to the fact that an attribute may have the same name as a method. This is
should be used for assigning values to new columns.

~~~
df_pheno['age_in_ten_years'] = df_pheno.age + 10
~~~
{: .python}

~~~
output
~~~
{: .output}



## Using the loc indexing attribute
Like data-descriptors, loc is not a method so does
not use parentheses; however, it is different in that uses square brackets so
that we can index into a dataframe as we did with lists and strings.
Specifically the `loc` indexing attribute allows us to access the explicitly
defined labels for all columns and rows of the dataframe.


* Selecting individual elements

~~~
df_pheno.loc[8,'project']
~~~
{: .python}

~~~
output
~~~
{: .output}


* Specifying ranges 

~~~
df_pheno.loc[0:8, 'project':'occupation']
~~~
{: .python}

~~~
output
~~~
{: .output}


* Selecting multiple rows using a list

~~~
df_pheno.loc[[1,8,35],'project':'occupation']
~~~
{: .python}

~~~
output
~~~
{: .output}


* Using masking to select rows
Masking for a dataframe might look something like:
    
    df_pheno.loc[[True,False,True,True,False],'age']

This would be laborious for more than a few rows. We instead use what is called
masking to create a boolean array for indexing the values

~~~
over_50s = df_pheno.loc[df_pheno.age>50, 'age'] # type changed
~~~
{: .python}

~~~
output
~~~
{: .output}

~~~
over_50s.count()
~~~
{: .python}

~~~
output
~~~
{: .output}


~~~
over_50s.loc[0:1000]
~~~
{: .python}

~~~
output
~~~
{: .output}


## Using the iloc indexing attribute

For those familiar with numpy naming conventions the iloc indexing attribute
will seem familiar. It allows us to access the implicit indices of the
dataframe, as in the indices that would expect certain values to have based on
their location in the dataframe. Once again we can specify rows and columns:

~~~
df_pheno.iloc[:5, :5]
~~~
{: .python}

~~~
output
~~~
{: .output}



## Creating unique IDs for each participant.

The following demonstrates split-apply-combine and joins:

~~~
df_id = df_pheno.groupby(df_pheno['participant_id'], as_index = False).count()
df_id = df_id.assign(id = range(1, 1 + len(df_id))).loc[:,['id','participant_id']]
df_id
df_pheno.merge(df_id, how = 'left', on = 'participant_id')
~~~
{: .python}



> ## Change me to a pandas exercise
>
> You want to select a random character from a string:
> ~~~
> bases = 'ACTTGCTTGAC'
> ~~~
>
> 1. What [standard library][stdlib] would you most expect to help?
> 2. Which function would you select from that library? Are there alternatives?
{: .challenge}

> ## Change me to a pandas exercise
>
> When a colleague of yours types `help(math)`,
> Python reports an error:
>
> ~~~
> NameError: name 'math' is not defined
> ~~~
> {: .error}
>
> What has your colleague forgotten to do?
{: .challenge}

> ## Change me to a pandas exercise
>
> 1. Fill in the blanks so that the program below prints `90.0`.
> 2. Rewrite the program so that it uses `import` *without* `as`.
> 3. Which form do you find easier to read?
>
> ~~~
> import math as m
> angle = ___.degrees(___.pi / 2)
> print(___)
> ~~~
> {: .python}
{: .challenge}


> ## Encapsulating Data Analysis
> 
> Assume that the following code has been executed:
> 
> ~~~ 
> import pandas
> 
> df = pandas.read_csv('gapminder_gdp_asia.csv', index_col=0)
> japan = df.ix['Japan']
> ~~~
> {: .python}
> 
> 1. Complete the statements below to obtain the average GDP for Japan
>    across the years reported for the 1980s.
> 
> ~~~ 
> year = 1983
> gdp_decade = 'gdpPercap_' + str(year // ____)
> avg = (japan.ix[gdp_decade + ___] + japan.ix[gdp_decade + ___]) / 2
> 
> ~~~
> {: .python}
> 
> 2. Abstract the code above into a single function.
> 
> ~~~
> def avg_gdp_in_decade(country, continent, year):
>     df = pd.read_csv('gapminder_gdp_'+___+'.csv',delimiter=',',index_col=0)
>     ____
>     ____
>     ____
>     return avg
> ~~~
> {: .python}
> 
> 3. How would you generalize this function
>    if you did not know beforehand which specific years occurred as columns in the data?
>    For instance, what if we also had data from years ending in 1 and 9 for each decade?
>    (Hint: use the columns to filter out the ones that correspond to the decade,
>    instead of enumerating them in the code.) 
> 
> > ## Solution
> > 
> > 1. 
> > 
> > ~~~ 
> > year = 1983
> > gdp_decade = 'gdpPercap_' + str(year // 10)
> > avg = (japan.ix[gdp_decade + '2'] + japan.ix[gdp_decade + '7']) / 2
> > ~~~
> > {: .python}
> > 
> > 2.
> > 
> > ~~~
> > def avg_gdp_in_decade(country, continent, year):
> >     df = pd.read_csv('gapminder_gdp_' + continent + '.csv', index_col=0)
> >     c = df.ix[country]
> >     gdp_decade = 'gdpPercap_' + str(year // 10)
> >     avg = (c.ix[gdp_decade + '2'] + c.ix[gdp_decade + '7'])/2
> >     return avg
> > ~~~
> > {: .python}
> > 
> > 3. We need to loop over the reported years
> >    to obtain the average for the relevant ones in the data.
> > 
> > ~~~
> > def avg_gdp_in_decade(country, continent, year):
> >     df = pd.read_csv('gapminder_gdp_' + continent + '.csv', index_col=0)
> >     c = df.ix[country] 
> >     gdp_decade = 'gdpPercap_' + str(year // 10)
> >     total = 0.0
> >     num_years = 0
> >     for yr_header in c.index: # c's index contains reported years
> >         if yr_header.startswith(gdp_decade):
> >             total = total + c.ix[yr_header]
> >             num_years = num_years + 1
> >     return total/num_years
> > ~~~
> > {: .python}
> {: .solution}
{: .challenge}