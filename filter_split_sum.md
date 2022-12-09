### Filter Dataset

In this part, I would like to filter the dataset with condition. Analygous to `filter()` inside `dplyr` in R. \
When filtering out NaN, two methods could be used. One is `.isnull()`, based on `pandas`, and the [Documentation](https://pandas.pydata.org/docs/reference/api/pandas.isnull.html).
```python
pd.DataFrame(yangzhi['您家中养殖过什么？'].isnull()) 
```
The result will look like:
||您家中养殖过什么？|
|---|---|
|0| False|
|1| False|
|2| True|

<sub>The Data is not real data. Just show how the result will be, and what's format of the dataset</sub>

Another method is using `.loc()`:
```python
yangzhi.loc[pd.isnull(yangzhi['您家中养殖过什么？'])] #like fiter in dplyr

df.loc[pd.isnull(df['您家中养殖过什么？'])] #apply to the overall df, yangzhi is only part of df
```
And the result will be like using `filter` inside `dplyr`:
||您家中养殖过什么？|
|---|---|
|85 |NaN|
|132|NaN|
|204|NaN|

<br></br>

### Split string inside columns

Because the dataset is from survey, this column contains multiple answers.
||您家中养殖过什么？|
|---|---|
|0	|黄牛,马,羊,猪|
|1	|黄牛,马,羊,猪|
|2	|马,羊,猪|
|3	|黄牛,羊,猪|
|4	|黄牛,马,羊,猪|

So the answer, string, needs to be splitted by comma. \
`.split()` could be used for single string, but for overall dataframe, `str.split()` should be applied.
```python
yangzhi['您家中养殖过什么？'][0].split(',')

['黄牛', '马', '羊', '猪'] #result
```
```python
yangzhi['您家中养殖过什么？'].str.split(',')
```
```
0      [黄牛, 马, 羊, 猪]
1      [黄牛, 马, 羊, 猪]
2          [马, 羊, 猪]
3         [黄牛, 羊, 猪]
4      [黄牛, 马, 羊, 猪]
           ...      
325           [马, 猪]
326    [黄牛, 马, 羊, 猪]
327          [黄牛, 猪]
328       [黄牛, 马, 猪]
329              NaN
Name: 您家中养殖过什么？, Length: 330, dtype: object
```
```python
yangzhi['您家中养殖过什么？'].str.split(',',expand=True)
```
|0|1|2|3|4|
|---|---|---|---|----|
|0	|黄牛	|马	|羊	|猪	|None|
|1	|黄牛|	马|	羊|	猪|	None|
|2	|马	|羊	|猪|	None|	None|

**Note the difference of synatx here**. \
Because the values are splitted by comma, the results do not have order. In other words, reults like '马' appear at the first column, and the second column.
Aiming to summarize the results, a function inside loop is writted, and will be clarified at later part. 
However, the code works but there might contain some hardcode. \
Besides, yangzhi is a dataframe which selects only one column from the original dataset. Hoping to get track of data, and merge table easier, 
'Response ID' is also selected out.
```python
yangzhi_s = df[['Response ID','您家中养殖过什么？']]
yangzhi_s.head()
```

||Response ID|	您家中养殖过什么？|
|-----|------|-------|
|0|	R_2PnBjYRYr|	黄牛,马,羊,猪|
|1|	R_24Jq3nLL4	|黄牛,马,羊,猪|
|2|	R_6J5UvdJAt	|马,羊,猪|

From here, not only the column '您家中养殖过什么？' need to be splitted, but also the column of 'Response ID' need to be maintained. \
The method is referenced from [here](https://thats-it-code.com/pandas/how-to-split-one-column-to-multiple-columns/).
```python
yangzhi_s = pd.concat([yangzhi_s['Response ID'], yangzhi_s['您家中养殖过什么？'].str.split(',', expand=True)], axis=1) 
#note axis to keep column name of Response ID
#if is axis=0, the name of response ID will disappear

yangzhi_s.head()
```
|   |Response ID|	0|	1|	2|	3|	4|
|-----|-----|------|---|-----|-----|---|
|0|R_2PnAFcBjYZAYRYr|	黄牛	|马	|羊	|猪|	None|
|1|R_24GIBSuzJq3nLL4|	黄牛|	马|	羊	|猪	|None|
|2|R_6J5UVsXzk9vdJAt|	马|	羊|	猪|	None|	None|

Parameter, `axis`, needs to be **noticed carefully**. \
When the column inclues ${\color{red}NaN}$ **(mix NaN with strings)**, there is a **trick** about dealing with NaN. A strightforward way of solving it is referenced [from](https://stackoverflow.com/questions/69354795/how-to-skip-nan-values-when-splitting-up-a-column).

|ResponseID|退耕还林（生态补偿）|
|---|---|
|R_3oNPXl67Z|260,NA,3,780|
|R_3HIC0zrA6|	NaN|

<sub>An example about how the dataframe will look like. [Reference](https://stackoverflow.com/questions/11509830/how-to-add-color-to-githubs-readme-md-file)
about adding texts with color, based on GitHub supporting LaTeX/Mathematics.</sub>
```python
pol1['退耕还林（生态补偿）'].str.split(",", n=4, expand=True)
```
The part,`n=number`need to be specified. Based on [document](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Series.str.split.html), `n`
infers to **Limit number of splits in output**. In this case, one column should be splitted into four columns, so n=4. 

<br></br>

### Summarizing splitted data

The goal is to summarize how many same answers happened among five columns. \
Another dataframe, test, is selected from the original dataframe to test methods. The first applied method is:
```python
test = yangzhi_s.loc[[0, 1, 2, 3, 4, 5, 6]]
test
```
||Response ID|	0	|1|	2|	3|	4|
|-----|-----|------|---|-----|-----|---|
|0	|R_2PnYZAYRYr|	黄牛|	马	|羊|	猪	|None|
|1	|R_24GIq3nLL4	|黄牛	|马	|羊	|猪|	None|
|2	|R_6J5UV9vdJA|	马	|羊	|猪|	None|	None|
|3	|R_2fiRwwxfs7|	黄牛|羊|	猪|	None|	None|
|4	|R_3LhiqCpFNw|	黄牛|马|	羊	|猪|	None|
|5	|R_tSLLOuGCSR|	黄牛|马	|羊	|猪	|None|
|6	|R_aaHMHC0KpH|	黄牛|	羊	|猪	|None|	None|
```python
test.where(test == "马")
```
||Response ID|	0	|1|	2|	3|	4|
|-----|-----|------|---|-----|-----|---|
|0	|NaN|	NaN|	马	|NaN|	NaN	|NaN|
|1	|NaN	|NaN	|马	|NaN	|NaN|	NaN|
|2	|NaN|	马	|NaN	|NaN|	NaN|	NaN|
|3	|NaN|	NaN|NaN|	NaN|	NaN|	NaN|
|4	|NaN|	NaN|马|	NaN	|NaN|	NaN|
|5	|NaN|	NaN|马	|NaN	|NaN	|NaN|
|6	|NaN|	NaN|	NaN	|NaN|NaN|	NaN|
```python
len(test.where(test == "马"))

7
```
Based on `.where()`, there are 5 anwers of '马'. However, just using `len()` is not correct. `len()` is counting the length of the dataframe, the number of rows. Therefore, another method, writing conditions is used here. \
The logic of writing if and else is similar with `ifelse()` in R. There is a [note](https://datatofish.com/if-condition-in-pandas-dataframe/) about applying if condition based on `pandas` in python. \
In order to select the columns easier, the columns are renamed.
```python
test.columns = ['ResponseID', 'type1', 'type2', 'type3', 'type4', 'type5']
```
A function inside loop is written:
```python
def change_val(var, dframe):
    dframe.loc[(dframe.type1 == var) | 
         (dframe.type2 == var) | 
         (dframe.type3 == var) | 
         (dframe.type4 == var) | 
         (dframe.type5 == var), var] = 1
    return dframe

types = ['黄牛', '牦牛', '马', '羊', '猪']
for t in types:
    d_frame = test #define global variable
    change_val(t, d_frame)
    num = d_frame[[t]].count().to_list()
    print("finish {}, the number is {}".format(t, num))
```
```
finish 黄牛, the number is [6]
finish 牦牛, the number is [0]
finish 马, the number is [5]
finish 羊, the number is [7]
finish 猪, the number is [7]
```
And the results based on test are consistent with what is included in table. The test table now looks like:
||Response ID|	type1	|type2|type3|type4|type5|黄牛|牦牛|马|	羊|猪|
|-----|-----|------|---|-----|-----|---|---|---|---|---|---|
|0	|R_2PnYZAYRYr|	黄牛|	马	|羊|	猪	|None|1.0|NaN|1.0|1.0|	1.0|
|1	|R_24GIq3nLL4	|黄牛	|马	|羊	|猪|	None|1.0|NaN|1.0|1.0|	1.0|
|2	|R_6J5UV9vdJA|	马	|羊	|猪|	None|	None|NaN|NaN|1.0|1.0|	1.0|
|3	|R_2fiRwwxfs7|	黄牛|羊|	猪|	None|	None|1.0|NaN|NaN|1.0|	1.0|
|4	|R_3LhiqCpFNw|	黄牛|马|	羊	|猪|	None|1.0|NaN|1.0|1.0|1.0|
|5	|R_tSLLOuGCSR|	黄牛|马	|羊	|猪	|None|1.0|NaN|1.0|1.0|1.0|
|6	|R_aaHMHC0KpH|	黄牛|	羊	|猪	|None|	None|1.0|NaN|NaN|	1.0|	1.0|

Later, the loop could be applied to the overall dataset. **Note**: the global variable need to be modified inside loop.
