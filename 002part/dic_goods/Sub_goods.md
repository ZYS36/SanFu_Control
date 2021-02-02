# 1.商品模块

DIC_GOODS

```
SELECT * FROM MDMDATA.DIC_GOODS
```

DIC_ARTICLES

```
SELECT * FROM MDMDATA.DIC_ARTICLES
```



## 1.1涉及岗位

| 岗位 | 职责                                                         |
| ---- | ------------------------------------------------------------ |
| 买手 | 商品的基础建档、商品修改、商品成分、商品供应商               |
| 企划 | 商品定价、调价（商品的成本变更：主要由各分部经理审核）       |
| 跟单 | 发货流程维护，如包装基数；商品是否开票、最低批发占比（是否开票发生变更：由各分部跟单主管在待审核是否开票变更窗口审核） |

## 1.2程序控制

### 1.2.1 程序控制主要分三种类型控制：新增、修改、删除

#### 1.2.1.1商品新增程序控制

##### 1.2.1.1.1建档必填字段

说明：由于三福存在多个分部的情况，所以在建档时就需要考虑分部差异化，每一个分部必填字段都会不一样，当然也存在共同必填字段，如：分部

- 分部共同必填字段有：分部、仓库、顾客群、品类组、品类、子品类、商品类别、一般税率、是否开票、主题、好产品、顾客类型、是否质检、计量单位、款式、进价、品牌、小规模税率、最低批发占比、交期、季节、计量单位组、是否易碎、供应商
- 每个分部必填字段

| 分部 | 必填字段                                                     |
| ---- | ------------------------------------------------------------ |
| 男装 | 腰型、是否有内口袋、套装类型、厚度、领型、衣长、供应商到仓日期、裤长、花版类型、订货基数、脚口、版型、羽绒类别、加工利润、弹力指数、面料、外套类目、针数、廓形、袖型、袖长、衣门禁 |
| 女装 | 外套类目、袖型、厚度、腰型、袖长、加工利润、花版类型、衣长、裙长、衣门禁、面料、供应商到仓日期、领型、订货基数、廓形、裤长 |
| 内衣 | 袜型、罩杯里料、模杯面料、挡位款式、海绵、腰型、廓形、搭扣排数、织法、双数、加工利润、厚薄、有无钢圈、订货基数、罩杯款式、后比、肩带宽度、文胸类型、加绒、供应商到仓日期、罩杯厚度、有插垫、内裤款式、功能、功效 |
| 美妆 | 供应商到仓日期、刷毛材质、加工利润、保质期、香味、香调、备案号、经营状态、；镜框材质、订货基数、是否定制、使用性别 |
| 淘品 | 导热体直径、支数、电源方式、表扣、拉链位、适用年龄、最高温度、伞杆材质、钥匙挂钩、接口类型、是否线控、是否可擦、保修、识字卡类型、导热体材质、加工利润、墨水是否速干、充电时长、是否带坠、钥匙挂钩、平均温度、表壳材质、电池类型、额定电压、电吹风风嘴样式、眼镜形状、内存容量、供应商到仓库日期、适用发质、档位、经营状态、表底、打开尺寸、表盘、适用手机机型、连续使用时间、防水深度、封面材质、页数、鞋底厚度、书写粗细、是否内置Wi-Fi、打开方式、幅面、表盘直径、订货基数、填充物、表盘厚度、是否多层、功能、有无麦克风、容量、是否有声、规格、内置蓝牙、鞋底材质、玩具类型、装订方式、表带材质、伞布材质、周长、机芯类型、温馨提示、坠子材质、保温时长、手表镜面材质、最佳造型时间、笔芯颜色、镜片材质、开关类型、笔杆材质、段数、额定功率、伞面涂层、功能类型、光源类型 |
| 鞋包 | 侧帮、肩带可调节、鞋头款式、防水强度、开口深度、是否可背负、内部结构、订货基数、前掌宽、是否放A4文件、跟底款式、有无拉杆、里料材质、箱包外袋种类、鞋面材质、鞋跟高、鞋帮高、内里材质、肩带样式、后帮、版型、滚轮样式、肩带长度、有无配件、筒围、供应商到仓库日期、厚薄、鞋底材质、鞋制作工艺、提拎部件类型、加工利润、可折叠、腰带带扣款式、带扣材质、规格、踝围、闭合方式、鞋垫材质、五金颜色、皮质特征、承重量 |

##### 1.2.1.1.2系统默认值

- 点击新增时系统默认值设置方式：DEFAULIEST

a)     映射字段、税票编码、执行标准、安全技术类别：根据商品税票编码基础表数据给默认值（DIC_CLS_TAX）

b)     供应商到仓库日期、订货基数：男女装分部设置为【1】，其他分部设置为【null】

c)     是否易碎：男女内分部设置为【否】，其他分部设置为【是】

d)     是否质检：男女内鞋包分部设置为【是】，后勤赠品美妆欧好分部设置为【否】，淘品分部子品类在（开口拖、礼帽、马尾夹、墨水、铅笔、青蛙夹、全包拖、人字拖、水彩笔、塑料杯、锁包扣、陶瓷杯、头箍、西瓜帽、橡巾、小风扇、鸭夹、颜料、一字夹、一字拖、渔夫帽、圆珠笔、爪夹、中性笔、组合发饰、组合套装）以上子品类中默认为【是】，否则默认为【否】

e)     最低批发占比：所有分部开票供应商设置为【0.1】

f)     小规模税率：所有分部默认值为【0.01】

- 触发器默认

a)     鞋包YWZ商品款式为【鞋】，将写入大件商品表（CFG_GOOD_LARGES）

b)     赠品部门定价必须为【0】

c)     只有后勤部门的饮料才允许店零星采购

d)     面料价格+辅料价格+设计价格+设计利润+加工价格+其他工艺=进价

e)     差异值、差异类型必须是标准组合

Ø 系统提示

a)     订货基数与包装基数不一致时系统会出现提示信息

#### 1.2.1.2商品修改程序控制

- 商品经止退换修改：若商品有条码，则同步更新条码禁止退换属性

- 商品设置为不开票：最低批发占比同步设置为【否】，且将开票状态设置为【待审核】，跟单主管在待审核是否开票变更窗口审核

- 美妆分部顾客群为：彩妆、护肤当定价大于【0】时，商品下所有条码必须摄入供应商条码

- 当商品仍有库存时，不可修改包装基数

- 包装基数必须在【1-100】之间

- 商品定价时系统进行以下处理：

a)     将商品信息写入DIC_GOODS_SUPPLIER表

b)     更新IC_GOODS_SUPPLIER表的新增时间为当前时间，最后修改时间为当前时间

c)     更新DIC_ARTICLES表新增时间为当前时间，最后修改时间为当前时间

d)     更新DIC_GOODSCOMPOSITION表新增时间为当前时间，最后修改时间为当前时间

e)     更新DIC_GOODSOFPICTURE表新增时间为当前时间，最后修改时间为当前时间

f)     更新DIC_SUP_BARCODE表新增时间为当前时间，最后修改时间为当前时间

- 已发布的数据不能修改主键，不能清空最近发布时间

- 不能修改已经订货的商品类别和地区

- 不能修改有库存的商品品名、风格、所属地区等信息

- 不能修改一个月前建档的商品品名、风格、所属地区等信息

- 不能清楚商品的定价

- 不能修改30天前商品的定价
-  不能修改有库存的商品定价
- 已经定价的商品不能修改进价和进价组成相关价格和是否电商专供款
- 已经定价的商品不能直接修改商品的主供应商，需做变更
- 已经定价的商品不能修改会员折扣且会员折扣必须在【0-85】之间

#### 1.2.1.3商品删除程序控制

- 不能删除一个月前创建的供应商档案
- 已发布的数据,不能删除（已发布表示已传中心）

### 1.2.2供应商条码相关控制

- 美妆69码长度必须时【13】位
-  69码删除时同步更新WMS数据【DIC_WMSGOODS】
- 数据新增时，若商品已经定价则自动新增时间为当前时间，最后修改时间为当前时间

### 1.2.3商品图片相关控制

-  数据新增时，若商品已经定价则自动新增时间为当前时间，最后修改时间为当前时间

### 1.2.4商品成分相关控制

- 新建档的商品必须设置相应的成分明细
- 每个成分组成的原料之和需等于【100%或者0%】
- 一个商品中，成分的备注字段最多只能一行
- 标签设计的角度看，一个商品成分不能超过5种

### 1.2.5商品供应商相关控制

- 存在“变更主供应商”记录的数据，不能删除供应商档案
- 存在“成本变更”记录的数据，不能删除供应商档案

## 1.3 表结构

### 1.3.1 DIC_GOODS

| 字段                   | 类型           | 为空 | 默认值           | 翻译                                                |
| ---------------------- | -------------- | ---- | ---------------- | --------------------------------------------------- |
| BRA_ID                 | NUMBER(3)      |      |                  |                                                     |
| SHO_ID                 | VARCHAR2(4)    |      | 配送中心店号     |                                                     |
| GOO_ID                 | VARCHAR2(13)   |      | 货号             |                                                     |
| GOO_NAME               | VARCHAR2(20)   |      | 品名             |                                                     |
| MATERIAL               | VARCHAR2(10)   | Y    |                  | 质地                                                |
| PATTERN                | VARCHAR2(10)   | Y    |                  | 款式                                                |
| SPECS                  | VARCHAR2(20)   |      | 特征             |                                                     |
| BUYERID                | VARCHAR2(6)    | Y    |                  | 采购员ID                                            |
| SUP_ID                 | VARCHAR2(5)    |      | 主供应商ID       |                                                     |
| INPRICE                | NUMBER(18,4)   |      | 进价             |                                                     |
| ADVICEPRICE            | NUMBER(18,4)   | Y    |                  | 建议定价                                            |
| PRICE                  | NUMBER(18,4)   | Y    |                  | 定价                                                |
| PAUSE                  | NUMBER(1)      | 0    | 禁用             |                                                     |
| USENOTE                | VARCHAR2(30)   | Y    |                  | 商品使用方法                                        |
| CREATETIME             | DATE           |      | (SYSDATE)        | 创建时间                                            |
| PINYIN                 | VARCHAR2(20)   | Y    |                  | 商品的拼音                                          |
| GOOSTYLE               | VARCHAR2(4)    | Y    |                  | 风格                                                |
| THEME                  | VARCHAR2(30)   | Y    |                  | 主题                                                |
| CUSTYPENO              | NUMBER(3)      |      | 顾客类型         |                                                     |
| ADVICESALEDAYS         | NUMBER(10)     | Y    |                  | 建议销售期                                          |
| ORDERSTARTAMOUNT       | NUMBER(18,4)   | Y    |                  | 起订量                                              |
| DELIVERDAYS            | NUMBER(10)     |      | 交期             |                                                     |
| ORDERBASEAMOUNT        | NUMBER(5)      |      | 订货基数         |                                                     |
| THUMBNAIL1             | BLOB           | Y    |                  | 缩略图                                              |
| MUSTORDER              | NUMBER(1)      | 0    | 必订             |                                                     |
| ORDERENDDATE           | DATE           | Y    | (TRUNC(SYSDATE)) | 止订日期                                            |
| COLORSTARTAMOUNT       | NUMBER(18,4)   | Y    |                  | 色起订量                                            |
| SIZESTARTAMOUNT        | NUMBER(18,4)   | Y    |                  | 码起订量                                            |
| SEASON                 | VARCHAR2(10)   |      | 季节             |                                                     |
| MINHANDAMOUNT          | NUMBER(10)     | Y    |                  | 最低现货量                                          |
| PRICETIME              | DATE           | Y    |                  | 定价时间                                            |
| BAKCODE                | VARCHAR2(13)   |      | 备用货号         |                                                     |
| POPULARITY             | VARCHAR2(10)   | Y    |                  | 流行度                                              |
| DESIGNMODE             | VARCHAR2(20)   | Y    |                  | 设计模式                                            |
| GOOTYPEID              | CHAR(1)        |      | -1               | 商品类别                                            |
| GOOAREAID              | VARCHAR2(2)    | Y    |                  | 所属地区（针对地区零星采购）                        |
| SETTLEMENT             | VARCHAR2(10)   | Y    |                  | 结算地                                              |
| WASHINGLIST            | VARCHAR2(100)  | Y    |                  | 洗涤说明                                            |
| MININPRICE             | NUMBER(18,4)   | Y    |                  | 最低进价                                            |
| MINDATETIME            | DATE           | Y    |                  | 最低进价的日期                                      |
| SERIES                 | VARCHAR2(20)   | Y    |                  | 系列                                                |
| FABRICPRICE            | NUMBER(18,2)   | Y    | null             | 面料价格                                            |
| ACCESSORIESPRICE       | NUMBER(18,2)   | Y    | null             | 辅料价格                                            |
| OTHERPRICE             | NUMBER(18,2)   | Y    |                  | 其它工艺价格                                        |
| ISNEWGOODS1            | NUMBER(1)      | Y    | -1               |                                                     |
| PICTURE1               | BLOB           | Y    |                  | 图片                                                |
| EXECUTIVESTD           | VARCHAR2(30)   | Y    |                  | 执行标准编号                                        |
| SAFETYTYPE             | VARCHAR2(30)   | Y    |                  | 安全级别                                            |
| TOUCHCLASS             | VARCHAR2(6)    | Y    |                  | 接触类型                                            |
| ISSKU                  | NUMBER(1)      | 0    | 关键单品         |                                                     |
| ADVICEINPRICE          | NUMBER(18,4)   | Y    |                  | 建议进价                                            |
| FIRSTINPRICE           | NUMBER(18,4)   | Y    |                  | 第一次的进货价格                                    |
| ISUM                   | NUMBER(1)      | 0    | 集中管理         |                                                     |
| SIMILARGOODID          | VARCHAR2(13)   | Y    |                  | 相似品货号                                          |
| ISNEWGOOD              | NUMBER(1)      | Y    | -1               |                                                     |
| WARMDEGREE             | NUMBER(2)      | Y    |                  | 保暖度                                              |
| MUSTENDDATE            | DATE           | Y    |                  |                                                     |
| MEMO                   | VARCHAR2(2000) | Y    |                  | 备注                                                |
| ORDERABLE_IND          | NUMBER(1)      | Y    | 1                | 可订购                                              |
| SELLABLE_IND           | NUMBER(1)      | 1    | 可销售           |                                                     |
| INVENTORY_IND          | NUMBER(1)      | 1    | 可库存           |                                                     |
| INSERTTIME             | DATE           | Y    |                  | 新增时间                                            |
| LASTMODIFYTIME         | DATE           | Y    |                  | 最近修改时间                                        |
| LASTPUBLISHTIME        | DATE           | Y    |                  | 最近发布时间                                        |
| DIFF_GROUP_ID1         | VARCHAR2(10)   | Y    |                  | 差异组编号1                                         |
| DIFF_GROUP_ID2         | VARCHAR2(10)   | Y    |                  | 差异组编号2                                         |
| DIFF_ID1               | VARCHAR2(1000) | Y    |                  | 差异编号1                                           |
| DIFF_ID2               | VARCHAR2(1000) | Y    |                  | 差异编号2                                           |
| CUSGROUPNO             | NUMBER(3)      |      | 顾客群编号       |                                                     |
| CATEGORYGROUPNO        | NUMBER(3)      |      | 品类组编号       |                                                     |
| CATEGORYNO             | NUMBER(4)      |      | 品类编号         |                                                     |
| SUBCATEGORYNO          | NUMBER(4)      |      | 子品类编号       |                                                     |
| UPDATETYPE             | NUMBER(38)     | 0    | 控制是否允许更新 |                                                     |
| GOODS_STAGE            | NUMBER(1)      | Y    |                  | 波段                                                |
| SUP_PACK_AMOUNT        | NUMBER(5)      | Y    |                  | 订货基数倍数                                        |
| DESIGNPRICE            | NUMBER(18,4)   | 0    | 设计价格         |                                                     |
| DESIGNPROFITPRICE      | NUMBER(18,4)   | 0    | 设计利润         |                                                     |
| PROCPRICE              | NUMBER(18,4)   | 0    | 加工价格         |                                                     |
| PROCPROFITPRICE        | NUMBER(18,4)   | 0    | 加工利润         |                                                     |
| UNGOODS                | NUMBER(1)      | 0    | 非商品           |                                                     |
| SUPTOWHDAYS            | NUMBER(4)      | 0    | 供应商到仓库日期 |                                                     |
| LACK                   | NUMBER(1)      | 0    | 缺货             |                                                     |
| ORDERSTARTDATE         | DATE           | Y    | (TRUNC(SYSDATE)) | 起订日期                                            |
| NOT_EXCHANGE           | NUMBER(1)      | Y    | 0                | 禁止退换货                                          |
| STRUCTURE              | VARCHAR2(20)   | Y    |                  | 品类(男装长度用)                                    |
| MODIFIER               | VARCHAR2(6)    | Y    |                  | 最近修改人                                          |
| PRODUCT_DESC           | VARCHAR2(1000) | Y    |                  | 产品说明                                            |
| TEMP_BRA_ID            | NUMBER(3)      | Y    |                  |                                                     |
| TEMP_CUSGROUPNO        | NUMBER(3)      | Y    |                  | 临时顾客群编号                                      |
| TEMP_CATEGORYGROUPNO   | NUMBER(3)      | Y    |                  | 临时品类组编号                                      |
| TEMP_CATEGORYNO        | NUMBER(4)      | Y    |                  | 临时品类编号                                        |
| TEMP_SUBCATEGORYNO     | NUMBER(4)      | Y    |                  | 临时子品类编号                                      |
| TEMP_SUP_ID            | VARCHAR2(5)    | Y    |                  | 临时主供应商ID                                      |
| STANDARD               | VARCHAR2(16)   | Y    |                  | 包规格                                              |
| SHELF_LIFE             | VARCHAR2(20)   | Y    |                  | 保质期                                              |
| MADE_IN                | VARCHAR2(50)   | Y    |                  | 产地                                                |
| USE_CYCLE              | VARCHAR2(30)   | Y    |                  | 使用周期                                            |
| BAND_LEVEL             | VARCHAR2(30)   | Y    |                  | 品牌等级                                            |
| HARDWARE_COLOR         | VARCHAR2(30)   | Y    |                  | 五金颜色                                            |
| NET_WEIGHT             | NUMBER(18,4)   | Y    |                  | 净含量                                              |
| HEAD_SIZE              | VARCHAR2(30)   | Y    |                  | 头围尺寸                                            |
| BIND_AMOUNT            | VARCHAR2(30)   | Y    |                  | 捆绑量                                              |
| FLOWCHOOSEGOO_ID       | NVARCHAR2(80)  | Y    |                  | 波段选款货号                                        |
| TAX_RATE               | NUMBER(12,4)   | Y    |                  | 一般税率                                            |
| SMALL_TAXRATE          | NUMBER(12,4)   | Y    | 0.01             | 小规模税率                                          |
| UNITS_ID               | VARCHAR2(8)    | Y    |                  | 单位ID                                              |
| UNITS_GROUPS_ID        | VARCHAR2(8)    | Y    |                  | 单位组ID                                            |
| IS_NICE                | NUMBER(1)      | Y    |                  | 好产品，主数据项目新增20180720                      |
| SAFE_TYPE              | VARCHAR2(30)   | Y    |                  | 安全技术类别                                        |
| TAX_NUMBER             | VARCHAR2(19)   | Y    |                  | 税票编码                                            |
| MAPFIELD               | VARCHAR2(30)   | Y    |                  | 映射字段                                            |
| SELLING_POINT          | VARCHAR2(200)  | Y    |                  | 卖点                                                |
| SCENE                  | VARCHAR2(50)   | Y    |                  | 场景                                                |
| THESAURUS              | VARCHAR2(50)   | Y    |                  | 词库                                                |
| OPEN_SIZE              | VARCHAR2(20)   | Y    |                  | 打开尺寸                                            |
| REMINDER               | VARCHAR2(50)   | Y    |                  | 温馨提示                                            |
| APPLY_MODEL            | VARCHAR2(30)   | Y    |                  | 适用手机机型                                        |
| WIRE_LENGTH            | VARCHAR2(20)   | Y    |                  | 缆线长度                                            |
| PERIMETER              | VARCHAR2(20)   | Y    |                  | 周长                                                |
| CAPACITY               | VARCHAR2(20)   | Y    |                  | 容量                                                |
| VOLUME                 | VARCHAR2(20)   | Y    |                  | 体积                                                |
| PACK_VOLUME            | VARCHAR2(20)   | Y    |                  | 包装体积                                            |
| OPEN_DEPTH             | VARCHAR2(20)   | Y    |                  | 开口深度                                            |
| ANKLE_GIRTH            | VARCHAR2(20)   | Y    |                  | 踝围                                                |
| CANISTER_GIRTH         | VARCHAR2(20)   | Y    |                  | 筒围                                                |
| HEIGHT_OF_SHOE_UPPER   | VARCHAR2(20)   | Y    |                  | 鞋帮高                                              |
| WIDTH_OF_FOREFEET      | VARCHAR2(20)   | Y    |                  | 前掌宽                                              |
| HEIGHT_OF_HEEL         | VARCHAR2(20)   | Y    |                  | 鞋跟高                                              |
| NET_CONTENT            | VARCHAR2(20)   | Y    |                  | 净含量                                              |
| RECORD_NUMBER          | VARCHAR2(50)   | Y    |                  | 备案号                                              |
| GROSS_WEIGHT           | NUMBER(18,4)   | Y    |                  | 毛重                                                |
| HAS_PACKING_BOX        | NUMBER(1)      | Y    |                  | 是否有包装盒                                        |
| IS_INK_QUICK_DRY       | NUMBER(1)      | Y    |                  | 墨水是否速干                                        |
| MATERIAL_PENHOLDER     | VARCHAR2(20)   | Y    |                  | 笔杆材质                                            |
| WRITE_THICKNESS        | VARCHAR2(20)   | Y    |                  | 书写粗细                                            |
| REFILL_COLOR           | VARCHAR2(20)   | Y    |                  | 笔芯颜色                                            |
| CAN_ERASE              | NUMBER(1)      | Y    |                  | 是否可擦                                            |
| BRANCH_AMOUNT          | VARCHAR2(20)   | Y    |                  | 支数                                                |
| RATED_POWER            | VARCHAR2(20)   | Y    |                  | 额定功率                                            |
| RATED_VOLTAGE          | VARCHAR2(20)   | Y    |                  | 额定电压                                            |
| WORKING_MODE           | VARCHAR2(10)   | Y    |                  | 工作模式                                            |
| GEAR                   | VARCHAR2(10)   | Y    |                  | 档位                                                |
| DRIER_MOUTH_STYLE      | VARCHAR2(20)   | Y    |                  | 电吹风风嘴样式                                      |
| POWER_MODE             | VARCHAR2(10)   | Y    |                  | 电源方式                                            |
| CHARGE_DURATION        | VARCHAR2(20)   | Y    |                  | 充电时长                                            |
| IS_AUTO_DISCONNECT     | NUMBER(1)      | Y    |                  | 是否自动断电                                        |
| USE_DURATION           | VARCHAR2(10)   | Y    |                  | 连续使用时间                                        |
| INTERFACE_TYPE         | VARCHAR2(20)   | Y    |                  | 接口类型                                            |
| IS_WIRE_CONTROL        | NUMBER(1)      | Y    |                  | 是否线控                                            |
| HAS_MICROPHONE         | NUMBER(1)      | Y    |                  | 有无麦克风                                          |
| HAS_PENDANT            | NUMBER(1)      | Y    |                  | 是否带坠                                            |
| MATERIAL_PENDANT       | VARCHAR2(20)   | Y    |                  | 坠子材质                                            |
| EXTENTED_CHAIN         | VARCHAR2(10)   | Y    |                  | 延长链                                              |
| IS_MULTI_LAYER         | NUMBER(1)      | Y    |                  | 是否多层                                            |
| FILLER                 | VARCHAR2(10)   | Y    |                  | 填充物                                              |
| APPLY_AGE              | VARCHAR2(20)   | Y    |                  | 适用年龄                                            |
| AVERAGE_TEMPERATURE    | VARCHAR2(10)   | Y    |                  | 平均温度                                            |
| HIGHEST_TEMPERATURE    | VARCHAR2(10)   | Y    |                  | 最高温度                                            |
| KEEP_WARM_DURATION     | VARCHAR2(20)   | Y    |                  | 保温时长                                            |
| MATERIAL_WATCH_FACE    | VARCHAR2(20)   | Y    |                  | 手表镜面材质                                        |
| MOVEMENT_TYPE          | VARCHAR2(30)   | Y    |                  | 机芯类型                                            |
| MATERIAL_WATCH_TAPE    | VARCHAR2(10)   | Y    |                  | 表带材质                                            |
| MATERIAL_WATCH_SHELL   | VARCHAR2(10)   | Y    |                  | 表壳材质                                            |
| WATERPROOFING_DEPTH    | VARCHAR2(20)   | Y    |                  | 防水深度                                            |
| DIAL_THICKNESS         | VARCHAR2(10)   | Y    |                  | 表盘厚度                                            |
| DIAL_DIAMETER          | VARCHAR2(10)   | Y    |                  | 表盘直径                                            |
| MATERIAL_COVER         | VARCHAR2(10)   | Y    |                  | 封面材质                                            |
| BINDING_MODE           | VARCHAR2(20)   | Y    |                  | 装订方式                                            |
| PAGE_COUNT             | VARCHAR2(10)   | Y    |                  | 页数                                                |
| PAPER_FORMAT           | VARCHAR2(4)    | Y    |                  | 幅面                                                |
| MATERIAL_LENS          | VARCHAR2(10)   | Y    |                  | 镜片材质                                            |
| HAS_SOUND              | NUMBER(1)      | Y    |                  | 是否有声                                            |
| HAS_WIFI               | NUMBER(1)      | Y    |                  | 是否内置Wi-Fi                                       |
| MATERIAL_CLOTH         | VARCHAR2(10)   | Y    |                  | 伞布材质                                            |
| MATERIAL_POLE          | VARCHAR2(20)   | Y    |                  | 伞杆材质                                            |
| OPEN_MODE              | VARCHAR2(10)   | Y    |                  | 打开方式                                            |
| COATING_OF_UMBRELLA    | VARCHAR2(10)   | Y    |                  | 伞面涂层                                            |
| FUNCTION_TYPE          | VARCHAR2(20)   | Y    |                  | 功能类型                                            |
| DIAMETER_OF_HEAT       | VARCHAR2(20)   | Y    |                  | 导热体直径                                          |
| MATERIAL_HEAT          | VARCHAR2(16)   | Y    |                  | 导热体材质                                          |
| PERFECT_DURATION       | VARCHAR2(20)   | Y    |                  | 最佳造型时间                                        |
| SUIT_HAIR              | VARCHAR2(10)   | Y    |                  | 适用发质                                            |
| SEGMENT_NUMBER         | VARCHAR2(10)   | Y    |                  | 段数                                                |
| LIGHT_TYPE             | VARCHAR2(10)   | Y    |                  | 光源类型                                            |
| SWITCH_TYPE            | VARCHAR2(10)   | Y    |                  | 开关类型                                            |
| BATTERY_CAPACITY       | VARCHAR2(20)   | Y    |                  | 电池容量                                            |
| BATTERY_TYPE           | VARCHAR2(20)   | Y    |                  | 电池类型                                            |
| HAS_BLUETOOTH          | NUMBER(1)      | Y    |                  | 内置蓝牙                                            |
| MATERIAL_INNER         | VARCHAR2(20)   | Y    |                  | 内里材质                                            |
| MATERIAL_SHOE_PAD      | VARCHAR2(20)   | Y    |                  | 鞋垫材质                                            |
| MATERIAL_SHOE_COVER    | VARCHAR2(20)   | Y    |                  | 鞋面材质                                            |
| CORTICAL_FEATHURES     | VARCHAR2(20)   | Y    |                  | 皮质特征                                            |
| PATTERN_SHOE_HEAD      | VARCHAR2(10)   | Y    |                  | 鞋头款式                                            |
| PATTERN_HELL_BOTTOM    | VARCHAR2(10)   | Y    |                  | 跟底款式                                            |
| CLOSE_MODE             | VARCHAR2(16)   | Y    |                  | 闭合方式                                            |
| PRODUCE_TECHNOLOGY     | VARCHAR2(10)   | Y    |                  | 鞋制作工艺                                          |
| CARRING_PARTS_TYPE     | VARCHAR2(16)   | Y    |                  | 提拎部件类型                                        |
| INNER_STRUCTURE        | VARCHAR2(16)   | Y    |                  | 内部结构                                            |
| TYPE_OF_OUTER_BAG      | VARCHAR2(10)   | Y    |                  | 箱包外袋种类                                        |
| CAN_FOLD               | NUMBER(1)      | Y    |                  | 可折叠                                              |
| MATERIAL_INNER_MAT     | VARCHAR2(10)   | Y    |                  | 里料材质                                            |
| STRAP_STYLE            | VARCHAR2(6)    | Y    |                  | 肩带样式                                            |
| STRAP_CAN_ADJUST       | NUMBER(1)      | Y    |                  | 肩带可调节                                          |
| HAS_PARTS              | NUMBER(1)      | Y    |                  | 有无配件                                            |
| PATTERN_BELT_BUCKLE    | VARCHAR2(10)   | Y    |                  | 腰带带扣款式                                        |
| MATERIAL_BELT_BUCKLE   | VARCHAR2(10)   | Y    |                  | 带扣材质                                            |
| IS_CUSTOMIZED          | NUMBER(1)      | Y    |                  | 是否定制                                            |
| SUIT_SEX               | VARCHAR2(20)   | Y    |                  | 适用性别                                            |
| EFFECT                 | VARCHAR2(20)   | Y    |                  | 功效                                                |
| SUIT_SKIN              | VARCHAR2(16)   | Y    |                  | 适用肤质                                            |
| NUMBER_OF_NEEDLE       | VARCHAR2(10)   | Y    |                  | 针数                                                |
| FOOT_MOUTH             | VARCHAR2(10)   | Y    |                  | 脚口                                                |
| DOWN_CATEGORY          | VARCHAR2(10)   | Y    |                  | 羽绒类别                                            |
| COORDINATE_CATEGORY    | VARCHAR2(10)   | Y    |                  | 套装类型                                            |
| ELASTIC_INDEX          | VARCHAR2(10)   | Y    |                  | 弹力指数                                            |
| SKIRT_LENGTH           | VARCHAR2(6)    | Y    |                  | 裙长                                                |
| GRAM_WEIGHT            | VARCHAR2(20)   | Y    |                  | 克重                                                |
| PATTERN_OF_CUP         | VARCHAR2(20)   | Y    |                  | 罩杯款式                                            |
| MATERIAL_CUP_INNER     | VARCHAR2(10)   | Y    |                  | 罩杯里料                                            |
| FABRIC_OF_CUP          | VARCHAR2(10)   | Y    |                  | 模杯面料                                            |
| NUMBER_OF_CLASP        | VARCHAR2(10)   | Y    |                  | 搭扣排数                                            |
| BRASSIERE_CUP_TYPE     | VARCHAR2(4)    | Y    |                  | 文胸杯型                                            |
| IS_WITH_VELVET         | VARCHAR2(6)    | Y    |                  | 加绒                                                |
| SOCK_TYPE              | VARCHAR2(16)   | Y    |                  | 袜型                                                |
| WIDTH_OF_CUP           | VARCHAR2(10)   | Y    |                  | 杯罩厚度                                            |
| BACK_WING              | VARCHAR2(10)   | Y    |                  | 后比                                                |
| SPONGE                 | VARCHAR2(10)   | Y    |                  | 海棉                                                |
| WIDTH_SHOULDER_BAND    | VARCHAR2(10)   | Y    |                  | 肩带宽度                                            |
| HAS_STEEL_RING         | NUMBER(1)      | Y    |                  | 有无钢圈                                            |
| HAS_INSERT_PAD         | NUMBER(1)      | Y    |                  | 有插垫                                              |
| PATTERN_UNDERPANTS     | VARCHAR2(10)   | Y    |                  | 内裤款式                                            |
| PATTERN_CROTCH         | VARCHAR2(10)   | Y    |                  | 档位款式                                            |
| WEAVE_METHOD           | VARCHAR2(10)   | Y    |                  | 织法                                                |
| COLLAR_TYPE            | VARCHAR2(20)   | Y    |                  | 领型                                                |
| FABRIC                 | VARCHAR2(20)   | Y    |                  | 面料                                                |
| TROUSER_LENGTH         | VARCHAR2(20)   | Y    |                  | 裤长                                                |
| LENGTH_OF_SLEEVE       | VARCHAR2(10)   | Y    |                  | 袖长                                                |
| LENGTH_OF_CLOTHES      | VARCHAR2(10)   | Y    |                  | 衣长                                                |
| COAT_CATEGORY          | VARCHAR2(10)   | Y    |                  | 外套类目                                            |
| THICKNESS              | VARCHAR2(10)   | Y    |                  | 厚度                                                |
| FLOWER_CATEGORY        | VARCHAR2(20)   | Y    |                  | 花版类型                                            |
| TYPE_VERSION           | VARCHAR2(10)   | Y    |                  | 版型                                                |
| USE_FUNCTION           | VARCHAR2(10)   | Y    |                  | 功能                                                |
| WIDTH                  | VARCHAR2(20)   | Y    |                  | 厚薄                                                |
| PACK_MODE              | VARCHAR2(20)   | Y    |                  | 包装方式                                            |
| SALE_STATE             | VARCHAR2(20)   | Y    |                  | 经营状态                                            |
| MATERIAL_TREAD         | VARCHAR2(10)   | Y    |                  | 鞋底材质                                            |
| SILHOUETTE             | VARCHAR2(10)   | Y    |                  | 廓型                                                |
| WAIST_TYPE             | VARCHAR2(10)   | Y    |                  | 腰型                                                |
| SLEEVE_TYPE            | VARCHAR2(10)   | Y    |                  | 袖型                                                |
| CLOTHING_PLACKET       | VARCHAR2(20)   | Y    |                  | 衣门襟                                              |
| HAS_INNER_POCKET       | NUMBER(1)      | Y    |                  | 是否有内口袋                                        |
| SPESC_TYPE             | VARCHAR2(10)   | Y    |                  | 规格类型                                            |
| AROMA                  | VARCHAR2(10)   | Y    |                  | 香味                                                |
| FRAGRANCE              | VARCHAR2(10)   | Y    |                  | 香调                                                |
| MATERIAL_BRUSH         | VARCHAR2(10)   | Y    |                  | 刷毛材质                                            |
| MATERIAL_FRAME         | VARCHAR2(10)   | Y    |                  | 镜框材质                                            |
| EVEN_NUMBERS           | VARCHAR2(10)   | Y    |                  | 双数                                                |
| GLASSES_SHAPE          | VARCHAR2(10)   | Y    |                  | 眼镜形状                                            |
| SHOES_BOOTOM_THICK     | VARCHAR2(10)   | Y    |                  | 鞋底厚度                                            |
| GUARANTEE              | VARCHAR2(10)   | Y    |                  | 保修                                                |
| DIAL_BUCKLE            | VARCHAR2(16)   | Y    |                  | 表扣                                                |
| DIAL_BOOTOM            | VARCHAR2(10)   | Y    |                  | 表底                                                |
| DIAL_COVER             | VARCHAR2(10)   | Y    |                  | 表盘                                                |
| KEY_HOOK               | VARCHAR2(6)    | Y    |                  | 钥匙挂钩                                            |
| ZIPPER_POSITION        | VARCHAR2(10)   | Y    |                  | 拉链位                                              |
| TOY_TYPE               | VARCHAR2(10)   | Y    |                  | 玩具类型                                            |
| LITER_CARD_TYPE        | VARCHAR2(10)   | Y    |                  | 识字卡类型                                          |
| MEMORY_CAPACITY        | VARCHAR2(10)   | Y    |                  | 内存容量                                            |
| CHAIN_STYLE            | VARCHAR2(10)   | Y    |                  | 链子样式                                            |
| SHOE_BACK              | VARCHAR2(10)   | Y    |                  | 后帮                                                |
| SHOE_SIDE              | VARCHAR2(6)    | Y    |                  | 侧帮                                                |
| HAS_PULL_ROD           | NUMBER(1)      | Y    |                  | 有无拉杆                                            |
| ROLLER_STYLE           | VARCHAR2(10)   | Y    |                  | 滚轮样式                                            |
| CAN_BEAR               | NUMBER(1)      | Y    |                  | 是否可背负                                          |
| SUPPORTING_POWER       | VARCHAR2(20)   | Y    |                  | 承重量                                              |
| WATERPROOF_STRENGTH    | VARCHAR2(10)   | Y    |                  | 防水强度                                            |
| FOR_A4_PAPER           | NUMBER(1)      | Y    |                  | 是否放A4文件                                        |
| STRAP_LENGTH           | VARCHAR2(20)   | Y    |                  | 肩带长度                                            |
| IS_SAMPLE              | NUMBER(1)      | Y    |                  | 试用装                                              |
| IS_GIFT                | NUMBER(1)      | Y    |                  | 赠品                                                |
| ACCESSORY_GOO_ID       | VARCHAR2(13)   | Y    |                  | 关联货号                                            |
| ACCESSORYNAME          | VARCHAR2(20)   | Y    |                  | 配件类型                                            |
| SALELEVEL              | VARCHAR2(1)    | Y    |                  | 畅销等级                                            |
| MEMBER_PERCENT         | NUMBER(5,2)    | Y    |                  | 会员折扣                                            |
| IS_BREAKABLE           | NUMBER(1)      | Y    |                  | 是否易碎品                                          |
| PACK_SPECS_SEND        | NUMBER(18,4)   | Y    |                  | 送货包装规格                                        |
| PACK_VOLUME_SEND       | NUMBER(18,4)   | Y    |                  | 送货包装体积                                        |
| IS_QC_REQUIRED         | NUMBER(1)      | Y    |                  | 是否质检                                            |
| PACKBASEAMOUNT         | NUMBER(18,4)   | Y    |                  | 包装基数                                            |
| IS_RECEIPT             | NUMBER(1)      | Y    | 1                | 是否开票                                            |
| MIN_WHOLESALERATIO     | NUMBER(6,4)    | Y    | 0.1              |                                                     |
| GOO_NAME_COMPARE       | VARCHAR2(20)   | Y    |                  | 品名对照                                            |
| RECEIPT_LASTMODIFIERID | VARCHAR2(6)    | Y    |                  | 是否开票上次更改人                                  |
| RECEIPT_AUDITOR        | VARCHAR2(6)    | Y    |                  | 是否开票审核人                                      |
| RECEIPT_AUDITTIME      | DATE           | Y    |                  | 是否开票审核时间                                    |
| RECEIPT_STATUS         | NUMBER(1)      | Y    |                  | 是否开票审核状态(0：不需审核，1：待审核，2：已审核) |
| IS_NETGOODS            | NUMBER(1)      | 0    | 是否电商专供款   |                                                     |
| DESIGN_INSP_SRC        | VARCHAR2(50)   | Y    |                  | 设计灵感来源                                        |
| DESIGN_INSP_SRC_INFO   | VARCHAR2(50)   | Y    |                  | 设计灵感来源信息                                    |

### 1.3.2 DIC_ARTICLES

| 字段             | 类型          | 为空 | 默认值    | 翻译         |
| ---------------- | ------------- | ---- | --------- | ------------ |
| GOO_ID           | VARCHAR2(13)  |      | 货号      |              |
| BARCODE          | VARCHAR2(13)  |      | 条码      |              |
| COLORNAME        | VARCHAR2(16)  |      | 颜色      |              |
| SIZENAME         | VARCHAR2(16)  | Y    |           | 码数         |
| ORDERBASEAMOUNT  | NUMBER(5)     | Y    |           | 订货基数     |
| ORDERSTARTAMOUNT | NUMBER(18,4)  | Y    |           | 起订量       |
| LACK             | NUMBER(1)     | 0    | 缺货      |              |
| SCALE            | NUMBER(5)     | Y    |           | 订货比例     |
| BAKCODE          | VARCHAR2(13)  |      |           |              |
| WHENTIME         | DATE          | Y    |           |              |
| ORDERENDDATE     | DATE          | Y    |           | 止订日期     |
| DIFF_ID1         | VARCHAR2(10)  |      | 差异编号1 |              |
| DIFF_ID2         | VARCHAR2(10)  | Y    |           | 差异编号2    |
| DIFF_ID3         | VARCHAR2(10)  | Y    |           | 差异编号3    |
| DIFF_ID4         | VARCHAR2(10)  | Y    |           | 差异编号4    |
| STDCOLORVALUE    | NUMBER(8)     | Y    |           | 标准色值     |
| STDCOLORNAME     | VARCHAR2(10)  | Y    |           | 标准色名称   |
| COLORTYPE        | VARCHAR2(8)   | Y    |           | 色类         |
| INSERTTIME       | DATE          | Y    |           | 新增时间     |
| LASTMODIFYTIME   | DATE          | Y    |           | 最近修改时间 |
| LASTPUBLISHTIME  | DATE          | Y    |           | 最近发布时间 |
| MEMO             | VARCHAR2(200) | Y    |           |              |
| NOT_EXCHANGE     | NUMBER(1)     | 0    | 禁止退换  |              |
| GRAM_WEIGHT      | VARCHAR2(20)  | Y    |           | 克重         |

