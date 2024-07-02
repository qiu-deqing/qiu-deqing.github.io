---
title: Python数据分析挖掘实战
---

地址：https://www.imooc.com/learn/1273

测试数据： https://www.kaggle.com/datasets/blastchar/telco-customer-churn

The book we recommend to learn pandas is Python for Data Analysis, by Wes McKinney, creator of pandas.

# 第一章 课程介绍

## 1-1 课程介绍和工具准备

# 第二章 数据读取

## 2-1 Pandas读取和保存数据的方法

Pandas I/O支持的数据格式：Picking, Feather, Flat file, Parquet, Clipboard, OCR, Excel, SAS, JSON, SPSS, HTML, SQL, HDFStore:PyTables, Google BigQuery, (HDF5), STATA

详细清单参考官网： https://pandas.pydata.org/docs/user_guide/io.html?highlight=io

常用数据读
- [read_excel()](https://pandas.pydata.org/docs/user_guide/io.html?highlight=io#io-excel-reader)

读取之后的数据是[DataFrame](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.columns.html)

### [read_csv()](https://pandas.pydata.org/docs/reference/api/pandas.read_csv.html)

常用参数：
- usecols=[1, 3, 5]： 只读取需要的列`pd.read_csv(data, usecols=['foo', 'bar'])`
- chunksize=100: 返回Reader迭代器，迭代读取数据


 ## 第三章

 ### Pandas查看和预览数据的方法

 [DataFrame API](https://pandas.pydata.org/docs/reference/frame.html)

 常用数据查看方法：

 - 查看数据尺寸
 - 查看指定条件数据
 - 预览数据内容
 - 查看数据类型

- `df.head(n)`: 查看前n条数据
- `df.tail(n)`: 查看后n条数据
- `df.shape`: 数据信息：(行数, 列数)
- `df[df['field']==value]`: 筛选满足条件的数据，可继续使用此方法多次过滤
- `df[fieldName]`: 过滤指定列，如df['name']，多列选择`df[['f1', 'f2']]`
- `df[n1:n2]`: 过滤指定行，如`df[10:20]`，查看[10,20)行
- `df.iat[row, col]`：查看具体单元格
- `df.dtypes`: 查看数据详细信息和数据指标类型
- `df.info()`：查看数据详细信息，指标类型统计，数据大小，内存占用信息

统计不同数据类型指标数量
```
from collections import Counter
Counter(df.dtypes.values)
```

## 第四章 数据统计分析

- `df.describe()`：对数值型数据进行统计并统计基本信息。count：数据条数；mean：平均值；std：标准差；min：最小值；25%：下次中位数； 50%：中位数；75%：上次中位数；max：最大值。下次中位数和上次中位数以外的可作为异常值关注区域

  ```
            Unnamed: 0  SeniorCitizen       tenure  MonthlyCharges
  count  7053.000000    7053.000000  7053.000000     7052.000000
  mean   3526.000000       0.162059    33.760669       68.771065
  std    2036.170057       0.368531   103.241266      255.020317
  min       0.000000       0.000000  -111.000000     -263.000000
  25%    1763.000000       0.000000     9.000000       35.500000
  50%    3526.000000       0.000000    29.000000       70.350000
  75%    5289.000000       0.000000    55.000000       89.850000
  max    7052.000000       1.000000  8256.000000    18937.000000
  ```


- `df[fieldName].value_counts()`：分类型数据统计如`df['gender'].value_counts()`,查看占比`df['gender'].value_counts(normalize=True)`

  ```
  Male      3556
  Female    3497
  Name: gender, dtype: int64
  ```

  ```
  Male      0.504183
  Female    0.495817
  Name: gender, dtype: float64
  ```

- [pd.cut()](https://pandas.pydata.org/docs/reference/api/pandas.cut.html?highlight=cut#pandas.cut):对数据进行分箱：

- `pd.cut(range(10), bins=5)`: 10个数据分成5份

```
group = pd.cut(df['age'], bins=100)
group.value_counts()  // 按照数量多少降序显示
group.value_counts(normalize=True)  // 数量显示为比例
group.value_counts().sort_index()  // 按照分箱顺序


```

# 绘制柱状图

```
import matplotlib.pyplot as plt
plt.figure(figsize=(20, 10))
plt.bar(
  range(12),  # 等宽显示
  df.value_counts().sort_index().values,
  tick_label=df.value_counts().sort_index().index
)
plt.show()
```

# 密度分布直方图

```
import seaborn as sns
sns.distplot(df)
plt.show()
```

# 散点图

```
import seaborn as sns
import matplotlib.pyplot as plt
sns.scatterplot(df['t1'], df['t2'])   # x, y
plt.show()
```

# 异常值处理


```
cols = ['a', 'b', 'c']

for col in cols:
  print('*' * 50 )
  print(col)
  q75 = df[col].quantile(q=0.75)
  print('上四分位数：', q75)


  q25 = df[col].quantile(q=0.25)
  print('下四分位数：', q25)

  # 四分位输间距
  d = q75 - q25
  print('四分位间距：', d)

  # 数据上下界
  data_top = q75 + 3 * d
  data_bottom = q25 - 3 * d
  print('数据上界：', data_top)
  print('数据下界：', data_bottom)

  # 查看异常值数量
  print('异常值个数：', len(df[(df[col] > data_top) | (df[col] < data_bottom)]))

  # 剔除异常值： 1. 根据条件直接删除  2. 删除重复数据
  group= pd.cut(df[col], bins = 100)
  plt.figure(figsize=(20, 10))
  plt.bar(
      range(12),  # 等宽显示
      group.value_counts().sort_index().values,
      tick_label=group.value_counts().sort_index().index
  )
  plt.show()

```
