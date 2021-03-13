# 数据标签配置

## 一、 功能说明

提供用户通过条件查询、快速查询、异步查询等方式将全部查询结果或选中的行数据生成标签。

用户使用已经生成的数据标签，对用户或商品等数据进行筛选过滤，快速找到需要的用户或货品等

## 二、 配置与表相关

1、注意：只要是在sys_tag_manage表配置了数据标签字段，查询器、Action弹窗搜索、Pick弹窗搜索有对应的字段时，就会出现数据标签使用选项！！

2、可配置分为系统标签和用户标签；系统标签可以使用不能创建 （再双\@\@变量中可以定义类型=1的不能创建）

3、用户标签 用户可自行进行创建、使用

4、用户可配置最上成目录（即系统标签和用户标签）再创建的时候是否要显示；

Select a.*,Rowid From Bwptest1.sys_tag_manage a;

Select a.*,Rowid From Bwptest1.sys_Param_List a Where A.param_Name Like '%TAG%';

Select a.*,Rowid From Bwptest1.Sys_Handle a Where A.element_Id = 'handle-888';

Select a.*,Rowid From Bwptest1.sys_Handles a Where A.element_Id = 'handle-888';

Select a.*,Rowid From BWPTEST1.SYS_ELEMENT a Where A.element_Id = 'handle-888';

## 三、数据标签生成配置

### 1.   **配置想要生成数据标签的字段**

1、**表：**sys_tag_manage

![img](sanfu.jxmlxg.com/images/006.png)

2、TAG_FIELD表示的是能生成数据标签的字段，该字段值与TAG_TYPE组成联合主键。根据过滤数据筛选字段展示对应的数据标签。

3、TAG_TYPE与TAG_TYPE_NAME是用于表示数据标签第一层级树的列表。

4、**TREE_SQL****是用于将数据标签分类展示为树形结构的****SQL**，即你在对应数据标签上设置的标签名称

![img](sanfu.jxmlxg.com/images/007.png)

5、KEY_FIELD(树节点ID值字段名)、NAME_FIELD(节点展示名称字段)，PARENT_FIELD(该节点的上级节点ID)、LEAF_FIELD(该节点是否为叶子节点)，

6、该字段值必须与TREE_SQL语句字段值一致。

SELECT tag_id, tag_name, parent_id, is_leaf FROM sys_tag_info WHERE tag_field = 'SHO_ID' AND tag_type = '2'

![img](sanfu.jxmlxg.com/images/008.png)

7、DATA_SQL则是取数据标签数据的业务SQL，该SQL会用于后续的业务SQL融合条件过滤，查询数据使用数据标签选择对应的标签名称的具体数据内容，只能返回单列值，不支持复杂SQL（不能写动态语句跟存储过程）。

SELECT data_value FROM sys_tag_data WHERE tag_id IN (&SHO_ID&)

### 2.   配置数据标签双\@变量

1、sys_param_list表配置，效果如下：

Select a.* From Bwptest1.sys_Param_List a Where A.param_Name Like '%TAG%';

![img](sanfu.jxmlxg.com/images/009.png)

![img](sanfu.jxmlxg.com/images/010.png)

2、 标签字段：默认为需要配置当前数据标签的字段

3、所属分类：指对应的数据标签名称，当选择该标签名称时会自动展示出不能选择的标签名称。

![img](sanfu.jxmlxg.com/images/011.png)

4、标签名称：自定义属于自己名称，最好取有意义的名称，比如西南大区门店使用工号加+CQ【ZYS36_CQ】

5、是否追加：追加【在该基础上以增量的形式增加数据】，替换【将本次数据替换原有数据】

6、数据范围：全部【如SHO_ID门店数据标签，则是全公司门店】，选中【数据为所选数据

### 3.   handle按钮中配置

1、handle的标题必须和数据标签表的命名一致：

![img](sanfu.jxmlxg.com/images/012.png)

![img](sanfu.jxmlxg.com/images/013.png)

2、 所属分类双\@变量为数据标签树形结构变量，业务可自行创建一张业务表存储树形结构，从里面获取数据：sys_tag_data、sys_tag_info表必须是当前数据源对应库的业务表，不可跨库使用。

3、配置数据标签Handle按钮

4、在handles的target_sql中引用双\@变量，并执行数据插入或修改SQL，根据页面选中为追加还是替换来判断。

![img](sanfu.jxmlxg.com/images/014.png)

5、上述查询为数据标签业务固定语法，如果不是数据标签业务双\@变量想引用树结构则可以自行替换。但是SQL返回结果字段需要包含以下两种固定语法的其中一种

6、适用于SQL：TOP_KEY_FIELD、TOP_NAME_FIELD、TREE_SQL、KEY_FIELD、NAME_FIELD、PARENT_FIELD、LEAF_FIELD

7、适用于直接返回树结构数据：NODE_ID、NODE_NAME、PARENT_ID、IS_LEAF

### 4.   异步执行配置：

1、在sys_element表中的is_async字段配置数值表示handle处理的数据量达到该值时会进行异步处理，这个值只有在数据范围为选中的时候才有效s，数据范围选择全部则直接进行异步执行；异步执行需配置定时调度任务，此时在等待的过程中可以做其他查询，看到以下系统通知表示数据标签已经生成，点击立即处理即可使用

![img](sanfu.jxmlxg.com/images/015.png)

2、配置为0则表示不进行异步处理，默认为0；

3、数据范围配置：

handles表中的range_type字段；0：不选中可点，1：单选可点，2：多选可点，3：不选中多选可点

数据范围选择全部配置range_type=3，不需要选中数据

### 5.   定时任务-任务管理配置

1、数据标签添加标签需要用到定时任务，需要新建一个任务管理

Select a.*,Rowid From Bwptest1.xxl_Job_Info a Where A.id = '102';

2、配置一个Handle异步定时任务 CRON表达式值：0 0 0/1 ? 执行BEAN名称：HandleAsyncJobHandler 任务描述：Handle异步执行

 

## 四、 效果展示：

1. 点击按钮创建标签：

![img](sanfu.jxmlxg.com/images/016.png)

1. 生成标签后，打开查询器、pickup、action使用标签

![img](sanfu.jxmlxg.com/images/017.png)