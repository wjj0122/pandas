## Pandas数据处理相关
最近用python的pandas库处理了一些表格数据，用的不是很熟练，记录一下一些基本用法
- - -

**0.导库**
```python
import pandas as pd
import csv
import numpy as np
```

**1.数据读取**
```python
# 读取csv文件的最基本方法，转化为了dataframe格式,filename为文件名（已经放在python文件的同一个文件夹下）
df = pd.read_csv("finename.csv")

# 查看dataframe尺寸
print(df.shape) # df.shape[0]表示行数,df.shape[1]表示列数

# 查看dataframe的前几行数据
print(df.head()) #默认打印前五行
print(df.head(10)) #打印前10行，以此类推

# 取某一列数据
print(df['col_name']) #col_name为对应的表头标题
print(df,loc[:,col]) #col为列号
# 读前几行
print(df['col_name'].head()) # 用法同整个dataframe的用法

# 取某一行数据
print(df.loc[row,:]) # 查看第row行的数据

# 查看某个数据
print(df['col_name'][row]) #查看df中某一列的第row行数据
print(df.iloc[row,col]) #查看df中row行col列的数据
```
`read_csv()`函数的参数：
```python
pd.read_csv(filepath_or_buffer, sep=',', delimiter=None, header='infer', names=None, index_col=None, 
usecols=None, squeeze=False, prefix=None, mangle_dupe_cols=True, dtype=None, engine=None, converters=None, 
true_values=None, false_values=None, skipinitialspace=False, skiprows=None, nrows=None, na_values=None, 
keep_default_na=True, na_filter=True, verbose=False, skip_blank_lines=True, parse_dates=False,
infer_datetime_format=False, keep_date_col=False, date_parser=None, dayfirst=False, iterator=False, chunksize=None, 
compression='infer', thousands=None, decimal=b'.', lineterminator=None, quotechar='"', quoting=0, escapechar=None, 
comment=None, encoding=None, dialect=None, tupleize_cols=False, error_bad_lines=True, warn_bad_lines=True, 
skipfooter=0, skip_footer=0, doublequote=True, delim_whitespace=False, as_recarray=False, compact_ints=False, 
use_unsigned=False, low_memory=True, buffer_lines=None, memory_map=False, float_precision=None)
```
常用的有：
1. `filepath_or_buffer`:（这是唯一一个必须有的参数，其它都是按需求选用的）
文件所在处的路径
2. `sep`：
指定分隔符，默认为逗号’,’
3. `delimiter : str, default None`
定界符，备选分隔符（如果指定该参数，则sep参数失效）
4. `header：int or list of ints, default 'infer'`
指定哪一行作为表头。默认设置为0（即第一行作为表头），如果没有表头的话，要修改参数，设置`header=None`
5. `names`：
指定列的名称，用列表表示。一般我们没有表头，即`header=None`时，这个用来添加列名就很有用啦！
6. `index_col`:
指定哪一列数据作为行索引，可以是一列，也可以多列。多列的话，会看到一个分层索引
7. `prefix`:
给列名添加前缀。如`prefix='x'`,会出来`"x1"、“x2”、"x3"`
8. `nrows : int, default None`
需要读取的行数（从文件头开始算起）
9. `encoding:`
乱码的时候用这个就是了，[官网](https://docs.python.org/3/library/codecs.html#standard-encodings)文档看看用哪个：
10. `skiprows : list-like or integer, default None`
需要忽略的行数（从文件开始处算起），或需要跳过的行号列表（从0开始）

**2.数据替换**

当我们要替换整个dataframe或者某一列中的某些数据时，使用`replace`函数进行替换
```python
# 实例，其中'No'为被替换的值，0为替换它的值
df_mof['Has_OMS'] = df_mof['Has_OMS'].replace('No',0)

#函数参数
DataFrame.replace(to_replace=None,value=None,inplace=False,limit=None,regex=False,method='pad',axis=None)
# to_replace:str，regex，list，dict，Series，int，float或None
# value:替换它的值
# inplace:是否修改原数据
# limit:向前或向后填充的最大尺寸间隙
# regex:是否支持正则表达式
# method:方法

# 多值替换
df1 = df1.replace([3,2],[30,20]) # 3替换为30，2替换为20

# 改变某列数据的数据类型
df['col']=df['col'].astype(int)
```

当我们要替换某个字符串里的一部分字符时使用`str.replace()`函数
```python
# 实例
df['All_Metals'] = df['All_Metals'].str.replace('[','')
df['All_Metals'] = df['All_Metals'].str.replace(']','')
df['All_Metals'] = df['All_Metals'].str.replace("'",'')
df['All_Metals'] = df['All_Metals'].str.replace(' ','')
# 它们分别将All_Metals这一列的所有字符串数据中的字符[]'和空格替换为空
```

**3.数据拆分**
将字符串数据按照某个字符进行拆分，形成多列
```python
# 按照'将字符串切片，形成多列数据
df1 = df['atoms_type'].str.split(',',expand=True)
# 当被切成的长度不一致时，较短的会以None填充
```
区分`None`和`Nan`：
```python
None == None #返回True
np.nan == np.nan #返回False

# 如何判断是否为nan？
value != value # 返回True时则为nan
```

**4.数据查找**
查找某列中某个数据的索引号
```python
# 实例，在df2的smiles这一列中查找某个值的索引号，n为我们想要查找的词
lid = df2[df2['smiles'].isin([n])].index.values[0]
```

**5.数据添加**
```python
# 实例，往dataframe的最后一行添加一行数据
df_atom.loc[len(df_atom.index)]=[i,atom]
# 使用loc定位到最后一行，然后直接写入即可

# 实例，往dataframe的最后一行添加多行数据
df_temp=df_temp.append(df_1,ignore_index=True) 
# ignore_index=True表示在原有的索引基础上计数

# 添加一列数据，其值来自于其他两列之差,直接相减即可
df['col_3']=df['col_1']-df['col_2']
```

**6.数据去重**
去除某一行或者某一列的重复值
```python
# 将df中row行的所有数据去重并存入列表
list1=df.loc[row,:].drop_duplicates().values.tolist()、

# python内置的set每次生成的顺序都不一样，为了保证一致性可以使用sort
fragments = set()
...
fragments = list(fragments)
fragments.sort()
```

**7.数据写入**
```python
# 将df写入csv文件，注意要将对应文件关闭，否则会失败
df.to_csv("filename.csv",encoding='utf-8-sig')
```

