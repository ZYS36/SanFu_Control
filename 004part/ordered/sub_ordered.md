# 1.订单

说明：本章节描述了待收货订单节点相关控制、订单结束正常结束收货控制以及异常订单定义逻辑。

## 1.1涉及岗位

业务所说的订单指的是采购单批准后的正式订单和熊猫订单窗口有所区别；本章节主要讲述的时涉及到订单的相关控制。订单涉及到的岗位有：备货员、调配、分部调价员、分货员、接收员、收货员、商品变价员、验货员

```
select max(1) from dual where (GetShopProperty('distribute',%sho_id%)=1 or GetShopProperty('SECONDWAREHOUSE',%sho_id%)=1)
  or (UserHasAction(%CurrentUserID%,'40,315')=1 and UserHasAction(%CurrentUserID%,'24,26,166')=1) 
```

## 1.2程序控制

### 1.2.1待收货订单模块程序控制

#### 1.2.1.1界面修改权限控制

修改语句可设置分货控制规则、分货控制店具体如下：

```
update ORDERED
set
 ACC_CTRL_RULE = :ACC_CTRL_RULE, ACC_CTRL_SHOPS = :ACC_CTRL_SHOPS,
 ACC_CTRL_EDITORID = %CurrentUserID%, ACC_CTRL_UPDTIME = SYSDATE
where
 ORD_ID = :OLD_ORD_ID
```

#### 1.2.1.2按钮权限控制

##### 1、更改交货日期按钮

按钮权限控制语句

```
select max(1) from dual
where UserHasAction(%CurrentUserID%,40)=1 and UserHasAction(%CurrentUserID%,'24,26')=1 
```

- 已经结束的订单不能修改交货日期
- 已经更改过交货日期的单据不能再次修改
- 订单未提取不可更改交货日期
- 更改的交货日期必须大于之前的交期
- 新交货日期与原交货日期相同，无需更改
- 设定的新交货日期不能比原交货日期大40天

##### 2、更改批发占比按钮

- 批发占比只能在0~1之间

按钮权限控制

```
SELECT MAX(1) FROM Mvusers a WHERE a.Userid = UPPER(%currentuserid%) AND a.Jobname IN ('跟单主管','跟单专员','跟单经理')   
```

按钮语句

```
Declare
P_I int;
Begin
  if @WHOLESALERATIO@ > 1 then 
    raise_application_error(-20002,'批发占比只能在0~1之间！');
  end if;
    update ordered set WHOLESALERATIO = @WHOLESALERATIO@  where ord_id in (%Selection%);
end; 
```

##### 3、更改是否质检按钮

- 只能设置存在已经结束并且未传WMS的预计到货单的订单

  按钮语句

  ```
  Update Asnorders a
     Set a.Is_Qc_Required = Case When @Is_Qc_Required@ = '是' Then 1 Else 0 End
   Where Exists (Select 1 From Ordered b
           Inner Join Asnordered c On b.Ord_Id = c.Ord_Id
           Where b.Ord_Id In (%Selection%) And c.Asn_Id = a.Asn_Id
             and c.finishtime is not null And c.Lastpublishtime Is Null)  
  ```

##### 4、结束订单

- 单据已经结束，不能执行该操作
- 不能结束有到货量的订单
- 订单正在分货，不能执行该操作
- 不能结束他人单据 
- 结束订单后该单不可再收货(提示)

```
Declare
  p_Lockcol Varchar2(15);
  p_i       Int;
Begin
  For Item In (Select a.Ord_Id From Ordered a Where a.Ord_Id In (%Selection%)) Loop
    Select Max(1) Into p_i From Dual Where Not Exists (Select 1 From Ordered Where Ord_Id = Item.Ord_Id);
    If p_i = 1 Then
      Raise_Application_Error(-20002, '单据不存在');
    Else
      Select Ord_Id Into p_Lockcol From Ordered Where Ord_Id = Item.Ord_Id For Update Nowait;
      Select Max(1) Into p_i From Dual 
       Where Exists (Select 1 From Ordered Where Ord_Id = Item.Ord_Id And Finishtime Is Not Null);
      If p_i = 1 Then
        Raise_Application_Error(-20002, '该单据已经结束，不能执行该操作');
      Else
        Select Max(1) Into p_i From Dual
         Where Exists (Select 1  From Orders
                 Where Ord_Id = Item.Ord_Id And Comeamount Is Not Null);
        If p_i = 1 Then
          Raise_Application_Error(-20002, '不能结束有到货量的订单');
        Else
          Select Max(1) Into p_i From Dual
           Where Exists (Select 1 From Orderaccept Where Ord_Id = Item.Ord_Id And Verifytime Is Null And Tabletype In ('I', 'P'))
              Or Exists (Select 1 From Orderaccept a
                          Inner Join Asnordered b On a.Ord_Id = b.Asn_Id And a.Tabletype In ('N', 'M')
                          Where a.Verifytime Is Null And b.Ord_Id = Item.Ord_Id)
              Or Exists (Select 1 From Orderaccept a
                   Inner Join Allocation b On a.Ord_Id = b.Doc_No And a.Tabletype = 'L'
                   Where a.Verifytime Is Null And b.Ord_Id = Item.Ord_Id);
          If p_i = 1 Then
            Raise_Application_Error(-20002,'该订单正在分货，不能执行该操作');
          Else
          
            --added 2018-8-20 by Huang Shumei 二级仓订单已经到达一级仓，不允许关闭 start
            Select Max(1) Into p_i From Dual
             Where Exists (Select 1 From Sup_Delivered t
                            Where t.Ord_Id = Item.Ord_Id And t.Dc_Accept_Date Is Not Null
                              And t.Sw_Accept_Date Is Null And t.Is_Direct = 0);
            If p_i = 1 Then
              Raise_Application_Error(-20002,
                                      '该订单已经到达一级仓，正准备发往二级仓，不能执行该操作');
            Else
              --added 2018-8-20 by Huang Shumei 二级仓订单已经到达一级仓，不允许关闭 end
            
              Update Ordered Set Finishtime = Sysdate,Closecauseno = 1,Operatorid = %Currentuserid%
               Where Ord_Id = Item.Ord_Id And Finishtime Is Null;
                 
              Update Reinforces Set Orderstatus = '已结束' Where Cancelled = 0 And Tabletype = 'I' And Tableid = Item.Ord_Id;
                 
              Update Reinforces Set Feedamount = 0 
               Where Cancelled = 0 And Tabletype = 'I' And Tableid = Item.Ord_Id And Feedamount Is Null;
                 
              Pupdateabendorder(Item.Ord_Id);
            
              /*二级仓订单手动关闭时，配货方案一并关闭 Added By ZhangQun 2017-11-21*/
              Update Allocation a Set a.Doc_Status = 'C', a.Finishtime = Sysdate
               Where a.Ord_Id = Item.Ord_Id
                 And Exists (Select 1 From Nsfdata.Ordered b Where b.Ord_Id = a.Ord_Id
                            --AND b.secondarysho_id IS NOT NULL
                         And b.Ord_Id = Item.Ord_Id)
                 And a.Doc_Status = 'A';
            End If;
          End If;
        End If;
      End If;
    End If;
  End Loop;
End;   
```

```
Select c.actionid,b.actioncaption,d.* From Sfsys.Action a
 Inner Join Sfsys.actions b On A.actionid = B.actionid
 Inner Join Sfsys.checkaction c On C.actionid = b.actionid
 Inner Join Sfsys.Checkactions d On C.checkid = d.checkid
 Where A.itemid = 2162 And A.actionid = 419、
```

##### 5、开始收货

按钮权限控制

```
Select Max(1)  From Dual
 Where (Getshopproperty('distribute', %Sho_Id%) = 1 Or Getshopproperty('SECONDWAREHOUSE', %Sho_Id%) = 1)
   And Canoperatebranch(%Currentuserid%, %Sho_Id%, %Bra_Id%) = 1
```

按钮语句

```
update Ordered set ConsigneeID=%CurrentUserID% where ord_id in (%Selection%) and OperatorID is not null and 
ConsigneeID is null and FinishTime is null and ord_id not in (select ord_id from Orders where GotAmount>0)
```

##### 6、录入QC专员

按钮权限控制

```
Select Max(1) From Dual
 Where (Getshopproperty('distribute', %Sho_Id%) = 1 Or
       Getshopproperty('SECONDWAREHOUSE', %Sho_Id%) = 1)
   And Userhasaction(%Currentuserid%, 84) = 1  Or %Currentuserid% In ('FQM')

```

按钮语句

```
Declare
P_I int;
Begin   
  select max(1) into p_i from users where userid =@QCID@;
  if p_i <> 1 then
     raise_application_error(-20002,'输入的员工号不合法，请重新输入！');
  end if;
   select max(1) into p_i from dual where exists(select * from ordered where finishtime is not null and ord_id in (%Selection%));
  if p_i =1 then 
    raise_application_error(-20002,'选中的某些订单已结束，无法录入跟单员！');
  end if;
    update ordered  set qcid =upper(@QCID@) where ord_id in (%Selection%);
end;
```

##### 7、录入跟单员

按钮权限控制

```
Select Max(1) From Dual
 Where (Getshopproperty('distribute', %Sho_Id%) = 1 Or
       Getshopproperty('SECONDWAREHOUSE', %Sho_Id%) = 1)
   And Userhasaction(%Currentuserid%, 84) = 1 Or %Currentuserid% In ('FQM')
```

按钮语句

```
Declare
P_I int;
Begin   
  select max(1) into p_i from users where userid =@merchandiserid@; 
  if p_i <> 1 then
     raise_application_error(-20002,'输入的员工号不合法，请重新输入！');
  end if;
   select max(1) into p_i from dual where exists(select * from ordered where finishtime is not null and ord_id in (%Selection%));
  if p_i =1 then 
    raise_application_error(-20002,'选中的某些订单已结束，无法录入跟单员！');
  end if;
    update ordered set merchandiserid =upper(@merchandiserid@)
    where ord_id in (%Selection%);
end;
```

##### 8、设置送货单

- 订单无ASN，无法设置为送货单
- 订单结束时间不为空，无法设置送货单
- 订单下ASN订单的预计到货时间为空，无法设置送货单
- 订单的订单量等于到货量，无法设置送货单

##### 9、提取订单

```
Declare
P_I int;
Begin
 for Item in (Select Ord_ID from Ordered where Ord_ID in (%Selection%) and FinishTime is null)
 Loop
  select max(1) into P_I from Ordered where Ord_ID=Item.Ord_ID and OperatorID is null;
  if P_I=1 then
   update Ordered set OperatorID=%CurrentUserID%,ExtractTime=sysdate where ord_id=Item.Ord_ID and OperatorID is null and FinishTime is null;  
   Update Orders set ConfirmAmount=OrderAmount,CONFIRMDELIVERDAYS=(select DeliverDate-trunc(CreateTime) from Ordered where Ord_ID=Item.Ord_ID) where Ord_ID=Item.Ord_ID;
   Update OrdersItem set ConfirmAmount=OrderAmount where Ord_ID=Item.Ord_ID;
   update Reinforces set OrderStatus='待收货' where Cancelled=0 and TableType='I'and TableID=Item.Ord_ID
     and TableID not in (select ord_id from Ordered where FinishTime is not null);
  else
   Update Ordered set OperatorID=%CurrentUserID% where Ord_ID=Item.Ord_ID and finishtime is null;
  end if;
 end loop;
end;
```

## 1.3订单结束收货及控制

### 1.3.1相关过程

```
Pendorder
```

### 1.3.2程序控制

- 单据已经结束不可操作
- 订单没有收货量不可操作
- 单据无分配不可操作
- 色码与系统不符不可操作
- 某些商品缺少色码的收货量不可操作
- 商品必须全部分配（之前可一单多品）
- 色码必须进行分配
- 单品收货量不可超出订货量+门店数
- 色码收货量不可超出订货量+门店数
- 不能对已经禁用的店进行分配
- 色码收货量之和与单品收货量不符时不可操作
- 色码量分配量之和与单品总分配量不符时不可操作
- 各店的分配量之和与单品收货量不符时不可操作
- 商品不是集中采购商品，不能操作

### 1.3.3收货流程

**第一步**

1. 将数据写入进仓单（Ingood）：此时进仓单类型位(p_Ingoodtype := 'PO'
2. 网店订单（WP）、现货订单（PP）、新店订单（NP）可直接通过订单关联收货流水取得最早送货日期（Supsendgoodsdate）
3. 集采（CP）、分采（SP）需通过订单关联预计到货单再关联收货流水取最早送货日期（Supsendgoodsdate）
4. 将单品收货量、供应商收货时间写入进货单商品表（INGOODS），进仓量位订单收货量、创建时间为当前时间、送货日期为收货流水最早时间

**第二步**

1. 单据类型为集采（CP）、分采（SP）则当分货量（ASSIGNAMOUNT）大于0时，用门店商品订单表（ORDERSHOPS）表单品单店的分货量更新欠货表的欠货量
2. 如果是现货单（PP）、（RP）单时，门店为订单的仓库，用该订单的仓库单品收货量更新该仓库单品欠货量
3. 如果是新店订单（NP）则更新对应仓库（GZN\YWN）单品欠货量
4. 如果是网店订单（WP），则更新GZW对应单品的欠货量

**第三步**

1. 将不存在Preparegoods该表的单品写入

**第四步**

1. 更新订单商品表（ORDERS）的到货量（原到货量+本次收货量），更新收货量为空

**第五步**

1. 将单品条码进货量写入进仓单色码表（INGOODSITEM）

2. 进仓单色码表的进仓量为本次收货量，增加批发数量写入

3. 批发数量计算规则：①当批发占比小于等于0时，批发数量为：0；

   ②若到货量小于（订单量乘于批发占比）向上取整，批发数量为：取（向上取整（订单量乘于批发占比），收货量）中的最小值再加上（到货量+收货量-订单量，0）中的最大值

   ③如果
   
   --待更新