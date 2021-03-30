# 数据标签配置

## 一、 功能概述

用户通过条件查询、快速查询、异步查询等方式将全部查询结果或选中的行数据生成用户自定义标签。用户再使用已经生成的数据标签，对数据进行筛选过滤，快速找到需要的数据。

## 二、 功能结构说明

数据标签由标签管理表sys_tag_manage、参数列表sys_Param_List、全局操纵Sys_Handle/Sys_Handles、元素表SYS_ELEMENT、调度信息表Xxl_Job_Info组成。

1. 标签管理表sys_tag_manage定义数据标签类型、数据标签名称以及数据源等功能。

2. 参数列表sys_Param_List定义数据标签UI界面输入项：如数据标签名称、数据标签字段等。

3. 全局操纵表Sys_Handle/Sys_Handles与标签管理表相对应定义数据标签字段，数据标签名称，主要功能是执行业务逻辑，生成数据标签数据。

4. 元素表SYS_ELEMENT定义该数据标签是否台步执行。当数据量达到系统规定上限时则后台执行该程序，此时用户可查询其他数据，当后台执行成功时会自动发送执行结果通知。

5. 调度信息表Xxl_Job_Info定义数据标签异步执行频率以及启停功能。

## 三、 功能配置

### 1.   **配置**标签管理表sys_tag_manage    

###  <img src="sanfu.jxmlxg.com\gitbook\images\006.png" style="zoom:80%;" />                    

- TAG_FIELD表示的是能生成数据标签的字段，该字段值与TAG_TYPE组成联合主键。根据过滤数据筛选字段展示对应的数据标签。

- TAG_TYPE与TAG_TYPE_NAME是用于表示数据标签第一层级树的列表。

- TREE_SQL是用于将数据标签分类展示为树形结构的SQL，即你在对应数据标签上设置的标签名称

  <img src="sanfu.jxmlxg.com\gitbook\images\009.png" style="zoom:80%;" />

- KEY_FIELD(树节点ID值字段名)、NAME_FIELD(节点展示名称字段)，PARENT_FIELD(该节点的上级节点ID)、LEAF_FIELD(该节点是否为叶子节点)，

- 该字段值必须与TREE_SQL语句字段值一致。

SELECT tag_id, tag_name, parent_id, is_leaf FROM sys_tag_info WHERE tag_field = 'SHO_ID' AND tag_type = '2'

![](sanfu.jxmlxg.com\gitbook\images\008.png)

- DATA_SQL则是取数据标签数据的业务SQL，该SQL会用于后续的业务SQL融合条件过滤，查询数据使用数据标签选择对应的标签名称的具体数据内容，只能返回单列值，不支持复杂SQL（不能写动态语句跟存储过程）。

SELECT data_value FROM sys_tag_data WHERE tag_id IN (&SHO_ID&)

### 2.   **配置**参数列表sys_Param_List

<img src="sanfu.jxmlxg.com\gitbook\images\009.png" style="zoom:80%;" />

![](sanfu.jxmlxg.com\gitbook\images\010.png)

- 标签字段：默认为需要配置当前数据标签的字段

- 所属分类：指对应的数据标签名称，当选择该标签名称时会自动展示出不能选择的标签名称。

  <img src="sanfu.jxmlxg.com\gitbook\images\011.png" style="zoom:80%;" />

- 标签名称：自定义属于自己名称，最好取有意义的名称，比如西南大区门店使用工号加+CQ【ZYS36_CQ】

- 是否追加：追加【在该基础上以增量的形式增加数据】，替换【将本次数据替换原有数据】

- 数据范围：全部【如SHO_ID门店数据标签，则是全公司门店】，选中【数据为所选数据

### 3.     配置全局操纵表Sys_Handle/Sys_Handles

handle的标题必须和数据标签表的命名一致：

<img src="sanfu.jxmlxg.com\gitbook\images\012.png" style="zoom:80%;" />

<img src="sanfu.jxmlxg.com\gitbook\images\013.png"  />

- 所属分类双\@变量为数据标签树形结构变量，业务可自行创建一张业务表存储树形结构，从里面获取数据：sys_tag_data、sys_tag_info表必须是当前数据源对应库的业务表，不可跨库使用。

- 配置数据标签Handle按钮

在handles的target_sql中引用双\@变量，并执行数据插入或修改SQL，根据页面选中为追加还是替换来判断。

 ![](sanfu.jxmlxg.com\gitbook\images\014.png)

- 上述查询为数据标签业务固定语法，如果不是数据标签业务双\@变量想引用树结构则可以自行替换。但是SQL返回结果字段需要包含以下两种固定语法的其中一种

- **适用于****SQL****：**TOP_KEY_FIELD、TOP_NAME_FIELD、TREE_SQL、KEY_FIELD、NAME_FIELD、PARENT_FIELD、LEAF_FIELD

- **适用于直接返回树结构数据：**NODE_ID、NODE_NAME、PARENT_ID、IS_LEAF

 

### 4.     配置元素表SYS_ELEMENT

- 在sys_element表中的is_async字段配置数值表示handle处理的数据量达到该值时会进行异步处理，这个值只有在数据范围为选中的时候才有效s，数据范围选择全部则直接进行异步执行；异步执行需配置定时调度任务，此时在等待的过程中可以做其他查询，看到以下系统通知表示数据标签已经生成，点击立即处理即可使用

 ![](sanfu.jxmlxg.com\gitbook\images\015.png)

- 配置为0则表示不进行异步处理，默认为0；

数据范围配置：

- handles表中的range_type字段；0：不选中可点，1：单选可点，2：多选可点，3：不选中多选可点

- 数据范围选择全部配置range_type=3，不需要选中数据

### 5.     配置调度信息表Xxl_Job_Info

数据标签添加标签需要用到定时任务，需要新建一个任务管理

配置一个Handle异步定时任务 CRON表达式值：0 0 0/1 ? 执行BEAN名称：HandleAsyncJobHandler 任务描述：Handle异步执行

 ![image-20210330163534443](sanfu.jxmlxg.com\gitbook\images\018.png)

## 四、 功能效果展示：

1. 点击按钮创建标签：

 ![](sanfu.jxmlxg.com\gitbook\images\016.png)

2. 选择数据标签所属分类、输入标签名称（用户自定义名称，建议使用有意义的名称）点击确认

 ![](sanfu.jxmlxg.com\gitbook\images\017.png)

3. 使用数据标签查询数据，打开查询器，在条件中选择属于标签，然后选择需要的数据标签进行查询数据 