---
title: 整理pandas的read_csv方法
author: Mingxian Yang
date: 2021-11-29 18:10:00 +0800
categories: [Learning Notes]
tags: [Python]
math: true
---

> 读取数据是使用pandas做数据处理的第一步，数据源可以来自于各种地方，csv、XML、json等都是数据处理的常见数据源文件。而读取csv文件是数据处理中最为常用的一种。pandas提供了非常强力的支持，其提供了大量的参数以供user方便地读取csv文件。这些参数中，有的可能因为使用频率很低而经常被忽略，有的却很常见为读取数据提供了巨大的便利。这篇文章就为数据分析的csv数据读取做一个简要的汇总整理。

read_csv中的基本参数

**filepath_or_buffer**
数据输入的路径：可以是文件路径、可以是URL，也可以是实现read方法的任意对象。这个参数，就是我们输入的第一个参数。
```python
pd.read_csv("data.csv")
```

还可以是一个URL，如果访问该URL会返回一个文件的话，那么pandas的read_csv函数会自动将该文件进行读取。
```python
pd.read_csv("http://localhost/data.csv")
```

里面还可以是一个 _io.TextIOWrapper，甚至是临时文件。比如：
```python
//_io.TextIOWrapper
f = open("data.csv", encoding="utf-8")
pd.read_csv(f)

//临时文件
import tempfile
tmp_file = tempfile.TemporaryFile("r+")
tmp_file.write(open("girl.csv", encoding="utf-8").read())
tmp_file.seek(0)
pd.read_csv(tmp_file)
```
- *注释：一般情况下，我们直接读取csv文件较多。read_csv等方法使用时，直接加上r即可食用：*
```python
  pd.read_csv(r"F:\test_data.csv")
```

**sep : str, default ‘,’**
读取csv文件时指定的分隔符，默认为逗号。注意："csv文件的分隔符" 和 "我们读取csv文件时指定的分隔符" 一定要一致。分隔符长于一个字符并且不是‘\s+’,将使用python的语法分析器。并且忽略数据中的逗号。正则表达式例子：'\r\t'. 

比如：data.csv中，我们将其分隔符从逗号改成"//"，如果这个时候还是用默认的逗号分隔符，那么数据读取之后便混为一体。

![sep](/assets/imgs/2021/017.png)

**delimiter : str, default None**
( str, default None )  定界符，备选分隔符（如果指定该参数，则sep参数失效）

**delim_whitespace : boolean, default False.**
指定空格(例如’ ‘或者’ ‘)是否作为分隔符使用，等效于设定sep='\s+'。如果这个参数设定为Ture那么delimiter 参数失效。
在新版本0.18.1支持
 
header : int or list of ints, default ‘infer’
指定行数用来作为列名，数据开始行数。如果文件中没有列名，则默认为0，否则设置为None。如果明确设定header=0 就会替换掉原来存在列名。header参数可以是一个list例如：[0,1,3]，这个list表示将文件中的这些行作为列标题（意味着每一列有多个标题），介于中间的行将被忽略掉（例如本例中的2；本例中的数据1,2,4行将被作为多级标题出现，第3行数据将被丢弃，dataframe的数据从第5行开始。）。
注意：如果skip_blank_lines=True 那么header参数忽略注释行和空行，所以header=0表示第一行数据而不是文件的第一行。
 
**names : array-like, default None**
用于结果的列名列表，如果数据文件中没有列标题行，就需要执行header=None。默认列表中不能出现重复，除非设定参数mangle_dupe_cols=True。
 
**index_col : int or sequence or False, default None**
用作行索引的列编号或者列名，如果给定一个序列则有多个行索引。
如果文件不规则，行尾有分隔符，则可以设定index_col=False 来是的pandas不适用第一列作为行索引。
 
**usecols : array-like, default None**
返回一个数据子集，该列表中的值必须可以对应到文件中的位置（数字可以对应到指定的列）或者是字符传为文件中的列名。例如：usecols有效参数可能是 [0,1,2]或者是 [‘foo’, ‘bar’, ‘baz’]。使用这个参数可以加快加载速度并降低内存消耗。
 
**squeeze : boolean, default False**
如果文件值包含一列，则返回一个Series
 
**prefix : str, default None**
在没有列标题时，给列添加前缀。例如：添加‘X’ 成为 X0, X1, ...
 
**mangle_dupe_cols : boolean, default True**
重复的列，将‘X’...’X’表示为‘X.0’...’X.N’。如果设定为false则会将所有重名列覆盖。

### read_csv中的通用解析参数

**dtype : Type name or dict of column -> type, default None**
每列数据的数据类型。例如 {‘a’: np.float64, ‘b’: np.int32}
 
**engine : {‘c’, ‘python’}, optional**
Parser engine to use. The C engine is faster while the python engine is currently more feature-complete.
使用的分析引擎。可以选择C或者是python。C引擎快但是Python引擎功能更加完备。
 
**converters : dict, default None**
列转换函数的字典。key可以是列名或者列的序号。
 
**true_values : list, default None**
Values to consider as True
 
**false_values : list, default None**
Values to consider as False
 
**skiprows : list-like or integer, default None**
需要忽略的行数（从文件开始处算起），或需要跳过的行号列表（从0开始）。

**skipfooter : int, default 0**
从文件尾部开始忽略。 (c引擎不支持)
 
 
**nrows : int, default None**
需要读取的行数（从文件头开始算起）。

**low_memory : boolean, default True**
分块加载到内存，再低内存消耗中解析。但是可能出现类型混淆。确保类型不被混淆需要设置为False。或者使用dtype 参数指定类型。注意使用chunksize 或者iterator 参数分块读入会将整个文件读入到一个Dataframe，而忽略类型（只能在C解析器中有效）

**memory_map : boolean, default False**
如果使用的文件在内存内，那么直接map文件使用。使用这种方式可以避免文件再次进行IO操作。

## read_csv中的空值处理相关参数

**na_values : scalar, str, list-like, or dict, default None**
一组用于替换NA/NaN的值。如果传参，需要制定特定列的空值。默认为‘1.#IND’, ‘1.#QNAN’, ‘N/A’, ‘NA’, ‘NULL’, ‘NaN’, ‘nan’`.
 
**keep_default_na : bool, default True**
如果指定na_values参数，并且keep_default_na=False，那么默认的NaN将被覆盖，否则添加。
 
**na_filter : boolean, default True**
是否检查丢失值（空字符串或者是空值）。对于大文件来说数据集中没有空值，设定na_filter=False可以提升读取速度。
 
**skip_blank_lines : boolean, default True**
如果为True，则跳过空行；否则记为NaN。

**verbose : boolean, default False**
是否打印各种解析器的输出信息，例如：“非数值列中缺失值的数量”等。


## read_csv中的时间处理相关参数

**parse_dates : boolean or list of ints or names or list of lists or dict, default False**
boolean. True -> 解析索引
list of ints or names. e.g. If [1, 2, 3] -> 解析1,2,3列的值作为独立的日期列；
list of lists. e.g. If [[1, 3]] -> 合并1,3列作为一个日期列使用
dict, e.g. {‘foo’ : [1, 3]} -> 将1,3列合并，并给合并后的列起名为"foo"

**date_parser : function, default None**
用于解析日期的函数，默认使用dateutil.parser.parser来做转换。Pandas尝试使用三种不同的方式解析，如果遇到问题则使用下一种方式。
1.使用一个或者多个arrays（由parse_dates指定）作为参数；
2.连接指定多列字符串作为一个列作为参数；
3.每行调用一次date_parser函数来解析一个或者多个字符串（由parse_dates指定）作为参数。
 
**infer_datetime_format : boolean, default False**
如果设定为True并且parse_dates 可用，那么pandas将尝试转换为日期类型，如果可以转换，转换方法并解析。在某些情况下会快5~10倍。
 
**keep_date_col : boolean, default False**
如果连接多列解析日期，则保持参与连接的列。默认为False。
 
**dayfirst : boolean, default False**
DD/MM格式的日期类型


## read_csv中的分块读入相关参数
**iterator : boolean, default False**
返回一个TextFileReader 对象，以便逐块处理文件。
 
**chunksize : int, default None**
文件块的大小， See IO Tools docs for more informationon iterator and chunksize.



## read_csv中的格式和压缩相关参数
**compression : {‘infer’, ‘gzip’, ‘bz2’, ‘zip’, ‘xz’, None}, default ‘infer’**
直接使用磁盘上的压缩文件。如果使用infer参数，则使用 gzip, bz2, zip或者解压文件名中以‘.gz’, ‘.bz2’, ‘.zip’, or ‘xz’这些为后缀的文件，否则不解压。如果使用zip，那么ZIP包中国必须只包含一个文件。设置为None则不解压。
新版本0.18.1版本支持zip和xz解压
 
**thousands : str, default None**
千分位分割符，如“，”或者“."
 
**encoding : str, default None**
指定字符集类型，通常指定为'utf-8'. List of Python standard encodings

**error_bad_lines : boolean, default True**
如果一行包含太多的列，那么默认不会返回DataFrame ，如果设置成false，那么会将改行剔除（只能在C解析器下使用）。
 
**warn_bad_lines : boolean, default True**
如果error_bad_lines =False，并且warn_bad_lines =True 那么所有的“bad lines”将会被输出（只能在C解析器下使用）。
 


## 总结
以上便是pandas的read_csv函数中绝大部分参数了，而且其中的部分参数也适用于读取其它类型的文件。其实在读取csv文件时所使用的参数就那么几个，很多参数平常都不会用，但至少要了解一下，因为在某些特定的场景下它们是可以很方便地帮我们解决一些问题的。
当然，read_csv函数中的参数还不止我们上面说的那些，有几个我们还没有介绍到，感兴趣可以自己看一下。但是个人觉得，掌握上面的那些参数的用法的话，其实已经完全够用了。

