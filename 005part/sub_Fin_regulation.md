# 1.财务规范化-主数据控制

## 1.1财务规范化-供应商相关控制

### 1.1.1主供应商新增/修改相关控制

-  必填字段：供应商名称、联系人、电话号码、注册地址、供应商类型、供应商状态、公司类型、省份、城市、区县、法人代表、级别、票种、纳税人身份、社会信用代码。
-  其中供应商名称只能包含中文或者括号，供应商名称不能重复，供应商名称不能超过100个字符
-  票种分为：普票、专票
-  纳税人身份分为：小规模纳税人、一般纳税人
-  社会信用代码：不能重复
-  在修改供应商时已经结束修改的供应商，不能再结束修改。
-  修改供应商时只能修改当前用户修改的记录，若需修改他人记录，则需点击![](sanfu.jxmlxg.com/gitbook/images/003.png)后修改
-  预付功能：预付比例、预付开始时间、预付结束时间，预付比例一旦设置则预付开始时间、结束时间不能为空

### 1.1.2主/辅供应商继承关系

- 子供应商在主供应商新增完成后点击结束修改按钮，子供应商自动继承（除仓库、分部、供应商状态、供应商级别、供应商批发占比、供应商加价比例、备注外）主供应商的其他属性

  ```
  Select * From mdmsys.actions a Where a.actionid = 9050;
  ```

- 子供应商设置了仓库和分部后如果数据已经传给中心那么将不能再修改

  ```
  Create Or Replace Trigger Tr_Bf_IU_DIC_Supplier
  Before Insert or Update on MDMDATA.DIC_SUPPLIER
  For each Row
  Begin
    If :New.Parentid Is Null And :New.Sup_Name <> :Old.Sup_Name And
       Lengthb(:New.Sup_Name) > 100 Then
      Raise_Application_Error(-20002, '供应商名称不能超过100个字符！');
    End If;
    If Inserting Then
      If :New.Parentid Is Null And :New.Sup_Name <> Nvl(:Old.Sup_Name, ' ') Then
        :New.Sup_Name := :New.Sup_Name || '*';
      End If;
    Elsif Updating Then
      If (:Old.Lastpublishtime Is Not Null Or
         :New.Lastpublishtime Is Not Null) And :Old.Sho_Id <> :New.Sho_Id Then
        Raise_Application_Error(-20002, '已经发布的供应商数据不可修改仓库！');
      End If;
    End If;
  End;
  ```

  

-  已经发布的子供应商除供应商状态、供应商级别、供应商批发占比、供应商加价比例、备注外其他属性不能修改

## 1.2财务规范化-商品相关控制

- 映射字段、税票编码、执行标准、安全技术类别：根据商品税票编码基础表数据给默认值（DIC_CLS_TAX）

- 最低批发占比：所有分部开票供应商设置为【0.1】

- 小规模税率：所有分部默认值为【0.01】

  ```
  select 0.10 MIN_WHOLESALERATIO,0.01 small_taxrate from dual
  ```

  

# 2.财务规范化-中心控制

## 2.1财务规范化-订货篮订货功能控制

- 所选商品如果不开票则不可加入试点店

  ```
  Select * From Sfsys.Handles a Where A.handleid = 90003
  ```

  

## 2.2财务规范化-采购单控制（SP\CP）

- 导入的数据中试点店商品最低批发占比不能小于等于0

- 供应商批发占比字段在选择供应商时系统自动赋值

  ```
  create or replace trigger TR_UPDATE_WHOSALERATIO
  before UPDATE OF SUP_ID ON ordered_request
  for each ROW
  Declare
    P_I NUMBER(6,4);
  BEGIN
    if :new.SUP_ID is not null THEN
    select s.wholesaleratio into p_i from supplier s where s.sup_id=:new.SUP_ID;
    :new.wholesaleratio := p_i;
    end if;
  END
  ```

- 在新增和修改ORDERS_REQUEST表的商品时，系统自动将商品的最低批发占比赋值给最低批发占比字段

- 如商品是不开票则不能下单给试点店

## 2.3财务规范化-ASN预约控制（SP\CP）

- 如商品是不开票则不分货给试点店，系统自动将试点店订货量分给GZY

  ```
  PAutoAssign
  ```

## 2.4财务规范化-调配控制

- 基于现货订单和仓库调配单，如商品不开票则不能调配至试点店

```
--checkid = 767 
Select '配货单号[' || a.Alloc_Id || ']中商品[' || a.Goo_Id || ']属性为不开发票,不能调配至试点店[' || a.Sho_Id || ']'
  From Alc_Allocs a
 Inner Join Goods b On a.Goo_Id = b.Goo_Id And Nvl(b.Is_Receipt, 0) = 0
 Where a.Alloc_Id In (%Selection%) And a.Final_Assignamount > 0
   And Exists (Select 1 From Sfinterface.Pl_Dic_Code_Detail t
         Where t.Code_Type = 'STD_SHOP' And t.Pause = 0 And a.Sho_Id = t.Code_Value)
   And Rownum <= 5
```



## 2.5财务规范化-调拨单控制

- 试点店商品可调拨至非试点店、电商、试点店

- 非试点店商品可以调拨至非试点店

- 非试点店商品不能调拨至试点店、电商

- 电商商品可调拨至试点店、非试点店

- 目标店接受商品时不控制商品是否开票与门店属性关系

- 仓库调拨至试点店/电商的商品，若包已发出则不控制；否则只能开票的商品调拨至试点店/电商

  ```
  --试点店
  select code_value from sfinterface.pl_dic_code_detail e
   where sub_code ='SHO_ID' and pause=0 and code_type='STD_SHOP'
   --电商
  select code_value from sfinterface.pl_dic_code_detail
   where sub_code ='SHO_ID' and pause=0 and code_type='ON_SHOP
  ```

  

## 2.6 财务规范化-预付比例控制

- 发起预付款系统默认付款方广州，可编辑修改
- 付款方是广州，预付累计金额不能超订单总金额*预付比例,如果预付款比例为100%，则广州付款金额不能超订单总金额*预付比例*（1-批发比例）
- 付款方修改为平潭则预付平潭累计金额不能超订单总金额*预付比例*批发比例
- 多次预付累计总金额不能超供应商订单总金额*预付比例
- 发起申请时间，必须是供应商预付款比例的有效期间，供应商主档有该预付款的有效起始时间，有效截止时间
- 预付与应付对冲逻辑：在生成应付单后按订单+付款方自动扣减预付金额



