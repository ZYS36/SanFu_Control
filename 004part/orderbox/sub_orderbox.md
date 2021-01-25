# 1.订货篮

## 1.1涉及岗位

1. 加入订货篮（HANDLE=90003）功能主要是：地区主管、区域买手使用

2. 加入订货篮按钮权限控制语句为：

   ```
   select max(1) from dual where userhasaction(%CurrentUserID%, '28,89,72')=1
   and %DataShopID% in (SELECT sho_id FROM SHOP WHERE getshopproperty('AREA',SHO_ID)=1 AND PAUSE=0)
   ```

## 1.2程序控制

1. 商品未禁用（PAUSE=0）

2. 开票审核状态不能为未审核（RECEIPT_STATUS<>1）

3. 商品必须已经定价

4. 商品类别为：1

5. 非集中采购商品（ISUM=0）

6. 所选商品分部必须在该门店中经营（SHOPBRANCH）

7. 止订日期为空或者止订日期大于当前日期

8. 起订日期为空或者起订日期小于明天

9. 所选商品款式和分部必须存在款式分部表（PATTERNBRANCH）且有效

10. 所选商品不存在商品不可订门店表中（GOODSOFNOSHOP）

11. 所选商品不存在商品不可订大区表中（GOODSOFNOAREA）

12. 所选商品不存在缺货（LACK <>0）或者（HANDGOODS）表库存大于0
13. 所选商品必须设置订货行程且当天必须满足订货行程（订货行程设置企划告知IT，由IT在中心-分部-订货行程设定窗口设定）
14. 所选商品不存在门店不可选款表（NOTORDERSHOPPATTERN）
15. 所选商品不存在分部不可选款表（NOTORDERPATTERN）
16. 所选商品不存在不存在门店不可定商品表（NOTORDERGOODS）
17. 所选商品不开票则不可加入试点店

## 1.3按钮代码

```
declare
p_i int;
p_a int;
begin
 for item in (select Sho_ID from Shop where sho_ID in (@BUYERSHOPIDS@)) loop

  for item2 in (select goo_id, nvl(a.MIN_WHOLESALERATIO,0) as MIN_WHOLESALERATIO from goods where goo_id in (%selection%)) loop
   select max(1) into p_i from SFINTERFACE.PL_DIC_CODE_DETAIL a where a.code_value = item.sho_id
                 and  a.SUB_CODE = 'SHO_ID'
                 and a.CODE_TYPE = 'STD_SHOP';
    if  p_i Is null or (p_i is not null and item2.MIN_WHOLESALERATIO < = 0) Then
      P_a :=1 ;
     else
     p_a :=0 ;
     end if;
    insert into OrderBox(sho_id,goo_id,Amount,CreateTime,IsHand,OperatorID, Safeamount,ADVICEPRICE)
    select distinct Item.Sho_ID,a.goo_id,nvl(OrderBaseAmount,1),sysdate,0,%CurrentUserID%, g.safeamount,a.ADVICEPRICE
      from goods a
      left join (select goo_id from GoodsOfYesShop where sho_id=Item.Sho_ID) b on a.goo_id=b.goo_id
      left join (select goo_id from GoodsOfYesArea where are_id=%Are_ID%) c on a.goo_id=c.goo_id
      left join Schedule d on a.bra_id=d.bra_id and NVL(a.GooStyle,d.GooStyle)=d.GooStyle and a.Pattern=d.Pattern
      left join HandGoods e on a.goo_ID=e.goo_ID
      LEFT JOIN pl_dic_categorygroup f ON f.categorygroupno = a.categorygroupno
      left join shopfirstorders g on g.sho_id=item.sho_id and g.goo_id=a.goo_id
     where a.goo_id in (item2.goo_id)
       and a.accessoryname is null
       and a.accessory_goo_id is null
       and a.goo_id not in (select goo_id from OrderBox where sho_id=Item.Sho_ID)
       and a.Pause=0
       and a.Price is not null
       and a.GooTypeID='1'
       and a.Isum=0
       and a.bra_id in (select bra_id from ShopBranch where sho_id=Item.Sho_ID and Pause=0)
       and (a.OrderStartDate is null or a.OrderStartDate<sysdate+1)
       and (a.OrderEndDate is null or a.OrderEndDate>=trunc(sysdate))
       and exists (select 1 from PatternBranch where PatternBranch.bra_id=a.bra_id
                     and PatternBranch.Pattern=a.Pattern and PatternBranch.Pause=0)
       and a.goo_id not in (select goo_id from GoodsOfNoShop where sho_id=Item.Sho_ID)
       and a.goo_id not in (select goo_id from GoodsOfNoArea where are_id=%Are_ID%)
       and (b.goo_id is not null or c.goo_id is not null or (a.YesShop is null and a.YesArea is null))
       and (Lack=0 or e.HandStockAmount>0)
       and (instr(';'||d.WeekName||';',';'||to_char(sysdate,'day')||';')>0
           or instr(';'||d.WeekName||';',';'||to_char(sysdate+1,'day')||';')>0
           or trunc(sysdate)-nvl(trunc(a.PriceTime),trunc(a.CreateTime))<=2
           or a.goo_id in (select goo_id from BestCommends where Cmd_ID in (select Cmd_ID from BestCommend where trunc(sysdate)=CommendDate))
           or exists (select 1 from Shop where trunc(sysdate)-trunc(FirstSaleTime)<=3 and Sho_ID=Item.Sho_ID)
           or e.HandStockAmount>0
           or GetShopProperty('Prepare',Item.Sho_ID)=1)
       and not exists (Select 1 from NotOrderShopPattern where sho_ID=Item.Sho_ID and Bra_ID=a.Bra_ID and Pattern=a.Pattern)
       and not exists (Select 1 From NotOrderPattern where Bra_ID=a.Bra_ID and Pattern=a.Pattern)
       and not exists (Select 1 From NotOrderGoods where Sho_ID=Item.Sho_ID and goo_ID=a.goo_ID)
       AND A.BRA_ID NOT IN ('08')
       and 1 = p_a
     UNION ALL
    select distinct Item.Sho_ID,a.goo_id,nvl(OrderBaseAmount,1),sysdate,0,%CurrentUserID%, g.safeamount,a.ADVICEPRICE
      from goods a
      left join (select goo_id from GoodsOfYesShop where sho_id=Item.Sho_ID) b on a.goo_id=b.goo_id
      left join (select goo_id from GoodsOfYesArea where are_id=%Are_ID%) c on a.goo_id=c.goo_id
      left join Schedule d on a.bra_id=d.bra_id and NVL(a.GooStyle,d.GooStyle)=d.GooStyle and a.Pattern=d.Pattern
      left join HandGoods e on a.goo_ID=e.goo_ID
      LEFT JOIN pl_dic_categorygroup f ON f.categorygroupno = a.categorygroupno
      left join shopfirstorders g on g.sho_id=item.sho_id and g.goo_id=a.goo_id
     where a.goo_id in (item2.goo_id)
       and a.accessoryname is null
       and a.accessory_goo_id is null
       and a.goo_id not in (select goo_id from OrderBox where sho_id=Item.Sho_ID)
       and a.Pause=0
       and a.Price is not null
       and a.GooTypeID='1' and a.Isum=0
       and a.bra_id in (select bra_id from ShopBranch where sho_id=Item.Sho_ID and Pause=0)
       and (a.OrderStartDate is null or a.OrderStartDate<sysdate+1)
       and (a.OrderEndDate is null or a.OrderEndDate>=trunc(sysdate))
       and exists (select 1 from PatternBranch where PatternBranch.bra_id=a.bra_id
                     and PatternBranch.Pattern=a.Pattern and PatternBranch.Pause=0)
       and a.goo_id not in (select goo_id from GoodsOfNoShop where sho_id=Item.Sho_ID)
       and a.goo_id not in (select goo_id from GoodsOfNoArea where are_id=%Are_ID%)
       and (b.goo_id is not null or c.goo_id is not null or (a.YesShop is null and a.YesArea is null))
       and (Lack=0 or e.HandStockAmount>0)
       and (instr(';'||d.WeekName||';',';'||to_char(sysdate,'day')||';')>0
           or instr(';'||d.WeekName||';',';'||to_char(sysdate+1,'day')||';')>0
           or trunc(sysdate)-nvl(trunc(a.PriceTime),trunc(a.CreateTime))<=2
           or a.goo_id in (select goo_id from BestCommends where Cmd_ID in (select Cmd_ID from BestCommend where trunc(sysdate)=CommendDate))
           or exists (select 1 from Shop where trunc(sysdate)-trunc(FirstSaleTime)<=3 and Sho_ID=Item.Sho_ID)
           or e.HandStockAmount>0
           or GetShopProperty('Prepare',Item.Sho_ID)=1)
       and not exists (Select 1 from NotOrderShopPattern where sho_ID=Item.Sho_ID and Bra_ID=a.Bra_ID and Pattern=a.Pattern)
       and not exists (Select 1 From NotOrderPattern where Bra_ID=a.Bra_ID and Pattern=a.Pattern)
       and not exists (Select 1 From NotOrderGoods where Sho_ID=Item.Sho_ID and goo_ID=A.GOO_ID)
       AND A.BRA_ID = '08'
       and A.CATEGORYGROUPNO NOT IN (255,258,260)
       and 1 = p_a
     UNION ALL
    select distinct Item.Sho_ID,a.goo_id,nvl(OrderBaseAmount,1),sysdate,0,%CurrentUserID%, g.safeamount,a.ADVICEPRICE
      from goods a
      left join (select goo_id from GoodsOfYesShop where sho_id=Item.Sho_ID) b on a.goo_id=b.goo_id
      left join (select goo_id from GoodsOfYesArea where are_id=%Are_ID%) c on a.goo_id=c.goo_id
      left join Schedule d on a.bra_id=d.bra_id and NVL(a.GooStyle,d.GooStyle)=d.GooStyle and a.Pattern=d.Pattern
      left join HandGoods e on a.goo_ID=e.goo_ID
      LEFT JOIN pl_dic_categorygroup f ON f.categorygroupno = a.categorygroupno
      left join shopfirstorders g on g.sho_id=item.sho_id and g.goo_id=a.goo_id
     where a.goo_id in (item2.goo_id)
       and a.accessoryname is null
       and a.accessory_goo_id is null
       and a.goo_id not in (select goo_id from OrderBox where sho_id=Item.Sho_ID)
       and a.Pause=0
       and a.Price is not null
       and a.GooTypeID='1'
       and a.Isum=0
       and a.bra_id in (select bra_id from ShopBranch where sho_id=Item.Sho_ID and Pause=0)
       and (a.OrderStartDate is null or a.OrderStartDate<sysdate+1)
       and (a.OrderEndDate is null or a.OrderEndDate>=trunc(sysdate))
       and exists(select 1 from PatternBranch where PatternBranch.bra_id=a.bra_id
                     and PatternBranch.Pattern=a.Pattern and PatternBranch.Pause=0)
       and a.goo_id not in (select goo_id from GoodsOfNoShop where sho_id=Item.Sho_ID)
       and a.goo_id not in (select goo_id from GoodsOfNoArea where are_id=%Are_ID%)
       and (b.goo_id is not null or c.goo_id is not null or (a.YesShop is null and a.YesArea is null))
       and (Lack=0 or e.HandStockAmount>0)
       and (instr(';'||d.WeekName||';',';'||to_char(sysdate,'day')||';')>0
           or instr(';'||d.WeekName||';',';'||to_char(sysdate+1,'day')||';')>0
           or trunc(sysdate)-nvl(trunc(a.PriceTime),trunc(a.CreateTime))<=2
           or a.goo_id in (select goo_id from BestCommends where Cmd_ID in (select Cmd_ID from BestCommend where trunc(sysdate)=CommendDate))
           or exists(select 1 from Shop where trunc(sysdate)-trunc(FirstSaleTime)<=3 and Sho_ID=Item.Sho_ID)
           or e.HandStockAmount>0
           or GetShopProperty('Prepare',Item.Sho_ID)=1)
       and not exists (Select 1 from NotOrderShopPattern where sho_ID=Item.Sho_ID and Bra_ID=a.Bra_ID and Pattern=a.Pattern)
       and not exists (Select 1 From NotOrderPattern where Bra_ID=a.Bra_ID and Pattern=a.Pattern)
       and not exists (Select 1 From NotOrderGoods where Sho_ID=Item.Sho_ID and goo_ID=a.goo_ID)

       AND (EXISTS (SELECT 1 FROM firstorderforcorp WHERE GOO_ID = A.GOO_ID)
        or EXISTS (select 1 from ORDERBOXFORRATIO
             where sho_id=Item.Sho_ID and goo_id = a.goo_id)
        or EXISTS (select 1 from reinforce_tmp t1
             inner join reinforces_tmp t2 on t1.rnf_id = t2.rnf_id
             where t1.sho_id=Item.Sho_ID and t2.goo_id = a.goo_id AND t1.Createtime >= ( sysdate - 3)))

       AND A.BRA_ID = '08'
       and A.CATEGORYGROUPNO IN (255,258,260)
       and 1 = p_a
       ;

    insert into ORDERBOXFORRATIO(sho_id,goo_id,DIFF_ID1,Amount,CreateTime,IsHand,OperatorID, Safeamount,ADVICEPRICE)
    select distinct Item.Sho_ID,a.goo_id,H.Diff_Id1,nvl(OrderBaseAmount,1),sysdate,0,%CurrentUserID%, g.safeamount,a.ADVICEPRICE
      from goods a
      left join (select goo_id from GoodsOfYesShop where sho_id=Item.Sho_ID) b on a.goo_id=b.goo_id
      left join (select goo_id from GoodsOfYesArea where are_id=%Are_ID%) c on a.goo_id=c.goo_id
      left join Schedule d on a.bra_id=d.bra_id and NVL(a.GooStyle,d.GooStyle)=d.GooStyle and a.Pattern=d.Pattern
      left join HandGoods e on a.goo_ID=e.goo_ID
      LEFT JOIN pl_dic_categorygroup f ON f.categorygroupno = a.categorygroupno
      left join shopfirstorders g on g.sho_id=item.sho_id and g.goo_id=A.GOO_ID
      LEFT JOIN (SELECT DISTINCT GOO_ID,DIFF_ID1 FROM ARTICLES ) h ON A.GOO_ID = H.Goo_Id
     where a.goo_id in (item2.goo_id)
       and a.accessoryname is null
       and a.accessory_goo_id is null
       and a.goo_id not in (select goo_id from ORDERBOXFORRATIO where sho_id=Item.Sho_ID)
       and a.Pause=0
       and a.Price is not null
       and a.GooTypeID='1'
       and a.Isum=0
       and a.bra_id in (select bra_id from ShopBranch where sho_id=Item.Sho_ID and Pause=0)
       and (a.OrderStartDate is null or a.OrderStartDate<sysdate+1)
       and (a.OrderEndDate is null or a.OrderEndDate>=trunc(sysdate))
       and exists(select 1 from PatternBranch where PatternBranch.bra_id=a.bra_id
                     and PatternBranch.Pattern=a.Pattern and PatternBranch.Pause=0)
       and a.goo_id not in (select goo_id from GoodsOfNoShop where sho_id=Item.Sho_ID)
       and a.goo_id not in (select goo_id from GoodsOfNoArea where are_id=%Are_ID%)
       and (b.goo_id is not null or c.goo_id is not null or (a.YesShop is null and a.YesArea is null))
       and (Lack=0 or e.HandStockAmount>0)
       and (instr(';'||d.WeekName||';',';'||to_char(sysdate,'day')||';')>0
           or instr(';'||d.WeekName||';',';'||to_char(sysdate+1,'day')||';')>0
           or trunc(sysdate)-nvl(trunc(a.PriceTime),trunc(a.CreateTime))<=2
           or a.goo_id in (select goo_id from BestCommends where Cmd_ID in (select Cmd_ID from BestCommend where trunc(sysdate)=CommendDate))
           or exists(select 1 from Shop where trunc(sysdate)-trunc(FirstSaleTime)<=3 and Sho_ID=Item.Sho_ID)
           or e.HandStockAmount>0
           or GetShopProperty('Prepare',Item.Sho_ID)=1)
       and not exists (Select 1 from NotOrderShopPattern where sho_ID=Item.Sho_ID and Bra_ID=a.Bra_ID and Pattern=a.Pattern)
       and not exists (Select 1 From NotOrderPattern where Bra_ID=a.Bra_ID and Pattern=a.Pattern)
       and not exists (Select 1 From NotOrderGoods where Sho_ID=Item.Sho_ID and goo_ID=a.goo_ID)
       AND NOT EXISTS (SELECT 1 FROM firstorderforcorp WHERE GOO_ID = A.GOO_ID)
       AND A.CATEGORYGROUPNO IN (255,258,260)
       AND A.BRA_ID = '08'
       and 1 = p_a
       ;
  end Loop;
 end Loop;
end;
```

## 1.4门店订货数据生成采购单规则

### 1.4.1订货数据自动生成控制

```
Pkg_If301_Spr
```

1. 门店在订货篮窗口对加入订货篮的数据进行确认订货后，系统每晚九点开始对确认订货数据进行自动推送至采购申请单

2. OEM类型供应商生成的采购申请单状态为工作表（0）
3. ODM类型供应商生成的采购申请单状态为已提交（2）
4. 鞋包分部（08）下配码比不为0的男鞋女鞋，默认为首单；否则为补单
5. 美妆（06）淘品（07）分部有进货明细默认为补单，无进货明细默认为首单
6. 男装、女装、内衣分部默认为补单

## 1.5表结构

### 1.5.1 orderbox

| 字段        | 类型          | 为空 | 默认值    | 翻译                                            |
| ----------- | ------------- | ---- | --------- | ----------------------------------------------- |
| SHO_ID      | VARCHAR2(4)   |      |           |                                                 |
| GOO_ID      | VARCHAR2(13)  |      |           |                                                 |
| ADVICEPRICE | NUMBER(18,4)  | Y    |           |                                                 |
| AMOUNT      | NUMBER(18,4)  |      |           |                                                 |
| CREATETIME  | DATE          |      | (SYSDATE) |                                                 |
| ISHAND      | NUMBER(1)     | 0    |           |                                                 |
| OPERATORID  | VARCHAR2(6)   |      |           |                                                 |
| SAFEAMOUNT  | NUMBER(18,4)  | Y    | 0         |                                                 |
| MEMO        | VARCHAR2(100) | Y    |           |                                                 |
| RATIOID     | NUMBER        | Y    | null      | 所用配码比,关联sizeratio4shoes,2017-03-20 ZQ362 |

### 1.5.2 orderboxlist

| 字段       | 类型          | 为空 | 默认值 | 翻译 |
| ---------- | ------------- | ---- | ------ | ---- |
| SHO_ID     | VARCHAR2(4)   |      |        |      |
| GOO_ID     | VARCHAR2(13)  |      |        |      |
| BARCODE    | VARCHAR2(13)  |      |        |      |
| AMOUNT     | NUMBER(18,4)  | Y    |        |      |
| SAFEAMOUNT | NUMBER(18,4)  | Y    | 0      |      |
| ISORDER    | NUMBER(1)     | Y    | -1     |      |
| MEMO       | VARCHAR2(100) | Y    |        |      |
| DIFF_ID1   | VARCHAR2(10)  | Y    |        |      |
| DIFF_ID2   | VARCHAR2(10)  | Y    |        |      |



