# 1.采购申请单

## 1.1模块说明

1. 采购申请单共分为五个模块分别为：未完成申请单、待提交申请单、待批准申请单、已完成申请单、采购申请单明细，其中未完成申请单、待提交申请单、待批准申请单这三个模块涉及到功能操作；已完成申请单、采购申请单明细只涉及到数据查看。采购申请单涉及岗位为：企划（负责开单）、跟单（负责提单）
2. 节点权限控制为：

```
Select Max(1)
  From Dual
 Where (Nsfdata.Userhasaction(%Currentuserid%, '166') = 1 Or
       Nsfdata.Userhasaction(%Currentuserid%, '314') = 1) And
       (Nsfdata.GetShopProperty('Distribute',%Sho_Id%)=1)
```

### 1.2未完成申请单

#### 1.2.1导入模式程序控制

##### 1.2.1.1 INSERTSQL控制

1. 数据导入新增单据时，若单品已经订货（判断订货条件为是否存在FIRSTORDERFORCORP表）则该次开单为补单，否则为首单

```
Select Count(*) Into p_j From Firstorderforcorp h Where h.Goo_id=Substr(:Goodsid, 0, 6);
```



##### 1.2.1.2 确认生成申请单按钮控制（ACTIONID=92）

1. 导入的收货仓库应为直流仓(GZZ或YWZ)
2. 交货日期不能小于当前日期
3. 禁用商品不能开单
4. 若商品有色码必须导入色码的订货量
5. 业务在导入商品时只能导入本部分数据
6. 导入的商品和供应商必须是该供应商生产的
7. 目前导入的门店只能导入门店类型为（'287', '8479', '36893', '285'）可销售门店
8. 导入的数据中有门店为285类型，供应商代发只能为是
9. 导入的数据中试点店商品最低批发占比不能小于等于0
10. 导入的数据中有门店为285类型，不可与其他门店同时导入
11. 集采类型单据必须导入门店数据

##### 1.2.1.3余货开单确认按钮控制（ACTIONID=488）

1. 导入的收货仓库应为直流仓(GZZ或YWZ)
2. 交货日期不能小于当前日期
3. 禁用商品不能开单
4. 若商品有色码必须导入色码的订货量
5. 业务在导入商品时只能导入本部分数据
6. 导入的商品和供应商必须是该供应商生产的

##### 1.2.1.4配码比开单确认导入按钮控制（PP\CP）（ACTIONID=556）

1. 配码比必须是系统规定的配码比（存在SIZERATIO4SHOES）
2. 配码比订单同一个货号同一个配码比下的SKU数不能少于3个，否则无法使用配码比
3. 配码比所对应的顾客群必须和货号的顾客群一一对应
4. 导入的收货仓库应为直流仓(GZZ或YWZ)
5. 交货日期不能小于当前日期
6. 禁用商品不能开单
7. 若商品有色码必须导入色码的订货量
8. 业务在导入商品时只能导入本部分数据
9. 导入的商品和供应商必须是该供应商生产的
10. 目前导入的门店只能导入门店类型为（'287', '8479', '36893', '285'）可销售门店
11. 导入的数据中有门店为285类型，供应商代发只能为是
12. 导入的数据中试点店商品最低批发占比不能小于等于0
13. 导入的数据中有门店为285类型，不可与其他门店同时导入
14. 集采类型单据必须导入门店数据

#### 1.2.2新建模式程序控制

##### 1.2.2.1供应商选择按钮程序控制（PICKID=530415）

1. 选择该分部在合作的供应商
2. 选择对应仓库的合作的供应商
3. 供应商等级不能为空
4. 供应商状态为 A
5. 供应商不能是禁用的

```

{DECLARE
 V_CNT INT;
 V_STRRESULT VARCHAR2(4000);
BEGIN
IF Getshopproperty('SECONDWAREHOUSE', %Sho_Id%) = 1 THEN

    V_STRRESULT := 'SELECT Sup_Id, Sup_Name, Suprank
                      FROM Supplier a
                     WHERE Bra_Id = ''' || %Bra_Id% || '''
                       AND Sho_Id In (SELECT Sho_Id FROM Shop e WHERE Getshopproperty(''DISTRIBUTE'', e.Sho_Id) = 1
                       AND e.Pause = 0)
                 AND Nvl(a.Sup_Status, ''A'') = ''A''
                 AND Suprank IS NOT NULL
                 AND EXISTS (SELECT 1 FROM Mv_Goods_Supplier b
                              INNER JOIN Goods c ON c.Goo_Id = b.Goo_Id
                              WHERE b.Sup_Id = a.Sup_Id AND c.Pause = 0)';
 Else
  If %Ord_Id% Is Null or %goodid% is null Then
    v_Strresult := 'SELECT distinct a.sup_id, a.sup_name,a.SUPRANK
                      FROM supplier a
                     Inner Join Mv_Goods_Supplier b
                        On a.Sup_Id = b.Sup_Id
                     WHERE a.bra_id = ''' || %Bra_Id% || '''
                       AND a.Sho_Id = ''' || %Sho_Id% || '''
                       AND a.suprank IS NOT NULL
                       AND Nvl(a.Sup_Status, ''A'') = ''A''
                       and b.pause = 0';
  Else
    v_Strresult := 'Select Distinct a.Sup_Id, a.Sup_Name, a.Suprank
                      From Supplier a
                     Inner Join Mv_Goods_Supplier b
                        On a.Sup_Id = b.Sup_Id
                    Where a.Sho_Id = ''' || %Sho_Id% || '''
                      And a.Suprank Is Not Null
                      And a.Bra_Id =  ''' || %Bra_Id% || '''
                      And b.Pause = 0
                      AND Nvl(a.Sup_Status, ''A'') = ''A''
                      And B.Goo_Id in (select goo_id_request from orders_request where ord_id = ''' || %ord_id% || ''')';
  End If;
end if;
  :Strresult := v_Strresult;
End;}
```



##### 1.2.2.2触发器控制（ORDERED_REQUEST）

1. 不能新增、修改为二级仓订单
2. 一个采购申请单只能开一个单品
3. 货日期不能小于当前日期
4. 商品是否开票属性在主数据进行修改后未审核不能下单
5. 当单据为CP单时，门店色码明细必填
6. 当单据为CP单时，门店类型为285时，供应商代发只能为是；且不能和其他类型门店混合开单
7. 当供应商发生变更时，系统自动更新ORDERS_REQUEST表的进价为当前供应商生产进价
8. 鞋包分部开单时是否首单字段必填
9. 在主数据进行修改的供应商必须进行结束修改操作，否则不能下单
10. 供应商批发占比字段在选择供应商时系统自动赋值给ORDERED_REQUEST
11. 在新增和修改ORDERS_REQUEST表的商品时，系统自动赋值给商品最低批发占比

### 1.3待提交申请单

#### 1.3.1提交按钮程序控制

1. 收货仓库应为直流仓(GZZ或YWZ)
2. 当单品号订货量不等于色码汇总量时必须点击【更新总量】按钮再提交
3. 商品订货量必须时商品的订货基数倍数
4. 商品订货必须当前时间大于等于起订日期
5. 已止定的商品不能提交

### 1.4待批准申请单

#### 1.4.1批准按钮程序控制

1. 商品必须是供应商所生产的商品
2. 批准采购单时会将该供应商所生产的商品进价更新到ORDERS_REQUSET表进价字段
3. MERCHANDISERID更新为当前人
4. 批准单据时是否首单字段必填

#### 1.4.2批准按钮数据流向说明

1. 写入ORDERED表，订单头表的创建时间为当前批准时间;更新正式订单号至NEW_ORD_ID；更新采购单状态为3；更新采购单结束时间为当前时间

2. 写入ORDERS表，当采购单表是否质检为空时，则给默认值【是】

3. 写入ORDERSITEM表

4. 写入ORDERSHOPS\ORDERSHOPSITEM门店表

5. 更新单品（色码）单店欠货表GOODSHORT\GOODSHORTS的订货量、欠货量

6. 分采类型单据会进行回写订货表数据：

   ①更新REINFORCE_TMP表PROCESSTIME为当前时间；

   ②数据回写至：REINFORCE\REINFORCES\REINFORCESITEM

### 1.5表结构

#### 1.5.1 ORDERED_REQUEST

| 字段            | 类型          | 为空 | 默认值 | 翻译                    |
| --------------- | ------------- | ---- | ------ | ----------------------- |
| ORD_ID          | VARCHAR2(15)  |      |        |                         |
| ORDER_SHO_ID    | VARCHAR2(4)   | Y    |        |                         |
| BRA_ID          | VARCHAR2(2)   |      |        |                         |
| SUP_ID          | VARCHAR2(5)   |      |        |                         |
| DELIVERDATE     | DATE          |      |        |                         |
| CREATETIME      | DATE          |      |        |                         |
| FINISHTIME      | DATE          | Y    |        |                         |
| OPERATORID      | VARCHAR2(6)   | Y    |        |                         |
| CONFIRMTIME     | DATE          | Y    |        |                         |
| CLOSECAUSENO    | NUMBER(2)     | Y    |        |                         |
| PO_TYPE         | VARCHAR2(6)   |      |        |                         |
| MERCHANDISERID  | VARCHAR2(6)   | Y    |        |                         |
| MEMO            | VARCHAR2(200) | Y    |        |                         |
| PREPAYPCT       | NUMBER(18,4)  | Y    | NULL   | 预付比例                |
| ISPREPAYED      | NUMBER        | Y    |        | 是否预付                |
| RATIOID         | NUMBER(2)     | Y    |        |                         |
| HIKERATIO       | NUMBER(12,4)  | Y    |        |                         |
| STATUS          | NUMBER(2)     | Y    |        | 参考RPM_STATUS          |
| NEW_ORD_ID      | VARCHAR2(15)  | Y    |        | 生成的订单号            |
| SECONDARYSHO_ID | VARCHAR2(15)  | Y    |        |                         |
| ISFIRSTORDERED  | NUMBER(2)     | Y    | NULL   | 是否首单：1是,0否       |
| SEND_BY_SUP     | NUMBER(1)     | Y    |        | 是否供应商代发：1是,0否 |
| SAMPLE_ORD      | NUMBER(1)     | Y    |        | 是否试用装：1是,0否     |
| WHOLESALERATIO  | NUMBER(6,4)   | Y    | 0      | 批发占比                |
| IS_MUCH_GOODS   | NUMBER(1)     | Y    |        | 是否多货：1是,0否       |
| IS_VIRTUAL      | NUMBER(1)     | Y    | 0      | 是否虚拟订单：1是,0否   |

#### 1.5.2 ORDERS_REQUEST

| 字段               | 类型          | 为空 | 默认值 | 翻译              |
| ------------------ | ------------- | ---- | ------ | ----------------- |
| ord_id             | varchar2(15)  |      |        |                   |
| goo_id_request     | varchar2(13)  |      |        |                   |
| inprice            | number(18,4)  | y    |        |                   |
| orderstartamount   | number(18,4)  | y    |        |                   |
| orderamount        | number(18,4)  |      |        |                   |
| colorsize          | blob          | y    |        |                   |
| deliverdate        | date          | y    |        |                   |
| confirmamount      | number(18)    | y    |        |                   |
| confirmdeliverdays | number(38)    | y    |        |                   |
| fabricdays         | number(38)    | y    |        |                   |
| cutdays            | number(38)    | y    |        |                   |
| needledays         | number(38)    | y    |        |                   |
| technicsdays       | number(38)    | y    |        |                   |
| tidyupdays         | number(38)    | y    |        |                   |
| productstatus      | varchar2(6)   | y    |        |                   |
| pd_sup_id          | varchar2(5)   | y    |        |                   |
| memo               | varchar2(300) | y    |        |                   |
| supsendgoodsdate   | date          | y    |        |                   |
| material_source    | varchar2(8)   | y    |        |                   |
| r_sup_name         | varchar2(128) | y    |        |                   |
| is_late            | varchar2(300) | y    |        |                   |
| r_lathe_num        | number        | y    |        |                   |
| p_lathe_num        | number        | y    |        |                   |
| confirmtime        | date          | y    |        |                   |
| confirmid          | varchar2(6)   | y    |        |                   |
| audittime          | date          | y    |        |                   |
| p_processid        | number        | y    |        |                   |
| r_processid        | number        | y    |        |                   |
| prearrivetime      | date          | y    |        |                   |
| reasoncode         | number(2)     | y    |        |                   |
| inprice_notax      | number(20,4)  | y    |        |                   |
| tax_rate           | number(12,4)  | y    |        |                   |
| isfirstordered     | number(2)     | y    | null   | 是否首单：1是,0否 |
| outdays            | number(38)    | y    |        |                   |
| is_qc_required     | number(1)     | y    | 1      | 是否质检          |
| urgency            | number(1)     | y    | 5      | 紧急度            |

#### 1.5.3 ORDERSITEM_REQUEST

| ORD_ID          | VARCHAR2(15)  |      |      |      |
| --------------- | ------------- | ---- | ---- | ---- |
| GOO_ID_REQUEST  | VARCHAR2(13)  |      |      |      |
| BARCODE_REQUEST | VARCHAR2(13)  |      |      |      |
| ORDERAMOUNT     | NUMBER(18,4)  |      |      |      |
| MEMO            | VARCHAR2(100) | Y    |      |      |