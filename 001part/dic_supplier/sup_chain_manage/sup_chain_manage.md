# 表结构

### dic_supplier

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