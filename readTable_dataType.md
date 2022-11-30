### Reading Data

In the project about grazing behavior, having some visulizations will speed up analyses due to the condition that data source is not cleaned up and has lots of missing fields.
Meanwhile, the source of dataset has lots of Chinese characters, which should be in notice when making analyses.
<br></br>
Formatting dataset is mainly based on `pandas` package.
<br></br>
The basic importing:
```ruby
import pandas as pd # data processing, CSV file I/O (e.g. pd.read_csv)
```
```ruby
df = pd.read_csv("path", engine='python', encoding='utf-8-sig')
```
**Note the part of encoding** to deal with Chinese characters. Also, the csv should be saved as **CSV UTF-8 encoding** in EXCEL.\
The dataset is reading as a **DataFrame** now.
```ruby
#have a quick view of the dataset
df.head()

#view column names
df.columns.values.tolist()

#view first row of dataset
df.iloc[[0]].values.tolist()
```
For this dataset, the column names are all question numbers, which should be dropped later. \
What should be keeped as column names is the first row of data. Some interesting discussions related to data type are also summarized here. 
<br></br>
Meanwhile, hoping to get a quick view of the dataset, such as # rows and # columns:
```python
df.shape[0]  # Gives number of rows
df.shape[1]  # Gives number of columns
```
<br></br>

#### Changing column names

As discussed earlier, the first row of dataset should be the column name. \
[Reference](https://stackoverflow.com/questions/61736164/how-can-i-set-second-row-as-a-name-of-columns-in-dataframe) 
```ruby
df.drop([0])
```
drop is not working here, because the column names are not counting as parts of dataframe where have index. When dropping [0], the first row of data is dropped. 
```ruby
df.columns = df.iloc[0]
df = df.iloc[1:].reset_index(drop=True)
```
There is another [note](https://stackoverflow.com/questions/31593201/how-are-iloc-and-loc-different) about differences between `loc` and `iloc`, and is worth noticing. 

<br></br>

#### Getting index based on column's name

Because the dataset is a survey dataset, getting index based on column's name might be useful to sort data. \
[Reference](https://www.tutorialspoint.com/how-to-get-column-index-from-column-name-in-python-pandas)
```ruby
columns = df.columns
columns.get_loc("您家中养殖过什么？")
```
If we want to slice the dataset, the following codes are all work. But one returns **DataFrame** and another returns **Series**.
```ruby
df[['您家中养殖过什么？']] #dataframe
df['您家中养殖过什么？']  #series
```
To better understand differences between `[]` and `[[]]`, there is also a [note](https://stackoverflow.com/questions/64955439/difference-between-and-in-python). \
In short, `[]` returns **series**, `[[]]` returns **dataframe**. Analygous to **list** and **matrix** in R. 
