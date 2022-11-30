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

Parameter, `axis`, needs to be **noticed carefully**.

<br></br>

### Summarizing splitted data
