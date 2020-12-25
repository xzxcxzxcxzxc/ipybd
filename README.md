- [一、概述](#一概述)
    - [1.1 数据处理的能力](#11-数据处理的能力)
    - [1.2 生成工具的能力](#12-生成工具的能力)
    - [1.3 数据统计分析能力](#13-数据统计分析能力)
- [二、安装方法](#二安装方法)
- [三、主要的数据处理方法](#三主要的数据处理方法)
  - [3.1 BioName](#31-bioname)
  - [3.2 FormatDataSet](#32-formatdataset)
    - [3.2.1 数据的装载](#321-数据的装载)
    - [3.2.2 学名处理](#322-学名处理)
    - [3.2.3 中文行政区划清洗和转换](#323-中文行政区划清洗和转换)
    - [3.2.4 日期、时间的清洗和转换](#324-日期时间的清洗和转换)
    - [3.2.5 经纬度清洗和转换](#325-经纬度清洗和转换)
    - [3.2.6 数值及数值区间的清洗和转换](#326-数值及数值区间的清洗和转换)
    - [3.2.7 重复值标注](#327-重复值标注)
    - [3.2.8 数据列的分割](#328-数据列的分割)
    - [3.2.9 数据列的合并](#329-数据列的合并)
- [四、自定义数据模型](#四自定义数据模型)
  - [4.1 特定数据集的结构重塑](#41-特定数据集的结构重塑)
  - [4.2 众源数据集的结构重塑](#42-众源数据集的结构重塑)
  - [4.3 带有值处理的数据模型定义](#43-带有值处理的数据模型定义)
- [五、DarwinCore 模型](#五darwincore-模型)
  - [5.1 Occurrence](#51-occurrence)
  - [5.2 CVH](#52-cvh)
  - [5.3 NSII](#53-nsii)
  - [5.4 NOI](#54-noi)
  - [5.5 KingdoniaPlant](#55-kingdoniaplant)
- [六、标签打印](#六标签打印)
- [七、基于 Pandas 的数据统计与分析](#七基于-pandas-的数据统计与分析)
- [八、特别声明](#八特别声明)



## 一、概述

ipybd 是一款由 `Python` 开发的中文生物多样性数据清洗、统计与分析框架。`ipybd` 当前版本主要致力于构建众源数据集与生物多样性数据库之间的通道。在此之前，国内生物多样性数据的提取、转换、装载等处理尚不成体系，不同数据源、不同数据集，不同数据清理者所采用的数据处理方式往往会有很大的差异。因此即便使用相同的处理工具，相应数据的处理方式也很难被复用到不同的数据处理工作之中。这导致数据链路中的采集者、接收者、利用者都需要为数据的标准化付出巨大的时间和人工成本，经验上这一工作至少会占据 70% 的链路时间。`ipybd` 当前版本则实现了一个**通用**的生物多样性数据提取、转换、装载框架，它可以显著提升数据平台、数据管理机构、数据使用者对不同来源、不同格式、不同品质、不同规范的数据集进行统一的清洗转换与整合利用的能力，从而大幅降低数据处理的门槛和成本，提高数据分析前的数据处理品质和效率。目前 `ipybd` 已经具备了以下一些能力：

####  1.1 数据处理的能力

+ **数据装载**： 目前支持从Excel/CSV/TEXT/Pandas.DataFrame 以及各类关系型数据库（比如Mysql）导入数据；

+ **物种学名**：可以在线批量获取 [POWO](http://www.plantsoftheworldonline.org/), [IPNI](https://www.ipni.org/), [中国生物物种名录](http://www.sp2000.org.cn/)上相应物种的最新分类阶元、分类处理、物种图片、发表文献、相关异名等信息；

+ **日期与时间**：可以对各类手工转录的日期和时间，进行严格的校验和转换，并可根据需要输出不同样式；

+ **经纬度**：可以对各类手工转录的经纬度，进行严格的校验和转换；

+ **中文行政区划**：可以对各种自然语言表达的中文县级及其以上的行政区划进行高品质的匹配、校正和转换；

+ **选值**：能够自定义各种字段的可选值和转换关系，并根据转换关系，自动完成值的规范化；

+ **数值和数值区间**：可以对各类数值或数值区间，进行自动化的清洗、校正和转换；

+ **拆分与合并**：`ipybd` 不仅可以对数据列进行各种合并和拆分，还可以将单列、多列或整个表格的数据列映射为各类 Python `dict`/`list` 对象或者 JSON `Object` 和 `Array`，从而为各种数据分析和互联网平台的数据交换工作提供灵活的格式转换支持。

+ **标签打印**：能够生成带有条形码样式的纸质标签文档以供打印。

+ **数据输出**：经过处理的数据，可以输出为Excel/CSV文件或者直接更新至相应的数据库之中。

#### 1.2 生成工具的能力

框架是生成工具的工具 ，`ipybd` 定义了一套简洁的语义，可以帮助用户快速的定制出个性化的数据转换模型。这些模型能够根据相应任务的需要，将以上各种数据处理能力自由拼接和组合，以实现数据集的自动化清洗和转换。

同时 `ipybd` 数据模型还具有良好的泛化能力，定义的模型不仅可以处理特定的数据集，还可以应用到同种类型不同数据源的处理任务之中。此外`ipybd` 数据模型同样支持数据处理能力的个性化扩展，用户自定义的数据处理方法也能够应用到数据模型的定义之中。

#### 1.3 数据统计分析能力

 `ipybd` 基础数据结构完全基于 `Pandas.DataFrame` 构建，因此其本身可以直接使用 `Pandas` 完善的数据统计和分析功能。同时，`pandas` 作为 Python 数据分析生态中的核心库，其完善的生态支持也为 `ipybd` 拓展生物多样性相关的分析能力提供了坚实的开发基础。

   

​     

## 二、安装方法

通过 pip 在线安装 ipybd：

```python
pip install ipybd
```
或者将程序包 Clone 到本地后，在终端内进入 ipybd 目录，然后运行：

```python
pip install .
```





## 三、主要的数据处理方法

### 3.1 BioName

BioName 类可接受 `tuple`、`list`或`Pandas.Series` 类型的学名字符串序列实例化对象：

```python
from ipybd import BioName

poa = BioName(["Poaceae", "Poa", "Poa annua", "Poa annua Schltdl. & Cham.", "Poa annua L.", None])
```
参与实例化的学名可以包含命名人（命名人的写法可随意），也可以不包含命名人（但包含命名人可以提高匹配精度），学名格式可以比较规范，也可以是不太规范的人工转录学名（但不能简写属名或种名）。

BioName 实例主要通过 `get`方法配合关键字从 [powo](http://www.plantsoftheworldonline.org/)、[ipni](www.ipni.org)、[中国生物物种名录](www.sp2000.org.cn) 获取相关学名的分类阶元、分类处理、物种图片、发表文献、相关异名等数据 。下面以获取上文 `poa` 实例对象在`powo`平台上的科属地位为例：

```python
poa.get('powoName')

Out:
[
  ('Poaceae', 'Barnhart', 'Poaceae'),
 	('Poa', 'L.', 'Poaceae'),
 	('Poa annua', 'L.', 'Poaceae'),
 	('Poa annua', 'Schltdl. & Cham.', 'Poaceae'),
 	('Poa annua', 'L.', 'Poaceae'),
 	(None, None, None)
]

```
默认返回的结果是以 `tuple` 为元素的 `list` 对象，`list`对象中的各检索结果与检索词的位置一一对应，对于没有检索结果的值，则以`None`值补充并与其他各检索结果对齐，以方便直接将返回结果转换成表格的行列；若希望以 `dict`对象返回，在请求时则可以通过`typ`参数指定：

```python
poa.get('powoName', typ=dict)  

Out:
{
  'Poaceae': ('Poaceae', 'Barnhart', 'Poaceae'),
 	'Poa': ('Poa', 'L.', 'Poaceae'),
 	'Poa annua': ('Poa annua', 'L.', 'Poaceae'),
 	'Poa annua Schltdl. & Cham.': ('Poa annua', 'Schltdl. & Cham.', 'Poaceae'),
 	'Poa annua L.': ('Poa annua', 'L.', 'Poaceae')
}
```

除了上述示例中的`powoName`参数，目前`BioName`的`get`关键字总共有 9 个，它们的作用分别是：

+ `'powoName'`: 获取 powo 平台相应学名的科属地位、学名简写和命名人信息；

+ `'powoImages'`: 获取 powo 平台相应学名的物种图片地址，每个物种返回三张图片地址；

+ `'powoAccepted'`: 获取 powo 平台相应学名的接受名；

+ `'ipniName'`: 获取 ipni 平台相应学名的科属地位、学名简写和命名人信息；

+ `'ipniReference'`: 获取 ipni 平台相应学名的发表文献信息；

+ `'colName'`: 获取相应学名在中国生物物种名录中的科属地位、学名简写和命名人信息；

+ `'colTaxonTree'`: 获取相应学名在中国生物物种名录中的完整的分类学阶元信息；

+ `'colSynonyms'`: 获取相应学名在中国生物物种名录中的异名信息;

+ `'stdName'`: 优先获取中国生物物种名录的名称信息，如果无法获得，则获取`ipni`的信息。 

使用时，只需将上例`get`方法中的相应关键字替换为所需关键字即可。

### 3.2 FormatDataSet

`FormatDataSet` 类是 `ipybd` 进行数据处理的核心类。它提供了对生物多样性相关的各类数据集进行各种结构重构和值格式化处理的基本方法。普通用户也可以直接调用它以更加自主的方式处理个性化的数据集或者开发自己的脚本和程序。

#### 3.2.1 数据的装载

目前 FormatDataSet 可接受一个 Excel、CSV、TXT、Pandas.DataFrame、RDBMS 数据库对象进行实例化：基于 Excel 、CSV 、TXT 数据表实例化，可以直接传递相应文件的路径给 `FormatDataSet`：

```python
collections = FormatDataSet(r"~/Documents/record2019-09-10.xlsx") 
```

`FormatDataSet` 默认采用 UTF-8 编码文件，如果传递 CSV 文件出现`UnicodeDecodeError`错误，可以尝试显式指定相应的编码方式，一般都可以得到解决（Python 支持的标准编码[戳这里](https://docs.python.org/3/library/codecs.html#standard-encodings)）。

```python
# 这里显式的指定了 csv 文件的编码方式为 gbk
collections = FormatDataSet(r"~/Documents/record2019-09-10.cvs", encoding='gbk') 
```

如果已经有一个 DataFrame 对象，也可以直接传递给 `FormatDataSet`：

```pythoon
collections = FormatDataSet(DataFrame)
```

基于本地或线上的关系型数据库创建 `FormatDataSet` 实例，需要先创建数据库连接。在 python 生态中有诸如 mysqlclient、pymysql、mysql-connector 等多种连接库可以使用，下方示例使用的是 sqlalchemy 建立 mysql 数据库连接，个人可以根据喜好自行选择相应的连接库创建连接器。

```python
# 首先导入相应的连接库
from sqlalchemy import create_engine

# 通过账户、密码、地址、端口等创建连接器
# 这里演示了与我本地 mysql 中 ScientificName 数据库建立连接
conn = create_engine('mysql+pymysql://root:mypassward@localhost:3306/ScientificName')

# 然后建立检索数据库的 sql 语句，用于筛选出需要的数据
# 这里的sql语句演示了从 ScientificName 数据库中调取 10 条 theplantlist 学名数据
sql = "select * from theplantlist limit 10;"

# 将 sql 语句和建立好的连接器传递给 ipybd.FormatDataSet
tpl = FormatDataSet(sql, conn)

# 执行完毕后，数据会以 DataFrame 结构保存 tpl 实例的 df 对象
# 然后就可以在本地内存中对这些数据进行各种操作了
# 比如查看数据中的学名 id 、family、genus 和拉丁名信息
tpl.df[['_id', 'family', 'genus', 'latin']] 

Out:
                        _id          family        genus                 latin
0  59367c08ec325b77e9124490     Achariaceae                          Chilmoria
1  59367c08ec325b77e9124491                                        Achariaceae
2  59367c08ec325b77e9124492     Achariaceae    Chilmoria     Chilmoria odorata
3  59367c08ec325b77e9124494  Alseuosmiaceae                         Alseuosmia
4  59367c09ec325b77e9124495                                     Alseuosmiaceae
5  59367c09ec325b77e9124496  Alseuosmiaceae   Alseuosmia    Alseuosmia banksii
6  59367c09ec325b77e9124498   Allisoniaceae                        Calycularia
7  59367c09ec325b77e9124499                                      Allisoniaceae
8  59367c09ec325b77e912449a   Allisoniaceae  Calycularia  Calycularia compacta
9  59367c09ec325b77e912449c     Acanthaceae                        Acanthodium
```

#### 3.2.2 学名处理

`FormatDataSet` 类基于 `BioName` 实例封装了一些学名处理方法，以便用户能够更便捷的对数据表中的名称进行处理。比如对于上述 `collections` 实例，若相关数据表中的学名并非单列，而是按照 `"属名"`、`"种名"`、`"种下单元"`、`"命名人"`四列分列存储，单纯使用 `BioName` 类需要用户先自行合并相应数据列才可以执行在线查询。而 `FormatDataSet` 实例则可以直接进行学名的查询和匹配：

```python
# 这里以获取 ipni 平台的信息为例
# 调用 get_ipni_name 时，可直接将多个列名按序传递给方法
collections.get_ipni_name("属名", "种名", "种下单元", "命名人")

Out:
[
	('Clematis', 'L.', 'Ranunculaceae'),
 	('Crepis', 'L.', 'Asteraceae'),
 	('Krascheninnikovia ceratoides', '(L.) Gueldenst.', 'Chenopodiaceae'),
	('Apiaceae', 'Lindl.', 'Apiaceae'),
 	('Boerhavia', 'L.', 'Nyctaginaceae'),
 	('Aster', 'L.', 'Asteraceae'),
 	('Agropyron cristatum', '(L.) P.Beauv.', 'Poaceae'),
 	('Orostachys', 'Fisch.', 'Crassulaceae'),
 	('Ephedra', 'L.', 'Ephedraceae'),
 	('Elymus', 'Mitchell', 'Poaceae'),
 	('Aster', 'L.', 'Asteraceae'),
 	('Nitraria tangutorum', 'Bobrov', 'Zygophyllaceae'),
 	(None, None, None),
 	('Carissa', 'L.', 'Apocynaceae'),
 	('Pedicularis', 'L.', 'Scrophulariaceae'),
 	('Centaurea', 'L.', 'Asteraceae'),
 	...
 ]
```

如上所示，不同数据表的学名表示方式时常会有差异，而通过诸如`get_ipni_name`方法这样的 `FormatDataSet` 实例方法可以大幅提高数据处理的便捷性和灵活性。目前 `FormatDataSet` 实例共支持以下几种学名处理方法：

+ `get_powp_name`: 获取 powo 平台相应学名的科属地位、学名简写和命名人信息；

+ `get_powo_images`: 获取 powo 平台相应学名的物种图片地址，每个物种返回三张图片地址；

+ `get_powo_accepted`: 获取 powo 平台相应学名的接受名；

+ `get_ipni_name`: 获取 ipni 平台相应学名的科属地位、学名简写和命名人信息；

+ `get_ipni_reference`: 获取 ipni 平台相应学名的发表文献信息；

+ `get_col_name`: 获取相应学名在中国生物物种名录中的科属地位、学名简写和命名人信息；

+ `get_col_taxontree`: 获取相应学名在中国生物物种名录中的完整的分类学阶元信息；

+ `get_col_Synonyms`: 获取相应学名在中国生物物种名录中的异名信息。

如果在使用这些方法时，并不希望程序直接返回结果，而是想直接将查询结果写入`collections`数据表，请求时可以将`concat`参数设置为`True`:

```python
collections.get_ipni_name("属名", "种名", "种下单元", "命名人", concat=True)
```

如果需要将整合后的数据表存储为文件，可以调用`collections`的`save_data`方法：

```python
collections.save_data(r"~/Documents/new_record.xlsx")
```

#### 3.2.3 中文行政区划清洗和转换

同物种学名一样，数据表中的中文行政区划也有可能是多列或单列。`FormatTale`提供了类似的方法对其进行批量清洗和转换。

```python
# FormatDataSet 实例可以通过 df 属性获得数据表的 DataFrame
# 下行代码输出了 collections 前 30 行记录的行政区划：
collections.df[["国别", "行政区"]].head(30)                                                                                                                                                                                           

Out:
     国别        行政区
0    中国        NaN
1    中国       大理南涧
2    中国  河北，承德，栾平县
3    中国       广东白云
4    中国    云南省云南楚雄
5    中国  甘肃省Anning
6   NaN         白云
7    中国     河北，承德县
8    中国      云南省大理
9    中国       台湾新竹
10   中国       内蒙白云
11   中国         海南
12  NaN         河南
13   中国         河北
14   中国       云南楚雄
15   中国      新竹市东区
16   中国  云南 Anning
17   中国   台湾新竹市,东区
18   中国        祥云县
19   中国         云县
20   中国         东昌
21   中国        东昌府
22   中国       江西东乡
23   中国        东乡族
24   中国      in 德钦
25   中国     安徽省怀远县
26   中国     黑龙江伊春市
27   中国         云龙
28   中国       四川南川
29   中国         龙江

```

可以发现 `collections` 实例的数据表中，每条记录的行政区划都是按照`"国别"`和`"行政区"`两个字段进行归类的，其中`"国别"`列的值相对规范，只有若干记录中会有些空值；而`"行政区"`列的值则非常的脏，主要问题表现为：

1. 行政区使用简称，比如大理南涧；
2. 行政等级信息缺失，比如“祥云县”缺少省级和市级行政区名称；
3. 名称缺少等级标识，比如“云龙”，不知是区还是县；
4. 格式和分隔符不统一，比如有用中文逗号作为分隔符，也有用空格，还有没有分隔符的；
5. 行政区中混有英文和拼音，比如“云南 Aning”。

类似这样的行政区数据大多转录自手写的纸质标签记录，尤其是那些年代比较久远的生物多样性原始数据集，这样简略的记录其实是广泛存在的，纯粹依靠人工逐条处理这些历史数据，其实是一件极为低效和不可靠的模式。而利用人工和 `ipybd`相结合的方式，可以大幅提高这类工作的效率和品质。

目前 `FormatDataSet` 实例的`format_admindiv`方法可以自动进行县级及其以上等级的中文行政区名称的清洗和转换：

```python
# 使用方式类似于学名处理方法，将覆盖国省市县行政区名的相关字段按序传递给方法即可
# 如果想要将清洗的结果直接替换 collections.df 数据表中的相应列
# 可以将这里的 inplace 保持默认，或设置为 True
collections.format_admindiv("国别", "行政区", inplace=False)

Out: 
   country province     city   county
0      !中国     None     None     None
1       中国      云南省  大理白族自治州  南涧彝族自治县
2       中国      河北省      承德市      平泉县
3       中国      广东省      广州市      白云区
4       中国      云南省  楚雄彝族自治州     None
5       中国      甘肃省     None     None
6      !中国      广东省      广州市      白云区
7       中国      河北省      承德市      承德县
8       中国      云南省  大理白族自治州     None
9       中国       台湾      新竹市     None
10      中国   内蒙古自治区      包头市   白云鄂博矿区
11     !中国      海南省     None     None
12      中国      河南省     None     None
13     !中国      河北省     None     None
14      中国      云南省  楚雄彝族自治州     None
15      中国       台湾      新竹市       东区
16      中国      云南省     None     None
17      中国       台湾      新竹市       东区
18      中国      云南省  大理白族自治州      祥云县
19      中国      云南省      临沧市       云县
20      中国      吉林省      通化市      东昌区
21      中国      山东省      聊城市     东昌府区
22      中国      江西省      抚州市      东乡县
23      中国      甘肃省  临夏回族自治州   东乡族自治县
24      中国      云南省  迪庆藏族自治州      德钦县
25      中国      安徽省      蚌埠市      怀远县
26      中国     黑龙江省      伊春市     None
27      中国      江苏省      徐州市      云龙区
28     !中国      四川省     None     None
29      中国     黑龙江省    齐齐哈尔市      龙江县

```

通过上述处理结果，可以发现`format_admindiv` 即便在应对简略的自然文本记录时，仍然可以将其中绝大多数的文本转换为正确规范的行政区等级。实际上随着数据完整性的提高，行政区文本的转换精度也会随之提高，在目前常用的数据规范下 `format_admindiv`的转换结果通常是可以被直接使用的。而对于信息比较简略的数据，`format_admindiv` 的优先参与也可以大幅降低后期人工核查的工作量，从而大大提高数据核查的质量和效率。

对于有疑议的转换，绝大多数情况下，`format_admindiv` 会将相应转换结果以英文"!"标注，但在一些特殊情况下仍然有可能会出现潜在的转换错误，这些情况包括：

1. 已经裁撤的行政区，比如第 28 行的 “四川南川”，程序只会匹配出“中国,四川省”，且不会做标注；
2. 拼音或英文行政区划，比如第 16 行的 “云南 Anning”，程序只会匹配到“中国,云南”且不会做标注；
3. 本身就有错误的行政区划，比如第 3 行的 “河北，承德，栾平县”，程序有一定的可能会返回错误的匹配并不做标识；
4. 难以通过字面文本确实的行政区，比如上述第 27 行的“云龙”，程序会随机返回一个同名的行政区且不会做标识；

上述问题在当前数据集之中并不广泛，但在历史数据集之中可能有一定的存量，使用中需要予以注意。

#### 3.2.4 日期、时间的清洗和转换

```python
collections.df['Time'].head(20) 

Out: 
0         2       X  1973
1             9  IX  1973
2           24 Aug., 2000
3                    1982
4              VIII  1982
5     2012-12-31 00:00:00
6                 60.6.30
7                   10995
8               1983.9.13
9                 4X'1994
10             VIII' 1963
11               4-6-1982
12    2013-03-30 12:30:09
13               19820431
14                 82.9.8
15               VI  1978
16                   9.12
17                2007.06
18             2007.07.13
19            12 IV' 1987
Name: Time, dtype: object
```
转换为国内目前广泛采用的 8 位整数型日期的：

```python
# 使用时直接传递所要清洗的日期列的列名给 foramt_datetime 方法
# 此外 style 参数可指定清洗后输出的日期格式，可传递的值目前有 "num"/"date"/"datetime"/"utc"，默认为 “datetime”样式
# mark 参数可指定是否返回带有!标记的错误日期，如果为 False，错误日期返回 None
# inplace 参数指定是否直接将转换后的列替代原数据表中的列
collections.format_datetime("Time", style='num', mark=True, inplace=False)

Out:
         Time
0    19731002
1    19730909
2    20000824
3    19820000
4    19820800
5    20121231
6    19600630
7      !10995
8    19830913
9    19941004
10   19630800
11   19820604
12   20130330
13  !19820431
14   19820908
15   19780600
16      !9.12
17   20070600
18   20070713
19   19870412
```

对于错误的日期，如果 `mark` 参数为 `True` ，会使用英文 “!” 标注返回。

转换为日期时间格式：

```python
collections.format_datetime("Time", style='datetime', mark=True, inplace=False)

Out:
                   Time
0    1973-10-2 00:00:00
1     1973-9-9 00:00:00
2    2000-8-24 00:00:00
3   1982-01-01 00:00:02
4    1982-8-01 00:00:01
5   2012-12-31 00:00:00
6    1960-6-30 00:00:00
7                !10995
8    1983-9-13 00:00:00
9    1994-10-4 00:00:00
10   1963-8-01 00:00:01
11    1982-6-4 00:00:00
12  2013-03-30 12:30:09
13            !19820431
14    1982-9-8 00:00:00
15   1978-6-01 00:00:01
16                !9.12
17   2007-6-01 00:00:01
18   2007-7-13 00:00:00
19   1987-4-12 00:00:00

```

如果原始数据没有时间，则补充"00:00:00"；如果原始数据没有day，则默认以每月 1 号 00:00:01 表示；如果原始数据缺失月份，则默认以该年 1 月 1 日 00:00:02 表示。这种转换方式可以将所有有效的日期和时间转换为规范可统计的时期时间文本。

转换为日期格式：

郗建勋. (1982). *55* (2020 ed.). 昆明: 中国科学院昆明植物研究所标本馆.

郗建勋.55* (2020 ed.). str: 昆明:中国科学院昆明植物研究所标本馆.

```python
collections.format_datetime("Time", style='date', mark=True, inplace=False)

Out:
          Time
0    1973-10-2
1     1973-9-9
2    2000-8-24
3   1982-01-01
4    1982-8-01
5   2012-12-31
6    1960-6-31
7       !10995
8    1983-9-13
9    1994-10-4
10   1963-8-01
11    1982-6-4
12   2013-3-30
13   !19820431
14    1982-9-8
15   1978-6-01
16       !9.12
17   2007-6-01
18   2007-7-13
19   1987-4-12
```

转换为 UTC 时间：

```python
# 默认为东八区时间，可以通过 timezone 参数手动指定，指定样式类似“+07:00”
collections.format_datetime("Time", style='utc', mark=True, inplace=False)

Out:
                         Time
0   1973-10-02T00:00:00+08:00
1   1973-09-09T00:00:00+08:00
2   2000-08-24T00:00:00+08:00
3   1982-01-01T00:00:02+08:00
4   1982-08-01T00:00:01+08:00
5   2012-12-31T00:00:00+08:00
6   1960-06-30T00:00:00+08:00
7                      !10995
8   1983-09-13T00:00:00+08:00
9   1994-10-04T00:00:00+08:00
10  1963-08-01T00:00:01+08:00
11  1982-06-04T00:00:00+08:00
12  2013-03-30T12:30:09+08:00
13                  !19820431
14  1982-09-08T00:00:00+08:00
15  1978-06-01T00:00:01+08:00
16                      !9.12
17  2007-06-01T00:00:01+08:00
18  2007-07-13T00:00:00+08:00
19  1987-04-12T00:00:00+08:00
```

UTC 世界协调时在处理和分析跨时区生物多样性数据时，具有明显优势。同时 UTC 时间支持`JSON Schema`时间样式，非常利于 web 平台间的数据交换。

#### 3.2.5 经纬度清洗和转换

经纬度数据是物种分布信息最为关键的信息，`FormatDataSet`实例为此提供了严格可靠的数据清洗方法 `format_latlon`，该方法既能最大限度的执行数据的自动清洗和转换，又能实现百分之百的数据纠错：

```python
collections.df['GPS'].head(20)     

Out[10]: 
0                N:28 34'478E:99 49 129"
1                       N:27 55'E:99 36'
2              N:38 34'481"E:099 50'054"
3     N: 24°35'51.22"; E: 100°04 '52.96"
4             N 31°04′206″, E 96°58′476″
5             N:26°14.636′，E:101°25.765′
6             24°53′01.78N,100°20′48.47E
7      N: 26°21'16.08", E: 103°01'43.76"
8               28 34'481",E:099 50'054"
9                         28 34'E:99 49'
10               27 20'356"N,100'04'776"
11                                   NaN
12                                   NaN
13                                   NaN
14               N:42.2354°, E:123.6607°
15                   N: 28 20', E:99 04'
16               41˚56'00"N, 123˚40'40"E
17      10˚37'08.83" N, 104˚01'53.97" E 
18                   S:64°41′, W: 62°37′
19                 48 45.9"N, 142 17.3'E
Name: GPS, dtype: object

```
调用`format_latlon`方法时需要将经纬度涉及的一列或两列的列名传递给该方法，`format_latlon`会自动纠正错位的经度和纬度，表格中经纬度的书写可以是十进制格式、度分格式、度分秒格式，或者以上几种的混合，数字之间的分隔符也没有统一的要求。

```python
collections.format_latlon("GPS", inplace=False)

Out:
   decimalLatitude decimalLongitude
0          28.5746          99.8188
1          27.9167             99.6
2          38.5747          99.8342
3          24.5976          100.081
4          31.0701          96.9746
5          26.2439          101.429
6          24.8838          100.347
7          26.3545          103.029
8              !28              !34
9              !28              !34
10             !27              !20
11            None             None
12            None             None
13            None             None
14         42.2354          123.661
15         28.3333          99.0667
16         41.9333          123.678
17         10.6191          104.032
18        -64.6833         -62.6167
19          48.765          142.288
```

#### 3.2.6 数值及数值区间的清洗和转换

`format_number` 函数可以对各种文本样式的数列或数值区间列进行处理，并以纯粹的整数或浮点数为元素返回 `list` 结果或者直接生成全新的数据列。

```python
# 首先预览一下海拔数据的情况
collections.df['altitude'].head(20) 

Out: 
0            NaN
1          大约10m
2      大概400-600
3            NaN
4          3800米
5         3800 m
6      1400～1800
7      1200-1300
8           1000
9           1250
10           700
11           700
12          1200
13           620
14       400+600
15       400-600
16       400～600
17       400-600
18       400-600
19       400—600
Name: 海拔, dtype: object
```
通过数据预览，可以发现 `collections` 实例的海拔属性是一个数值区间，而且区间间隔符号也不统一，有些数值还带有计量单位或者其他文本字符。`format_number` 方法在处理单个数列时，只需传递该列列名即可。但在处理数值区间时，就需要接受两个实际可调用的表格字段名，因此我们首先需要将 `collections` 的 `altitude` 属性拆分位两列，拆分数据列可以调用 `split_column` 方法：

```python
# split_column 方法会在下文详解
# 这里将 altitude 按照 '-' 符号拆分为 minimumElevation 和 maximumElevation 两个新列
collections.split_column("altitude", "-", new_headers["minimumElevation","maximumElevation"]) 
  
# 可以发现单纯的列拆分并不能将所有的区间值分开
# 但是它可以为进一步使用 fromat_number 方法提供基础
  Out: 
           minimumElevation         maximumElevation
0                       NaN                     None
1                     大约10m                     None
2                     大概400                      600
3                       NaN                     None
4                     3800米                     None
5                    3800 m                     None
6                 1400～1800                     None
7                      1200                     1300
8                      1000                     None
9                      1250                     None
10                      700                     None
11                      700                     None
12                     1200                     None
13                      620                     None
14                  400+600                     None
15                      400                      600
16                  400～600                     None
17                      400                      600
18                      400                      600
19                  400—600                     None

```

将拆分出的两个新列传递给 `format_number` 方法，进行数值清洗：

```python
# 调用 format_number 方法清洗数据列
# 同时指定清洗结果为整形，也可以根据需要将其指定为 float
# 该方法默认会将清洗结果直接替换 df 属性中的相应数据列
# 如果不希望直接替换被处理的数据列，可以在调用时设置 inplace=False
# 此外对于非法数值，该方法默认会在返回结果中删除该值，并以空值填充
# 如果希望标记和保留非法数值，可以在调用时设置 mark=True
collections.format_number("minimumElevation", "maximumElevation", typ=int)

# 预览处理结果，可以发现原先的数值区间已经准确的进行了拆分
# 原先的单个数值，则会默认补充为同值区间，以保证拆分结果的一致性
collections.df[["minimumElevation", "maximumElevation"]].head(20)  

Out: 
            minimumElevation          maximumElevation
0                       <NA>                      <NA>
1                         10                        10
2                        400                       600
3                       <NA>                      <NA>
4                       3800                      3800
5                       3800                      3800
6                       1400                      1800
7                       1200                      1300
8                       1000                      1000
9                       1250                      1250
10                       700                       700
11                       700                       700
12                      1200                      1200
13                       620                       620
14                       400                       600
15                       400                       600
16                       400                       600
17                       400                       600
18                       400                       600
19                       400                       600

```



#### 3.2.7 重复值标注

`FormatDataSet` 提供了类似 Excel 的行值判重功能，该功能可以通过 `mark_repeat` 方法实现。

```python
collections.df[["标本号", "国别", "Time"]]

Out: 
        标本号  国别        Time
0    016589  中国        1978
1    016589  中国        1978
2    016589  中国    1983.5.8
3    019387  中国    1983.5.8
4    016108  中国  1982.08.24
..      ...  ..         ...
507  016675  中国  1983.11.29
508  016676  中国  1983.12.12
509  016677  中国  1983.12.18
510  016678  中国  1975.03.04
511  016965  中国  1984.11.29

[512 rows x 3 columns]

```
通过预览，可以发现 `collections` 有些字段的值是有重复的，`mark_repeat` 支持分别以单列和多列为依据进行重复值标记，比如：

```python
# 以标本号作为判重依据
collections.mark_repeat("标本号")                                                                                                                                                                  
# 查看标记结果
collections.df[["标本号", "国别", "Time"]]                                                                                                                                                         
Out[14]: 
         标本号  国别        Time
0    !016589  中国        1978
1    !016589  中国        1978
2    !016589  中国    1983.5.8
3     019387  中国    1983.5.8
4     016108  中国  1982.08.24
..       ...  ..         ...
507   016675  中国  1983.11.29
508   016676  中国  1983.12.12
509   016677  中国  1983.12.18
510   016678  中国  1975.03.04
511   016965  中国  1984.11.29

[512 rows x 3 columns]

```

如上所示：凡是重复的标本号都会以英文 "!" 标注。如果想要以多个列值为依据进行重复值标记，可以在调用`mark_repeat`时提供多个真实可调用的列名即可：

```python
# 以标本号、国别、Time 三个字段联合判重
collections.mark_repeat("标本号", "国别", "Time")                                                                                                                                                  

# 查看标记结果
collections.df[["标本号", "国别", "Time"]]                                                                                                                                                         
Out: 
         标本号  国别        Time
0    !016589  中国        1978
1    !016589  中国        1978
2     016589  中国    1983.5.8
3     019387  中国    1983.5.8
4     016108  中国  1982.08.24
..       ...  ..         ...
507   016675  中国  1983.11.29
508   016676  中国  1983.12.12
509   016677  中国  1983.12.18
510   016678  中国  1975.03.04
511   016965  中国  1984.11.29

[512 rows x 3 columns]

```

通过多个列名进行重复值标记，程序只会标注那些相应字段均重复的数据。

#### 3.2.8 数据列的分割

`split_column`方法可以对文本数据列进行分拆操作，该方法与常用的文本列分割方法有些不同：`split_column` 的方法一次性可以接受多个不同分隔符进行分拆操作，但每个分隔符只能作用于一次拆分，比如 下方 `collections` 的`cite1` 字段是一个引文内容，各引文成分之间分别使用了","和":"进行了分割。

```python
collections.df["cite1"].head(10)                                                                                                                                                                   
Out: 
0           Linnaeus, 1758,Syst. Nat.,ed. 10,1:159
1      Sharpe,1894,Cat. Bds. Brit. Mus. 23:250,252
2                 Buturlin, 1916, OpH. Becth.7:224
3           Jerdon et Blyth, 1864, Bds. Ind. 3:648
4                         Riley, 1925, Auk 42: 423
5    Boddaert,17348,Tabl. Pl. enlum. Hist. Nat.:54
6         Swinhoe,1871,Proc. Zool. Soc. London:401
7                  Hartert,1917, Nov. Zool. 24:272
8         Swinhoe,1871,Proc. Zool. Soc. London:401
9                                              NaN
Name: cite1, dtype: object

```

如果想要将其拆分为作者、年代、杂志卷期、页码四列数据，就需要根据数据情况传递两个","分割符和一个":"分割符给`split_column`方法。

```python
collections.split_column("cite1", (",",",",":"), new_headers=["author", "year", "from", "page"])                                                                                                   

collections.df[["author", "year", "from", "page"]].head(10)                                                                                                                                        
Out: 
            author   year                         from     page
0         Linnaeus   1758          Syst. Nat.,ed. 10,1      159
1           Sharpe   1894      Cat. Bds. Brit. Mus. 23  250,252
2         Buturlin   1916                 OpH. Becth.7      224
3  Jerdon et Blyth   1864                  Bds. Ind. 3      648
4            Riley   1925                       Auk 42      423
5         Boddaert  17348  Tabl. Pl. enlum. Hist. Nat.       54
6          Swinhoe   1871      Proc. Zool. Soc. London      401
7          Hartert   1917                Nov. Zool. 24      272
8          Swinhoe   1871      Proc. Zool. Soc. London      401
9              NaN   None                         None     None

```

其中`split_column`方法第一个参数为需要拆分的列名。第二个参数为拆分所依据的分隔符，可以是字符（只根据该字符拆分一次），也可以是字符组成的元组（每个字符按序拆分一次）。第三个参数用于设置拆分后新列的列名，如果缺省，则返回 `list` 数据而不改变实例 `df` 属性的数据列；如果给予，则会直接改写`df`属性相应的数据列。上面示例子就是将原 `collections.df` 中 `cite`数据列改为了`author`，`year`, `from`,  `page` 四列。

`split_column` 方法同时支持中西文拆分，使用时可以将分隔符设为单个"$"符号或多个该符号组成的元组：

```python
collections.df["鉴定"].head(10)                                                                                                                                                                     
Out: 
0                                          青海二色香青
1                  白苞蒿Artemisia lactiflora Walld.
2                     马蛋果Gynocardia odorata Roxb.
3                  高山大风子Hydnocarpus alpinus Wight
4    海南大风子Hydnocarpus hainanensis (Merr.) Sleumer
5                   针叶韭Allium aciphyllum J. M. Xu
6                           llium caeruleum Pall.
7              滇南黄杨Buxus austroyunnanensis Hatus.
8                      雀舌黄杨uxus bodinieri H. Lév.
9                                             NaN
Name: 鉴定, dtype: object

```

拆分结果：

```python
collections.split_column("鉴定", "$", new_headers=["中文名", "拉丁名"])

collections.df[["中文名", "拉丁名"]].head(10)
Out: 
                     中文名                                      拉丁名
0                 青海二色香青                                         
1                    白苞蒿              Artemisia lactiflora Walld.
2                    马蛋果                 Gynocardia odorata Roxb.
3                  高山大风子                Hydnocarpus alpinus Wight
4                  海南大风子  Hydnocarpus hainanensis (Merr.) Sleumer
5                    针叶韭               Allium aciphyllum J. M. Xu
6  llium caeruleum Pall.                                         
7                   滇南黄杨           Buxus austroyunnanensis Hatus.
8                   雀舌黄杨                   uxus bodinieri H. Lév.
9                    NaN                                     None

```



#### 3.2.9 数据列的合并

`merge_columns` 方法支持多列按序拼接合并，合并分隔符可以是同样的字符：

```python
# 统一使用 : 号拼接上述被拆分的引文内容
# 调用 merge_columns 方法如果不传递 new_header 参数
# 则默认返回 list 类型的拼接结果，但不改变 df 属性的值
collections.merge_columns(["author", "year", "from", "page"], ":") 

['Linnaeus: 1758:Syst. Nat.,ed. 10,1:159',
 'Sharpe:1894:Cat. Bds. Brit. Mus. 23:250,252',
 'Buturlin: 1916: OpH. Becth.7:224',
 'Jerdon et Blyth: 1864: Bds. Ind. 3:648',
 'Riley: 1925: Auk 42: 423',
 'Boddaert:17348:Tabl. Pl. enlum. Hist. Nat.:54',
 'Swinhoe:1871:Proc. Zool. Soc. London:401',
 'Hartert:1917: Nov. Zool. 24:272',
 'Swinhoe:1871:Proc. Zool. Soc. London:401',
 None]
```

也可以使用不同的字符拼接各列：

```python
collections.merge_columns(["author", "year", "from", "page"], (",", "; ", ":"))

Out:
['Linnaeus, 1758; Syst. Nat.,ed. 10,1:159',
 'Sharpe,1894; Cat. Bds. Brit. Mus. 23:250,252',
 'Buturlin, 1916;  OpH. Becth.7:224',
 'Jerdon et Blyth, 1864;  Bds. Ind. 3:648',
 'Riley, 1925;  Auk 42: 423',
 'Boddaert,17348; Tabl. Pl. enlum. Hist. Nat.:54',
 'Swinhoe,1871; Proc. Zool. Soc. London:401',
 'Hartert,1917;  Nov. Zool. 24:272',
 'Swinhoe,1871; Proc. Zool. Soc. London:401',
 None]

```

如果调用 `merge_columns` 时指定了 `new_header` 属性，则返回 None ，同时会将处理结果直接合并到实例的 df 属性中，并删除参与合并的列：

```python
# 指定 new_header 后，方法执行后返回 None
collections.merge_columns(["author", "year", "from", "page"], (",", "; ", ":") ,new_header="cite")                                                                                                 

# df 属性中新增了合并的 cite 数据列，同时 author, year, from, page 列会被删除
collections.df['cite'].head(10)                                                                                                                                                                              
Out: 
0           Linnaeus, 1758; Syst. Nat.,ed. 10,1:159
1      Sharpe,1894; Cat. Bds. Brit. Mus. 23:250,252
2                 Buturlin, 1916;  OpH. Becth.7:224
3           Jerdon et Blyth, 1864;  Bds. Ind. 3:648
4                         Riley, 1925;  Auk 42: 423
5    Boddaert,17348; Tabl. Pl. enlum. Hist. Nat.:54
6         Swinhoe,1871; Proc. Zool. Soc. London:401
7                  Hartert,1917;  Nov. Zool. 24:272
8         Swinhoe,1871; Proc. Zool. Soc. London:401
9                                              None
Name: cite, dtype: object


```

`merge_columns` 方法不仅可以对数据列进行按序拼接，还可以将不同数据列组合为带 title 的换行文本，或者组装为 Python 的 `Dict` 和 `list`， JSON 的 `Object` 和 `Array` 对象。如果需要这样做，可以在传递参数时，将分隔符参数按需设置为 "r"、"d"、"l"、"o"、"a" 中的某一个，即可将拼接结果转换为相应的形式。这里仍以上述已分列的引文数据为例：

```python
# 合并为带title的换行文本
collections.merge_columns(["author", "year", "from", "page"], "r")

# 组合结果带有字段 Title，且每个组合都以换行符\n 分开，这种形式非常适合需要带title的打印文本输出
Out:
['author：Linnaeus\nyear： 1758\nfrom：Syst. Nat.,ed. 10,1\npage：159',
 'author：Sharpe\nyear：1894\nfrom：Cat. Bds. Brit. Mus. 23\npage：250,252',
 'author：Buturlin\nyear： 1916\nfrom： OpH. Becth.7\npage：224',
 'author：Jerdon et Blyth\nyear： 1864\nfrom： Bds. Ind. 3\npage：648',
 'author：Riley\nyear： 1925\nfrom： Auk 42\npage： 423',
 'author：Boddaert\nyear：17348\nfrom：Tabl. Pl. enlum. Hist. Nat.\npage：54',
 'author：Swinhoe\nyear：1871\nfrom：Proc. Zool. Soc. London\npage：401',
 'author：Hartert\nyear：1917\nfrom： Nov. Zool. 24\npage：272',
 'author：Swinhoe\nyear：1871\nfrom：Proc. Zool. Soc. London\npage：401',
 None]


# 将每一组合并为 python 的 dict 对象 
collections.merge_columns(["author", "year", "from", "page"], "d")      

Out: 
[{'author': 'Linnaeus',
  'year': ' 1758',
  'from': 'Syst. Nat.,ed. 10,1',
  'page': '159'},
 {'author': 'Sharpe',
  'year': '1894',
  'from': 'Cat. Bds. Brit. Mus. 23',
  'page': '250,252'},
 {'author': 'Buturlin',
  'year': ' 1916',
  'from': ' OpH. Becth.7',
  'page': '224'},
 {'author': 'Jerdon et Blyth',
  'year': ' 1864',
  'from': ' Bds. Ind. 3',
  'page': '648'},
 {'author': 'Riley', 'year': ' 1925', 'from': ' Auk 42', 'page': ' 423'},
 {'author': 'Boddaert',
  'year': '17348',
  'from': 'Tabl. Pl. enlum. Hist. Nat.',
  'page': '54'},
 {'author': 'Swinhoe',
  'year': '1871',
  'from': 'Proc. Zool. Soc. London',
  'page': '401'},
 {'author': 'Hartert',
  'year': '1917',
  'from': ' Nov. Zool. 24',
  'page': '272'},
 {'author': 'Swinhoe',
  'year': '1871',
  'from': 'Proc. Zool. Soc. London',
  'page': '401'},
 None]


# 合并为 Python 的 list 对象
collections.merge_columns(["author", "year", "from", "page"], "l")

Out: 
[['Linnaeus', ' 1758', 'Syst. Nat.,ed. 10,1', '159'],
 ['Sharpe', '1894', 'Cat. Bds. Brit. Mus. 23', '250,252'],
 ['Buturlin', ' 1916', ' OpH. Becth.7', '224'],
 ['Jerdon et Blyth', ' 1864', ' Bds. Ind. 3', '648'],
 ['Riley', ' 1925', ' Auk 42', ' 423'],
 ['Boddaert', '17348', 'Tabl. Pl. enlum. Hist. Nat.', '54'],
 ['Swinhoe', '1871', 'Proc. Zool. Soc. London', '401'],
 ['Hartert', '1917', ' Nov. Zool. 24', '272'],
 ['Swinhoe', '1871', 'Proc. Zool. Soc. London', '401'],
 None]


# 合并为 Json 的 Object 对象
collections.merge_columns(["author", "year", "from", "page"], "o")

Out: 
['{"author": "Linnaeus", "year": " 1758", "from": "Syst. Nat.,ed. 10,1", "page": "159"}',
 '{"author": "Sharpe", "year": "1894", "from": "Cat. Bds. Brit. Mus. 23", "page": "250,252"}',
 '{"author": "Buturlin", "year": " 1916", "from": " OpH. Becth.7", "page": "224"}',
 '{"author": "Jerdon et Blyth", "year": " 1864", "from": " Bds. Ind. 3", "page": "648"}',
 '{"author": "Riley", "year": " 1925", "from": " Auk 42", "page": " 423"}',
 '{"author": "Boddaert", "year": "17348", "from": "Tabl. Pl. enlum. Hist. Nat.", "page": "54"}',
 '{"author": "Swinhoe", "year": "1871", "from": "Proc. Zool. Soc. London", "page": "401"}',
 '{"author": "Hartert", "year": "1917", "from": " Nov. Zool. 24", "page": "272"}',
 '{"author": "Swinhoe", "year": "1871", "from": "Proc. Zool. Soc. London", "page": "401"}',
 None]


# 合并为 JSON 的 Array 对象
collections.merge_columns(["author", "year", "from", "page"], "a")

Out: 
['["Linnaeus", " 1758", "Syst. Nat.,ed. 10,1", "159"]',
 '["Sharpe", "1894", "Cat. Bds. Brit. Mus. 23", "250,252"]',
 '["Buturlin", " 1916", " OpH. Becth.7", "224"]',
 '["Jerdon et Blyth", " 1864", " Bds. Ind. 3", "648"]',
 '["Riley", " 1925", " Auk 42", " 423"]',
 '["Boddaert", "17348", "Tabl. Pl. enlum. Hist. Nat.", "54"]',
 '["Swinhoe", "1871", "Proc. Zool. Soc. London", "401"]',
 '["Hartert", "1917", " Nov. Zool. 24", "272"]',
 '["Swinhoe", "1871", "Proc. Zool. Soc. London", "401"]',
 None]

```





## 四、自定义数据模型

### 4.1 特定数据集的结构重塑

将某一特定结构的数据集转换为另一种具有特定字段和结构的数据集，通常会涉及一系列的数据拆分、合并和字段更名。`ipybd` 将不同的数据集结构视为不同的数据模型，通过自定义数据模型，可以实现数据集结构的自动转换。这里以国内植物标本数据集中广泛使用的 CVH 数据表为例，CVH 的数据表包含了："采集人"、"采集号"、"采集日期"、"国家"、"省市"、“区县”、"属"、"种"、“命名人”、"种下等级"、“种下等级命名人” 等一系列字段。如果我们只想从 CVH 的数据表中提取一个包含"记录入"、"记录编号"、"记录时间"、"省"、"市"、"学名" （不含命名人）六个概要性字段的数据表，就可以通过 `ipybd` 自定义模型实现 CVH 数据表的自动转换：

```python
from ipybd import imodel    
from enum import Enum

@imodel 
class MyModel(Enum):
    记录人 = '$采集人'   
    记录编号 = '$采集号' 
    记录时间 = '$采集日期'
    省_市 = {'$省市':','}  
    学名 = ('$属', '$种', '$种下等级', ' ')  

```

上面代码通过 `ipybd` 的 `imodel` 修饰符，修饰了一个符合 `ipybd` 模型语义规范的枚举类。这样就可以生成一个自定义的 `MyModel` 数据模型。模型定义中，枚举元素的 `name` 为新数据集的字段名，枚举元素的 `value` 为该字段指代的数据对象。单纯的结构重塑中 `value` 的表达通常涉及三类操作：

+ **字段更名**：原数据集中的数据对象不用做任何改变，直接更名后映射到新的数据集中。上面代码中的`采集人`,`采集号`,`采集日期`就是通过更名表达式重新映射为新数据集中的`记录人`,`记录编号`,`记录时间`。其中`$` 前缀在 `ipybd` 模型中用于修饰一个数据对象，带有该前缀的字符串会被认为是一个数据对象参数。因此 `记录人 = $采集人` 这样的表达式表示的就是：将原数据集中的`采集人`数据对象转换为新数据集中的`记录人`数据对象。

+ **数据拆分**：将原数据集中的单个数据对象拆分为新数据集中的多个数据对象。`ipybd` 通过 `dict` 类型表达拆分，比如上面代码中的 `省_市 = {'$省市':','}`  就是表示：将原数据集中的`省市`数据对象通过 `,` 分隔符拆分为新数据集中的`省`,`市`两个新数据对象。其中新分出的列名之间要以 `_` 进行连接。需要注意的是单个 `str` 分隔符只会参与一次拆分，如果想要进行多次拆分，可以将多个分隔符组装为一个 `tuple` ，比如 `国_省_市_县 = {行政区划:(";", ";",";")}` 或者 `国_省_市_县 = {行政区划:(";",)*3}` 就表示将`行政区划`拆分为`国`,`省`,`市`,`县`四个新数据对象。

+ **数据合并**：将原数据集中的多个数据对象合并为单个数据对象。 `ipybd` 通过 `tuple` 类型表达合并，比如上面代码中的

  `学名 = ('$属', '$种', '$种下等级', ' ') ` 就表示将原数据集中的`属`,`种`,`种下等级`数据通过空格按序连接，合并为新数据集中的`学名`字段。合并表达式的最后一个元素为各个字段间的连接符。

当用一个 CVH Excel 数据表实例化 `MyModel` 对象， 它会在内存中自动完成数据表结构的转换，生成一个符合模型定义的新的数据表：

```python
cvh = MyModel(r"/Users/.../cvh.xlsx") 

cvh.df.head()                                                                                                                                                          
Out: 
            记录人                    记录编号      记录时间    省     市                          学名
0    王雷,朱雅娟,黄振英    Beijing-huang-dls-0026  20070922   北京   北京市     Ostericum grosseserratum
1           NaN                      YDDXSC-022  20071028  云南省   临沧市   Boenninghausenia albiflora
2  欧阳红才,穆勤学,奎文康                YDDXSC-022  20071028  NaN     None   Boenninghausenia albiflora
3   吴福川,查学州,余祥洪                      NaN   20070512  湖南省  张家界市       Broussonetia kazinoki
4   吴福川,查学州,余祥洪               SCSB-07009   20070512  湖南省  张家界市       Broussonetia kazinoki

```

`MyModel` 模型默认会自动去除模型中未定义的数据对象，如果想要保留原始数据集中其他数据对象，可以在调用时传递相应关键字：

```python
cvh = MyModel(r"/Users/.../cvh.xlsx", cut=False) 
```

此外通过 `@imodel` 修饰生成的数据模型，都是前述 `FormatDataSet` 的子类，因此 `FormatDataSet` 实例具有的方法都可以被直接调用。

### 4.2 众源数据集的结构重塑

除了针对特定数据集进行结构的转换，`ipybd` 数据模型还支持不同数据源到特定数据集结构的转换。相应数据模型的定义与上述数据模型的定义稍有不同：

```python
from enum import Enum
from ipybd import imodel

@imodel  
class MySmartModel(Enum):  
    记录人 = '$recordedBy'  
    记录编号 = '$recordNumber'  
    采集日期 = '$eventDate' 
    省_市 = {('$province', '$city'): ','} 
    学名 = ['$scientificName',  ('$genus', '$specificEpithet', '$taxonRank', '$infraspecificEpithet', ' ')] 
    
```

如上所示，对于具备众源映射功能的 `ipybd` 数据模型，其数据对象的表达不再是具体真实的字段名，而是采用了一套新的标准字段名以指代同一语义的不同字段名。比如上面代码中的 "recordedBy" 就可以同时指代"采集人"、"采集人员"、"记录人"、"记录者"、"记录人员"、"COLLECTOR" .... 等一批不同名称但意义相同的字段。`ipybd` 就是依靠这种标准字段名称的泛化关系实现了众源数据集结构的转换。目前 `ipybd` 的标准字段名称库主要基于 [DarwinCore](https://dwc.tdwg.org/terms/) 定义，其中也有部分字段名称会根据国内数据的现实情况，做了一些调整和扩展。标准字段名称库相当于一类对象的基本构成元素库。使用它进行模型的定义，会存在一些特殊的表达方式，比如上述 `MySmartModel` 模型中：

```python
省_市 = {('$province', '$city'): ','} 
```

与 `MyModel` 中`$省市`的表达方式就有很大差异：

```python
省_市 = {'$省市':','}
```

这是因为在 `ipybd` 的标准字段名称库中，并不存在与"省市"完全等同的标准字段名，而只存在 "province" 和 "city" 两个标准字段名。显然“省市”应该是由"province"和"city"两个标准字段组合而成，因此在 `MySmartModel`中就需要以表示合并语义的元组表达`$省市`这个概念。

同理，对于`学名`的表示：

```python
学名 = ['$scientificName',  ('$genus', '$specificEpithet', '$taxonRank', '$infraspecificEpithet', ' ')]
```

上面 `list` 中第二个元素，是表示学名也可能是由 “genus”、“specificEpithet”、“taxonRank”、“infraspecificEpithet” 四个字段以空格相间的形式组合而成。相对于 `dict` 表示元素拆分，`tuple` 表示元素的合并，**`list` 在 `ipybd` 语义中则表示按需优先选择**，因此上面这行代码在 `ipybd` 数据模型定义中表示：`学名`优先采用与 `scientificName` 相对应数据对象，其次使用由多个字段组合而成的数据对象。

通过这种形式，`ipybd` 数据模型不仅可以很好的处理同一对象不同名称的映射，还可以实现同一对象不同结构形式的映射，从而获得更加通用的数据自动处理能力。

具备字段映射能力的数据模型，不仅可以处理与模型结构相对应的数据集结构转换：

```python
# 3.1 中示范的 cvh 数据表，仍然可以通过 MySmartModel 执行数据结构转换
# 需要注意的是使用映射功能的模型，在调用时需要设置 fields_mapping=True
cvh = MySmartModel(r"/Users/.../cvh.xlsx", fields_mapping=True) 
cvh.df.head()

Out: 
            记录人                    记录编号      记录时间    省     市                          学名
0    王雷,朱雅娟,黄振英    Beijing-huang-dls-0026  20070922   北京   北京市      Ostericum grosseserratum
1           NaN                      YDDXSC-022  20071028  云南省   临沧市   Boenninghausenia albiflora
2  欧阳红才,穆勤学,奎文康                YDDXSC-022  20071028  NaN     None    Boenninghausenia albiflora
3   吴福川,查学州,余祥洪                      NaN   20070512  湖南省  张家界市       Broussonetia kazinoki
4   吴福川,查学州,余祥洪               SCSB-07009   20070512  湖南省  张家界市       Broussonetia kazinoki

```

还可以实现不同数据源的数据结构转换，比如不同于 CVH 的数据表，biotracks.cn 导出的数据集中省、市是分列的，同时学名也不是分开的，而是聚合的。这种差异明显的数据集，仍然可以通过 `MySmartModel` 实现结构的转换。
```python
bio = MySmartModel(r'/Users/.../biotracks.xlsx', fields_mapping=True)
bio.df.head()

Out: 
          记录人      记录编号        采集日期             省              市                  学名
0  刘恩德|182,徐洲锋|28  9125  2019-08-09 12:29:00  新疆维吾尔自治区 吐鲁番地                             Clematis
1  刘恩德|182,徐洲锋|28  9126  2019-08-09 12:33:00  新疆维吾尔自治区 吐鲁番地                               Crepis
2  刘恩德|182,徐洲锋|28  9128  2019-08-09 14:02:00  新疆维吾尔自治区 巴音郭楞蒙古自治州 Krascheninnikovia ceratoides
3  刘恩德|182,徐洲锋|28  9132  2019-08-09 14:25:00  新疆维吾尔自治区 巴音郭楞蒙古自治州                     Apiaceae
4  刘恩德|182,徐洲锋|28  9136  2019-08-09 15:01:00  新疆维吾尔自治区 巴音郭楞蒙古自治州                    Boerhavia

```

### 4.3 具有值处理功能的数据模型定义

简单的字段名映射、数据拆分与合并可以应付数值规范的数据集结构重塑，但在面对各种来源的人工数据集时，单纯的数据集结构重塑还是无法满足数据的强一致性需求。事实上对于众源数据，尤其是人工梳理的数据，其数据结构和值的规范其实很难保证严格一致。比如下方所示的物种分布数据集：

```python
import pandas

dirtydata = pandas.read_excel(r"/Users/.../dirtydata.xlsx")
dirtydata.head()

Out:
                              鉴定                       坐标        日期     海拔      国别  产地1   产地2
0                 Trifolium repens   N:28 34'478E:99 49 129"        1978   1400～1800  中国  HEB  承德市
1                        Lauraceae  N:27 55'E:99 36'          1982.08.24    1200-1300  中国   HN    吉首
2                      Glycine max  N:38 34'481"E:099 50'054"   19830500  大概400-600   中国   YN  勐腊县
3                     Glycine  N:24°35'51.22"; E:100°04 '52.96" 1983.6.31  1000-1100m  中国   YN    大理
4  Rubia cordifolia var. sylvatica  N 31°04′206″, E 96°58′476″    19820824     3800米  中国   SC  德格县

```

该数据集所有的内容采用的都是文本格式，同时经纬度、日期、海拔的写法也不统一，学名没有命名人，省份使用的是拼音简写...这样的人工数据集虽然表意明确，但对于数据库归档、数据分析等真正的应用而言，仍然是无法直接利用的。`ipybd` 可以定义具备严格数据类型校验和转换功能的数据模型，以自动化的处理这类脏数据梳理的问题：

```python

from ipybd import imodel
from ipybd import BioName, GeoCoordinate, DateTime, Nmber, AdminDiv
from enum import Enum

@imodel
class DataCleaner(Enum):  
    拉丁名 = BioName('$鉴定', style='scientificName')  # style 关键字参数指名返回带有完整命名人的学名
    经度_纬度 = GeoCoordinate('$坐标') 
    采集日期 = DateTime('$日期') 
    海拔1_海拔2 = {'$海拔':'-'} 
    海拔_海拔高 = Number('$海拔1', '$海拔2', int)   # int 位置参数指明返回 int 类型的数值
    国_省_市_县 = AdminDiv(('$国别', '$产地1', '$产地2', ','))  # ipybd 定义的元组等表达式都可以作为单个参数进行传递                                                                                                                                                             
```

相对于简单的结构转换模型，`DataCleaner` 的枚举值中不仅定义了`$`修饰的数据对象，还将这些对象传递给了诸如 `BioName` 这样的 `ipybd` 数据类。这些类会自动清洗相应的数据，并返回符合预期的结果：

```python
cleandata = DataCleaner(r"/Users/.../dirtydata.xlsx")
cleandata.df.head()                                                                                                                                                       
Out: 
             拉丁名           经度       纬度    采集日期    海拔  海拔高   国     省                 市      县
0 Trifolium repens L.      28.5746  99.8188    19780000  1400  1800  中国  河北省              承德市   None
1 Lauraceae                27.9167     99.6    19820824  1200  1300  中国  湖南省  湘西土家族苗族自治州   吉首市
2 Glycine max (L.) Merr.   38.5747  99.8342    19830500   400   600  中国  云南省    西双版纳傣族自治州   勐腊县
3 Glycine                  24.5976  100.081  !1983.6.31  1000  1100  中国  云南省       大理白族自治州   None
4 !Rubia cordifolia var. sylvatica 31.0701  96.9746 19820824  3800  3800  中国  四川省  甘孜藏族自治州   德格县
```

相比于原始的数据集，经过 `DataClean` 清洗的数据，值的格式更加规范统一，其中经纬度、海拔采用的是可以直接参与计算的十进制数值格式，行政区划也被拆分为标准的四级行政区，学名附带有完整的命名人。而对于可能存在错误的数据，`ipybd` 会使用 “!” 标识原始数据以便于核实（上述学名 Rubia cordifolia var. sylvatica 被标注主要是因为该名称在中国生物物种名录中是一个异名）。目前，`ipybd` 总共提供了以下十个内置值处理类以供用户使用，在 `ipybd` 数据模型中使用这些类，最终均会返回一个 `DataFrame` 对象：

+ `BioName(names: Union[list, pd.Series, tuple], style='scientificName')`

  清洗学名，其中 `style` 关键字参数表示返回后的学名样式，默认返回带有命名人的完整学名，也可以设置为 `simpleName` 返回去命名人的拉丁名。

+ `AdminDiv(address: Union[list, pd.Series, tuple])`

  清洗中文行政区划，最终返回由`country` `province` `city` `country` 组成的`DataFrame` 对象；对于非中文书写的地址会忽略。

+ `Number(min_column: Union[list, pd.Series, tuple], max_column: Union[list, pd.Series, tuple] = None, typ=float, min_num=0, max_num=8848)`

  清洗数值或数值区间，其`min_column`和`max_column`分别指定待处理数据的可迭代对象，`typ` 用于指定处理后返回的对象类型，支持 `int` 和 `float`，`min_num`和`max_num` 分别设置参与处理的数字大小下限和上限。

+ `GeoCoordinate(coordinates: Union[list, pd.Series, tuple])`

  严格的校验、清洗和转换经纬度，接收一个由经度和纬度组成的可迭代对象。

+ `DateTime(date_time: Union[list, pd.Series, tuple], style="num", timezone='+08:00')`

  清洗日期或带日期的时间，其中 `style` 指代最终返回的数据样式，默认输出文本纯数字日期，可重设为 `'utc'` `'date'` `'datetime'` 样式；`timezone` 为日期或时间所处的时区。

+ `HumanName(names: Union[list, pd.Series, tuple])`

+ 清洗人名，可以将中文人名中的空格清除，并判断长度的合理性，同时可以将常见西文人名的书写格式统一规范化。

+ `UniqueID(*columns: pd.Series)`

  标注重复值。

+ `FillNa(df: Union[pd.DataFrame, pd.Series], fval)`

  填充数据对象中的空值，`fval` 可设置要填充的值。

+ `Url(column: Union[list, pd.Series, tuple])`

  标注是否上一个 url 链接。

+ `RadioInput(column, title)`

  选值匹配，根据`ipybd` 内置的标准选值库检查数据对象的值是否标准，并尽力转换为标准值，无法自动转换的默认会执行手动对应引导。

### 4.4 具有值处理功能的映射数据模型定义

如同单纯的结构转换模型，带有值处理功能的数据模型也可以被定义为具备字段映射功能的数据模型，从而实现更广泛通用的数据处理能力：

```python
@imodel  
class SmartCleaner(Enum):  
    拉丁名 = BioName(['$scientificName', ('$genus', '$specificEpithet', '$taxonRank', '$infraspecificEpithet', ' ')], style='scientificName') 
    经度_纬度 = GeoCoordinate(('$decimalLatitude', '$decimalLongitude', ';')) 
    采集日期 = DateTime('$eventDate') 
    海拔_海拔高 = Number('$minimumElevationInMeters', '$maximumElevationInMeters', int) 
    国_省_市_县 = AdminDiv(('$country', '$province', '$city', '$county', ',')) 

```

输出：

```python
cleandata = SmartCleaner(r"/Users/.../dirtydata.xlsx", fields_mapping=True)
cleandata.df.head()                                                                                                                                                       
Out: 
             拉丁名           经度       纬度    采集日期    海拔  海拔高   国     省                 市      县
0 Trifolium repens L.      28.5746  99.8188    19780000  1400  1800  中国  河北省              承德市   None
1 Lauraceae                27.9167     99.6    19820824  1200  1300  中国  湖南省  湘西土家族苗族自治州   吉首市
2 Glycine max (L.) Merr.   38.5747  99.8342    19830500   400   600  中国  云南省    西双版纳傣族自治州   勐腊县
3 Glycine                  24.5976  100.081  !1983.6.31  1000  1100  中国  云南省       大理白族自治州   None
4 !Rubia cordifolia var. sylvatica 31.0701  96.9746 19820824  3800  3800  中国  四川省  甘孜藏族自治州   德格县
```





## 五、DarwinCore 模型

### 5.1 Occurrence

### 5.2 CVH

### 5.3 NSII

### 5.4 NOI

### 5.5 KingdoniaPlant

## 六、标签打印

```python
from ipybd import Label


printer = Label(r"/Users/.../record20201001.xlsx', repeat=2)
printer.write_html(start_code="KUN004123", page_num=8)
```



## 七、基于 Pandas 的数据统计与分析



## 八、特别声明

1. Ipybd 遵从 GNU General Public License v3.0 许可    
2. 本软件由 NSII 资助，© 徐洲锋，中国科学院昆明植物研究所
