# 1.订单

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
 ACC_CTRL_RULE = :ACC_CTRL_RULE,
 ACC_CTRL_SHOPS = :ACC_CTRL_SHOPS,
 ACC_CTRL_EDITORID = %CurrentUserID%,
 ACC_CTRL_UPDTIME = SYSDATE
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
SELECT MAX(1) FROM Mvusers a
         WHERE a.Userid = UPPER(%currentuserid%)
           AND a.Jobname IN ('跟单主管','跟单专员','跟单经理')   
```

按钮语句

```
Declare
P_I int;
Begin
   
  if @WHOLESALERATIO@ > 1 then 
    raise_application_error(-20002,'批发占比只能在0~1之间！');
  end if;

    update ordered
      set WHOLESALERATIO = @WHOLESALERATIO@
    where ord_id in (%Selection%);

end; 
```

##### 3、更改是否质检按钮

- 只能设置存在已经结束并且未传WMS的预计到货单的订单

  按钮语句

  ```
  Update Asnorders a
     Set a.Is_Qc_Required = Case When @Is_Qc_Required@ = '是' Then 1 Else 0 End
   Where Exists (Select 1
            From Ordered b
           Inner Join Asnordered c
              On b.Ord_Id = c.Ord_Id
           Where b.Ord_Id In (%Selection%)
             And c.Asn_Id = a.Asn_Id
             and c.finishtime is not null
             And c.Lastpublishtime Is Null)  
  ```

##### 4、结束订单

##### 5、开始收货

##### 6、录入QC专员

##### 7、录入跟单员

##### 8、设置送货单

##### 9、提取订单

​       



​    





