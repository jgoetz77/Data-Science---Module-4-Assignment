# Data-Science---Module-4-Assignment
Module 4 Assignment
In this assignment, you will be given four .txt files containing data from the United Kingdom (UK).  

`UK_profession.txt` : contains profession data of individuals from UK, including `id`, `governement_work`, `occupation`, and `hours_per_week`.  

`UK_demographics.txt` : contains demographic data of individulas from UK, including `id`, `age`, `gender`, `education`, `marital_status`, `relationship`, and `ethnicity`.  

`UK_gains_losses.txt` : contains financial data of individuals from UK, including `id`, `capital_gain`, and `capital_loss`.  

`UK_income.txt` : contains income data of individuals from UK, in wide format.

The objective of this assignment is to manipulate and clean the UK data into a single data frame called `UK_data` before using `bind_rows()` to combine it with the larger `income` data set. The steps needed to complete this task are outlined below. After each step, you should use some of the functions you've learned in modules 2 and 3 to take a closer look at your data frame objects and ensure that they look the way you expect them to.

Please note that you will be asked to store the results of each step in unqiuely named objects, as described in the instructions below. This is to facilitate autograding, and is not necessarily a best practice!

Question 1:
-  Load the {dplyr} and {tidyr} packages.  
- Import the data contained in the files `UK_profession.txt`, `UK_demographics.txt`, `UK_gains_losses.txt`, and `UK_income.txt` into R, and store the data frames in objects called `UK_profession`, `UK_demographics`, `UK_gains_losses`, and `UK_income`, respectively.  
- Use functions like `head()` and `glimpse()` to familiarize yourself with the data.  

*Assignment Code:*  
```{r}
# Load libraries
library(dplyr)
library(tidyr)
# Import the data
UK_profession<-read.delim("UK_profession.txt")
UK_demographics<-read.delim("UK_demographics.txt")
UK_gains_losses<-read.delim("UK_gains_losses.txt")
UK_income<-read.delim("UK_income.txt")
# Familiarize yourself with the data
head(UK_profession)
glimpse(UK_profession)
head(UK_demographics)
glimpse(UK_demographics)
head(UK_gains_losses)
glimpse(UK_gains_losses)
head(UK_income)
glimpse(UK_income)

Question 2: Join `UK_profession` and `UK_demographics` into a data frame called `UK_data_step1`.
UK_profession:
Observations: 102
Variables: 4
$ id               <int> 37, 45, 69, 81, 18,…
$ governement_work <fct> Private, Private, N…
$ occupation       <fct> Other service, Adm …
$ hours_per_week   <int> 7, 9, 12, 12, 20, 2…

UK_demographics:
Observations: 102
Variables: 7
$ id             <int> 59, 18, 34, 42, 45, 9…
$ age            <int> 17, 18, 19, 19, 20, 2…
$ gender         <fct> Male, Male, Male, Mal…
$ education      <fct> 11th, 11th, Some to c…
$ marital_status <fct> Never married, Never …
$ relationship   <fct> Own child, Own child,…
$ ethnicity      <fct> White, White, White, …

UK_data_step1<-left_join(UK_profession, UK_demographics)

# You can't just stick these data frames together by row because each dataset has a different order of individuals. You need to sort the datasets so that the data for the subjects/rows lines up across the datasets
# left_join(x, y) : joins data frames by returning all rows from x and all columns from x and y based on shared variables. x is an argument from one dataframe and y is an argument from the other dataframe. This says take all of the rows from x and the appropriate rows from y and joint them based on shared variables. So this function would match values from the ID columns (i.e, participant ID 769) across the two datasets and then sort and combine the rows across the datasets based on the matching values (i.e, participant ID 769)
nonUS_data <- left_join(nonUS_profession, nonUS_demographics)
•	nonUS_data <- left_join(nonUS_profession, nonUS_demographics) # creates the nonUS_data object and stores joined data from the nonUS_profession and nonUS_demographics datasets
Joining, by = "id"

Question 3:  
*Instructions:*  
- Transform the `UK_income` from wide to long format, with columns called `id`, `country`, and `income`. Store this new data frame in an object called `UK_income_long_step1`.

Observations: 102
Variables: 3
$ id       <int> 1, 2, 3, 4, 5, 6, 7, 8, 9, …
$ England  <fct> <=50K, <=50K, >50K, <=50K, …
$ Scotland <fct> NA, NA, NA, NA, NA, <=50K, …

UK_income_long_step1<-gather(UK_income, key=country, value=income, -id)

# ---- Step Two: Transform nonUS_income from wide to long ----
# What does this data set look like? This data is in "wide" format, where each country has its own column, and each individual has their own row. The cells contain information about income. You need to convert it to "long" format; you want a column called id, a column called country, and a column called income
head(nonUS_income, 1)
dim(nonUS_income)
> dim(nonUS_income) # returns number of rows and then number of columns
[1] 2706   39

# gather() : collapses multiple columns into key-value pairs, taking a wide format dataset and converting it to a long format dataset. 
nonUS_income_long <- gather(nonUS_income, key = country, value = income, -id) # creates the object nonUS_income_long, then gathers the data from the wide format nonUS_income dataset by key = country (the commonality among the columns that we want to collapse, e,g, country), value = income (the value that is in each of the cells under each of the country columns in the wide format dataset), and -id (excluding the data in the id column. id is not considered a country when combining the data but the id column is kept by itself)
> nonUS_income_long <- gather(nonUS_income, key = country, value = income, -id)
Warning message: attributes are not identical across measure variables; they will be dropped # This warning message is common and not to worry in this context

# What does the transformed data set look like?
head(nonUS_income_long)
> head(nonUS_income_long) # This returns the values for each country for each participant regardless of the participant’s country of origin. So, I’ll need to remove rows for which the income value is <NA>
  id  country income
1  1 Cambodia   <NA>
2  2 Cambodia   <NA>
3  3 Cambodia   <NA>
4  4 Cambodia   <NA>
5  5 Cambodia   <NA>
6  6 Cambodia   <NA>

dim(nonUS_income_long)
> dim(nonUS_income_long)
[1] 102828      3

Question 4
*Instructions:*  
- Remove rows in `UK_income_long_step1` with missing values. Store this new data frame in an object called `UK_income_long_step2`.

UK_income_long_step2<-drop_na(UK_income_long_step1)

# ---- Step Three: Remove the rows with income equal to NA ----
# Right now the data has every combination of id and country. You want to discard these "extra" rows that have been added. They can be identified by income having a value of NA

# drop_na() :  drop rows containing missing values
nonUS_income_long <- drop_na(nonUS_income_long) #permanently replaces the nonUS_income_long object with one containing data from the nonUS_income_long dataset in which the rows containing <NA> are removed. Just running drop_na(nonUS_income_long) would only show us what the dataset would look like.

# What does the transformed data set look like?
head(nonUS_income_long)
> head(nonUS_income_long)
     id  country income
25   25 Cambodia   >50K
148 148 Cambodia   >50K
346 346 Cambodia  <=50K
403 403 Cambodia  <=50K
466 466 Cambodia   >50K
680 680 Cambodia  <=50K

dim(nonUS_income_long)
> dim(nonUS_income_long)
[1] 2706    3 # We see that the original number of participants/rows is the same (e.g, 2706) and the number of columns has been compressed into 3 (e.g., id, country, income)


Question 5
*Instructions:*  
- Join the `UK_income_long_step2` data to `UK_data_step1`. Store this new data frame in an object called `UK_data_step2`.  
UK_data_step1
UK_profession:
Observations: 102
Variables: 4
$ id               <int> 37, 45, 69, 81, 18,…
$ governement_work <fct> Private, Private, N…
$ occupation       <fct> Other service, Adm …
$ hours_per_week   <int> 7, 9, 12, 12, 20, 2…

UK_demographics:
Observations: 102
Variables: 7
$ id             <int> 59, 18, 34, 42, 45, 9…
$ age            <int> 17, 18, 19, 19, 20, 2…
$ gender         <fct> Male, Male, Male, Mal…
$ education      <fct> 11th, 11th, Some to c…
$ marital_status <fct> Never married, Never …
$ relationship   <fct> Own child, Own child,…
$ ethnicity      <fct> White, White, White, …

UK_income_long_step2 has id, income, country
Observations: 102
Variables: 3
$ id       <int> 1, 2, 3, 4, 5, 6, 7, 8, 9, …
$ Country <fct> England, Scotland, …
$ Income <fct> <=50K, <=50K, >50K, <=50K, …

UK_data_step2<-left_join(UK_data_step1, UK__income_long_step2)

- Join the `UK_gains_losses` data to `UK_data_step2`. Store this new data frame in an object called `UK_data_step3`.
UK_gains_losses
Observations: 102
Variables: 3
$ id           <int> 1, 2, 3, 4, 5, 6, 7, 8,…
$ capital_gain <int> 0, 0, 0, 0, 0, 0, 0, 0,…
$ capital_loss <int> 0, 0, 1902, 0, 0, 0, 0,…

UK_data_step3<-left_join(UK_data_step2, UK_gains_losses)

Question 6
*Instructions:*  
- Drop the `id` column from `UK_data_step3`. Store this new data frame in an object called `UK_data_step4`.

UK_data_step4<-UK_data_step3 %>% select(-id)

# ---- Step Seven: Drop id column ----
# You no longer need identification numbers for each individual
income <- income %>%
  select(-id) # Takes the data in the income object, puts it in a pipeline, and selects the id variable for removal. Then puts that new dataframe into a new object named income

Question 7
*Instructions:*  
- Correct the spelling of the `governement_work` column in `UK_data_step4`. Store this new data frame in an object called `UK_data_step5`.

UK_data_step5<-UK_data_step4 %>%
  rename(government_work = governement_work)

# ---- Step Eight: Correct the spelling of the column "governement_work" ----
# You can see that one of the variable names is spelled incorrectly
names(income) # Returns the column names of the income dataframe
> names(income)
 [1] "age"              "governement_work" "education"        "marital_status"  
 [5] "occupation"       "relationship"     "ethnicity"        "gender"          
 [9] "capital_gain"     "capital_loss"     "hours_per_week"   "income"          
[13] "country"  

# rename() : modify column names in a data frame using the format new_name = old_name
income <- income %>%
  rename(government_work = governement_work)

Question 8
*Instructions:*  
- Import the `income.csv` dataset and store it in an object called `income_original`.  
income_original<-read.csv("income.csv")

- Bind `UK_data_step5` with `income_original`.  Store this new data frame in an object called `income_merged`.
income_merged<-bind_rows(UK_data_step5, income_original)

# ---- Step Six: Combine nonUS_data with US_data ----
# Now US_data and nonUS_data are ready to be combined. Now check the rows in each to see how many rows the final dataset would have
nrow(US_data) # 29170 rows of US data
nrow(nonUS_data) # 2706 rows of non US data

# The US_data and nonUS_data dataframes have variables (i.e, columns) in common and so I need to stack, not tack one to the end of the other, the dataframes and match the shared variables. The bind_rows() function stacks rows, automatically combining like columns by order or columns of the first dataframe in the function, on top of each other whereas the left_join() function joins the lefthand side of one dataframe to the righthand side of the other.

# bind_rows() : bind multiple data frames together by rows
income <- bind_rows(US_data, nonUS_data) # Creates an object called income containing the stacked US_data and nonUS_data dataframes
> income <- bind_rows(US_data, nonUS_data)

Question 9
*Instructions:*  
- Convert all columns of `income_merged` containing character vectors to factors. Store this new data frame in an object called `income`.
income<-income_merged %>%
  mutate_if(is.character, as.factor)

Observations: 31,978
Variables: 13
$ government_work <chr> "Private", …
$ occupation      <chr> "Other serv…
$ hours_per_week  <int> 7, 9, 12, 1…
$ age             <int> 68, 20, 23,…
$ gender          <fct> Female, Mal…
$ education       <chr> "HS to grad…
$ marital_status  <chr> "Widowed", …
$ relationship    <fct> Unmarried, …
$ ethnicity       <chr> "White", "W…
$ country         <chr> "England", …
$ income          <chr> "<=50K", "<…
$ capital_gain    <int> 0, 0, 0, 0,…
$ capital_loss    <int> 0, 0, 0, 0,…

# You can do one at a time
income <- income %>%
  mutate(government_work = as.factor(government_work)) # Takes the income dataframe, puts it in a pipeline, mutates the dataframe by changing the government_work variable class from character back to factor. Then puts that dataframe into a new object named income.

# Or do all of them at once
# mutate_if() : apply a transformation to a variable if a condition is met
income <- income %>%
  mutate_if(is.character, as.factor) # Takes the income dataframe, puts it in a pipeline, mutates (mutate_if) the dataframe by changing all the character class variables (is.character) to factor class variables (as.factor). Then puts that new dataframe into a new object called income.
![image](https://user-images.githubusercontent.com/59670247/208267158-9b26ed63-3fbb-4af6-b0de-9e0d40218b0a.png)
