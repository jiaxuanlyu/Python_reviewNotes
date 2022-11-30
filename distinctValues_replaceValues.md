### Distinct Values

Aiming to get a sense about how many distinct values are inside one column, `.unique()` and `.nunique()` are used.  \
The functions are still from `pandas`. 
```python
#make a new dataframe, only about animal types of grazing
yangzhi = pd.DataFrame(df[['您家中养殖过什么？']])
yangzhi.head()
```
|               | 	您家中养殖过什么？ |
| ------------- | ------------- |
| 0  | 黄牛,马,羊,猪  |
| 1  | 黄牛,马,羊,猪  |
| 2  | 马,羊,猪  |
| 3  | 黄牛,马,羊,猪  |
| 4  | 黄牛,马,猪  |

<sub>The table just gives a brief view about how the dataset looks like, but is not the real dataset</sub>
\
Indeed, whether creating new dataframe by using `.DataFrame()` does not matter. The goal could be achieved by using:
```python
pd.DataFrame(df[['您家中养殖过什么？']]).head()
```
Same for later parts, when we want to get unique values. But it should be **noticed** that, `.unique()` is a function only written for **Series**.
In other words, when counting unique values for one column, the type should be **series**.
```python
yangzhi['您家中养殖过什么？'].unique()
```
```python
df['您家中养殖过什么？'].unique()
```
```
array(['黄牛,马,羊,猪', '马,羊,猪', '黄牛,羊,猪', '猪', '黄牛,猪', '黄牛,羊', '羊,猪', '羊',
       '黄牛', nan, '黄牛,牦牛,马,羊,猪', '黄牛,马,猪', '马', '马,猪', '牦牛,羊,猪', '5'],
      dtype=object)
```
`.nunique()` is used to count how many dinstinct values inside a column.
```python
yangzhi['您家中养殖过什么？'].nunique()
```
Meanwhile, there are some notes about synatx when writing `print()` with variables. 
```python
print("The unique values of the question '您家中养殖过什么？' are", str(df['您家中养殖过什么？'].unique()))
```
```
The unique values of the question '您家中养殖过什么？' are ['黄牛,马,羊,猪' '马,羊,猪' '黄牛,羊,猪' '猪' '黄牛,猪' '黄牛,羊' '羊,猪' '羊' '黄牛' nan
 '黄牛,牦牛,马,羊,猪' '黄牛,马,猪' '马' '马,猪' '牦牛,羊,猪' '5']
```
One method is writting inside `str()`, and another method is using `.format()`. There is a [reference](https://stackoverflow.com/questions/15286401/print-multiple-arguments-in-python) about
how to use `.format`. One benefit of `.format()` is that you could add variable inside `print()` and is able to make sentence as a loop.
```python
num = df['您家中养殖过什么？'].nunique()
print("There are {} of unique values".format(num))
```

<br></br>

### Replace Values

The survey data needs to be further cleaned. Like previous steps shows, the values of the answer should not be number, but the result shows that one
'5' is contained inside dataset. \
Also, the frequency of the answers could be gained by using `.value_counts())`.
```python
# pandas count distinct values in column
pd.DataFrame(df['您家中养殖过什么？'].value_counts())
```
adding`.DataFrame` just helps to show the result in better format, like:
|               | 	您家中养殖过什么？ |
| ------------- | ------------- |
| 猪	| 99  |
| 黄牛,羊,猪 |	58 |
|黄牛,牦牛,马,羊,猪 | 1 |
|5 | 1 |
| 马 | 1 |

The number '5' should not be appreaded as '5'. It should be change as "黄牛,牦牛,马,羊,猪". Hence, `.replace()` is used here. \
[Documentation](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.replace.html) of `.replace()` needs to be read carefully.  \
Based on documentation: 
```python
DataFrame.replace(to_replace=None, value=_NoDefault.no_default, *, inplace=False, limit=None, regex=False, method=_NoDefault.no_default)
```
Data types of **to_replace** and **value** should be noticed when using `.replace()`. \
Currently, either `yangzhi['您家中养殖过什么？']` or `df['您家中养殖过什么？']` is **Series**. To show as a clearer format, benefited by using
Jupyter Notebook, the result is shown as **dataframe**.
```python
pd.DataFrame(yangzhi['您家中养殖过什么？'].replace(to_replace='5', value= '黄牛,牦牛,马,羊,猪').value_counts())

#if want to create a new df
new_yangzhi = pd.DataFrame(yangzhi['您家中养殖过什么？'].replace(to_replace='5', value= '黄牛,牦牛,马,羊,猪'))
pd.DataFrame(new_yangzhi['您家中养殖过什么？'].value_counts())
```
|               | 	您家中养殖过什么？ |
| ------------- | ------------- |
| 猪	| 99  |
| 黄牛,羊,猪 |	58 |
|黄牛,牦牛,马,羊,猪 | 2|
| 马 | 1 |

`.nunique()` could also be used here to check whether data is replaced or not. 

`.drop()` could also be used to drop rows and columns. [Documentation](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.drop.html).
