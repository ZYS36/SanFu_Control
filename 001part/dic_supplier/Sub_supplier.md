# 1.供应商模块

------

系统相关表

DIC_SUPPLIER  

```
select * from dic_supplier
```

DIC_SUPPLIER_BASE

```
select * from dic_supplier_base
```



## 1.1维护人员

### 1.1.1供应商维护部门

供应链管理部

财务部

#### 1.1.1.1岗位职责

供应链管 理部（具体岗位：供应商评估专员；跟单主管/专员）主要负责供应商的新增以及对供应商基本信息维护，如供应商名称、联系人、联系方式，预付比例等20余处信息维护，具体如下图：

![](https://jxmlxg.com/images/001.png)

财务部（商品中心财务部GZZ/义乌财务部YWZ）主要负责供应商的修改对供应商首付款方式进行维护，如供应商对公账号，身份证，付款方式，结算方式等10余处信息维护，具体如下图：

![](https://jxmlxg.com/images/002.png)

## 1.2程序控制

### 1.2.1新增程序控制

-  必填字段：供应商名称、联系人、电话号码、注册地址、供应商类型、供应商状态、公司类型、省份、城市、区县、法人代表、级别、票种、纳税人身份、社会信用代码。

a)  其中供应商名称只能包含中文或者括号，供应商名称不能重复，供应商名称不能超过100个字符

b)  供应商类型分为：ODM兼OEM、ODM、OEM、外协加工厂

c)  供应商状态分为：生效、失效

d)  公司类型分为：贸易型、制造型、贸易-制造型

e)  级别分为：A、B、C、D级

f)  票种分为：普票、专票

g)  纳税人身份分为：小规模纳税人、一般纳税人

h)  社会信用代码：不能重复

### 1.2.2修改程序控制

a)     在修改供应商时已经结束修改的供应商，不能再结束修改。

b)  修改供应商时只能修改当前用户修改的记录，若需修改他人记录，则需点击![](https://jxmlxg.com/images/003.png)后修改

c)  当财务人员在维护收付款方式时：

①付款方式为“现金“可以更改为“转账“，为“转账”的就不能修改为其他付款方式。

②付款方式为“现金”时，账户的相关信息必须为空。

③付款方式为“转账”时，必须填写相关账户信息。

④对公账号不为空时，与对公相关的收款人、收款人身份证号、开户银行不能为空。

⑤个人账号不为空时，与个人相关的收款人、收款人身份证号、开户银行不能为空。

d)  付款方式和结算方式都不能为空。

①结算方式不是"货到付款",首次付款日不能为空。

②结算方式是"货到付款",首次付款日必须为空。

③结算方式不是"货到付款",首次付款日为1月1次或者2月1次或者3月1次，首次付款日必须小于等于28。

④结算方式不是"货到付款",首次付款日为1月2次，首次付款日必须小于等于14。

⑤结算方式不是"货到付款",首次付款日为1月3次，首次付款日必须小于等于9。

⑥启用日期"一旦不为空后，就不能再修改。

e)  已发布的数据,不能修改主键、不能清空"最近发布时间"

### 1.2.3删除程序控制

a)     不能删除一个月前创建的供应商档案

b)     已发布的数据,不能删除（已发布表示已传中心）

## 1.3主/辅供应商关系

1. 主供应商维护在新增/修改供应商界面维护，维护完成后点击结束修改按钮表示主供应商维护完成。

2. 辅供应商维护在已审核供应商节点维护，已审核供应商选择你之前维护好的主供应商双击进入下层：供应商分部设置、已结束供应商设置、外协厂绑定

a)     供应商分部设置：新增辅供应商记录，维护后需点击结束修改按钮![](https://jxmlxg.com/images/004.png)

b)     已结束供应商设置：查看已设置分部供应商信息，如需修改点击![](https://jxmlxg.com/images/005.png)即可。

c)     外协厂绑定：用于外协厂绑定。

3. 当主供应商在新增/修改供应商界面点击结束修改按钮时，系统会进行以下数据处理：

a)     主供应商写入结束修改时间、最后修改时间、最后修改人

b)     将主供应商信息写入主供应商历史表（用于追踪当前人修改的数据信息）

c)     将主供应商信息写入辅供应商表，保证主辅供应商主要信息一致（子供应商可以维护：店号、分部号、状态、级别；该信息可以与主供应商不一致）

## 1.4税票审核

1. 税票审核功能主要是当主供应商的加价比例、批发占比主要信息发生变更时审核使用

2. 供应商的加价比例、批发占比主要信息发生变更时未审核会导致第二天中心服务器开单失败

## 1.5表结构

### 1.5.1dic_supplier

| 字段               | 类型          | 为空 | 默认值  | 翻译                                        |
| ------------------ | ------------- | ---- | ------- | ------------------------------------------- |
| SHO_ID             | VARCHAR2(4)   |      |         | 店号                                        |
| BRA_ID             | NUMBER(3)     | Y    |         | 部门编号                                    |
| SUP_ID             | VARCHAR2(5)   |      |         | 供应商编号                                  |
| SUP_NAME           | VARCHAR2(100) |      |         | 供应商                                      |
| LEGALPERSON        | VARCHAR2(8)   | Y    |         | 法人代表                                    |
| LINKMAN            | VARCHAR2(8)   |      |         | 联系人                                      |
| PHONENUMBER        | VARCHAR2(20)  |      |         | 电话号码                                    |
| ADDRESS            | VARCHAR2(140) |      |         | 注册地址                                    |
| YEARSALE           | NUMBER(18,4)  | Y    |         | 年产能                                      |
| DAYYIELD           | NUMBER(10)    | Y    |         | 日产量                                      |
| SALEPATH           | VARCHAR2(4)   | Y    |         | 销售渠道                                    |
| SUPRANK            | VARCHAR2(4)   |      | 'C'     | 级别                                        |
| CREATETIME         | ·             | Y    |         | 创建时间                                    |
| SUP_TYPE           | VARCHAR2(20)  |      |         |                                             |
| CREATETORID        | VARCHAR2(6)   | Y    |         | 创建员工号                                  |
| INSERTTIME         | DATE          |      | SYSDATE | 新增时间                                    |
| LASTMODIFYTIME     | DATE          |      | SYSDATE | 最近修改时间                                |
| LASTPUBLISHTIME    | DATE          | Y    |         | 最近发布时间                                |
| SUP_STATUS         | VARCHAR2(1)   |      | 'A'     | ·                                           |
| PARENTID           | VARCHAR2(5)   | Y    |         | 父供应商编号                                |
| COUNTYID           | NUMBER(10)    |      |         | 供应商地址所在区县                          |
| PROVINCEID         | NUMBER(10)    |      |         | 供应商地址所在省份                          |
| CITYNO             | NUMBER(10)    |      |         | 供应商地址所在城市                          |
| MEMO               | VARCHAR2(300) | Y    |         | 备注                                        |
| LASTRENAMETIME     | DATE          | Y    |         | 最近供应商名字变更时间                      |
| POSTCODE           | VARCHAR2(6)   | Y    |         |                                             |
| ATTACHNAME         | VARCHAR2(50)  | Y    |         |                                             |
| ATTACHMENT         | BLOB          | Y    |         |                                             |
| TAX_ID             | VARCHAR2(50)  | Y    |         | 纳税识别号                                  |
| TICKETSPECIES      | VARCHAR2(6)   | Y    |         | 票种：普票，专票                            |
| HIKERATIO          | NUMBER(12,4)  | Y    |         | 加价比例                                    |
| TAXPAYER           | NUMBER(1)     | Y    |         | 纳税人身份:0.小规模纳税人 1：一般纳税人     |
| SUPACCOUNTNO       | VARCHAR2(30)  | Y    |         | 对公账号                                    |
| SUPCASHIER         | VARCHAR2(100) | Y    |         | 对公收款人                                  |
| SUPIDCARD          | VARCHAR2(18)  | Y    |         | 对公身份证号                                |
| SUPBANKNAME        | VARCHAR2(100) | Y    |         | 对公开户行                                  |
| SUPPHONE           | VARCHAR2(15)  | Y    |         | 对公联系电话                                |
| PERSONACCOUNTNO    | VARCHAR2(30)  | Y    |         | 个人账号                                    |
| PERSONCASHIER      | VARCHAR2(30)  | Y    |         | 个人收款人                                  |
| PERSONIDCARD       | VARCHAR2(18)  | Y    |         | 个人身份证号                                |
| PERSONBANKNAME     | VARCHAR2(100) | Y    |         | 个人开户行                                  |
| PERSONPHONE        | VARCHAR2(15)  | Y    |         | 个人联系电话                                |
| SUPPAYMODE         | VARCHAR2(4)   | Y    |         | 付款方式                                    |
| SUPSETTLEMENT      | VARCHAR2(10)  | Y    |         | 结算方式                                    |
| CHECKLINKMAN       | VARCHAR2(10)  | Y    |         | 对账联系人                                  |
| CHECKPHONE         | VARCHAR2(15)  | Y    |         | 对账联系电话                                |
| BEGINUSEDATE       | DATE          | Y    |         | 启用日期                                    |
| EFFECTIVETIME      | DATE          | Y    |         | 纳税人身份生效日期                          |
| SUP_ID_BASE        | VARCHAR2(5)   | Y    |         | 基础供应商编号                              |
| AUDITTIME          | DATE          | Y    |         | 审核时间，对公帐户/加价比例最后一次审核时间 |
| PAUSE              | NUMBER(1)     |      | 0       | 禁用，供应商合并                            |
| BEGINDAY           | NUMBER(3)     | Y    |         | 首次付款日                                  |
| WHOLESALERATIO     | NUMBER(6,4)   | Y    | null    | 税票审核人                                  |
| WHOLESALEAUDITTIME | DATE          | Y    |         |                                             |
| WHOLESALEAUDITORID | VARCHAR2(10)  | Y    |         |                                             |
| FINISHTIME         | DATE          | Y    |         | 结束时间                                    |
| LASTMODIFIERID     | VARCHAR2(6)   | Y    |         | 最后更新时间                                |
| AUDITORID          | VARCHAR2(6)   | Y    |         | 审核人编号                                  |
| PREPAYPCT          | NUMBER(18,4)  | Y    |         | 预付比例                                    |
| PREECTIVETIME      | DATE          | Y    |         | 预付开始时间                                |
| PREFINISHTIME      | DATE          | Y    |         | 预付结束时间                                |

### 1.5.2 dic_supplier_base

| 字段             | 类型          | 为空 | 默认值     | 翻译                                    |
| ---------------- | ------------- | ---- | ---------- | --------------------------------------- |
| SUP_ID_BASE      | VARCHAR2(5)   |      | 供应商编号 |                                         |
| SUP_NAME         | VARCHAR2(100) |      | 供应商     |                                         |
| LEGALPERSON      | VARCHAR2(18)  | Y    |            | 法人代表                                |
| LINKMAN          | VARCHAR2(18)  |      | 联系人     |                                         |
| PHONENUMBER      | VARCHAR2(20)  |      | 电话号码   |                                         |
| ADDRESS          | VARCHAR2(140) |      | 注册地址   |                                         |
| SALEPATH         | VARCHAR2(4)   | Y    |            | 销售渠道                                |
| SUPRANK          | VARCHAR2(4)   | Y    | 'C'        | 级别                                    |
| CREATETIME       | DATE          |      |            | 创建时间                                |
| SUP_TYPE         | VARCHAR2(20)  |      | 供应商类型 |                                         |
| CREATETORID      | VARCHAR2(6)   | Y    |            | 创建员工号                              |
| INSERTTIME       | DATE          |      | SYSDATE    | 新增时间                                |
| LASTMODIFYTIME   | DATE          | Y    | SYSDATE    | 最近修改时间                            |
| LASTPUBLISHTIME  | DATE          | Y    |            | 最近发布时间                            |
| SUP_STATUS       | VARCHAR2(1)   | 'A'  | 供应商状态 |                                         |
| COUNTYID         | NUMBER(10)    | Y    |            | 供应商地址所在区县                      |
| PROVINCEID       | NUMBER(10)    | Y    |            | 供应商地址所在省份                      |
| CITYNO           | NUMBER(10)    | Y    |            | 供应商地址所在城市                      |
| MEMO             | VARCHAR2(200) | Y    |            | 备注                                    |
| LASTRENAMETIME   | DATE          | Y    |            | 最近供应商名字变更时间                  |
| POSTCODE         | VARCHAR2(6)   | Y    |            | 邮编                                    |
| TAX_ID           | VARCHAR2(50)  | Y    |            | 纳税识别号                              |
| TICKETSPECIES    | VARCHAR2(6)   | Y    |            | 票种：普票，专票                        |
| HIKERATIO        | NUMBER(12,4)  | Y    |            | 加价比例                                |
| TAXPAYER         | NUMBER(1)     | Y    |            | 纳税人身份:0.小规模纳税人 1：一般纳税人 |
| SUPACCOUNTNO     | VARCHAR2(30)  | Y    |            | 对公账号                                |
| SUPCASHIER       | VARCHAR2(100) | Y    |            | 对公收款人                              |
| SUPIDCARD        | VARCHAR2(18)  | Y    |            | 对公身份证号                            |
| SUPBANKNAME      | VARCHAR2(100) | Y    |            | 对公开户行                              |
| SUPPHONE         | VARCHAR2(15)  | Y    |            | 对公联系电话                            |
| PERSONACCOUNTNO  | VARCHAR2(30)  | Y    |            | 个人账号                                |
| PERSONCASHIER    | VARCHAR2(100) | Y    |            | 个人收款人                              |
| PERSONIDCARD     | VARCHAR2(18)  | Y    |            | 个人身份证号                            |
| PERSONBANKNAME   | VARCHAR2(100) | Y    |            | 个人开户行                              |
| PERSONPHONE      | VARCHAR2(15)  | Y    |            | 个人联系电话                            |
| SUPPAYMODE       | VARCHAR2(4)   | Y    |            | 付款方式                                |
| SUPSETTLEMENT    | VARCHAR2(10)  | Y    |            | 结算方式                                |
| CHECKLINKMAN     | VARCHAR2(10)  | Y    |            | 对账联系人                              |
| CHECKPHONE       | VARCHAR2(15)  | Y    |            | 对账联系电话                            |
| BEGINUSEDATE     | DATE          | Y    |            | 启用日期                                |
| EFFECTIVETIME    | DATE          | Y    |            | 纳税人身份生效日期                      |
| SUPACCOUNTNO_TMP | VARCHAR2(30)  | Y    |            | 上一版本对公账号                        |
| HIKERATIO_TMP    | NUMBER(12,4)  | Y    |            | 上一版本加价比例                        |
| LASTMODIFIERID   | VARCHAR2(6)   | Y    |            | 最后修改人ID                            |
| FINISHTIME       | DATE          | Y    |            | 结束时间                                |
| AUDITORID        | VARCHAR2(6)   | Y    |            | 审核人ID                                |
| AUDITTIME        | DATE          | Y    |            | 审核时间                                |
| WHOLESALERATIO   | NUMBER(6,4)   | Y    |            | 批发占比                                |
| ATTACHNAME       | VARCHAR2(50)  | Y    |            | 附件名称                                |
| ATTACHMENT       | BLOB          | Y    |            | 附件                                    |
| COMPANY_TYPE     | VARCHAR2(30)  | Y    |            | 公司类型                                |
| PREPAYPCT        | NUMBER(18,4)  | Y    |            | 预付比例                                |
| PREECTIVETIME    | DATE          | Y    |            | 预付开始时间                            |
| PREFINISHTIME    | DATE          | Y    |            | 预付结束时间                            |
| BEGINDAY         | NUMBER(3)     | Y    |            | 首次付款日                              |