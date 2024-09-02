# 04-优惠券场景下的风控规则和阙值确定

## 1 羊毛党主要判断依据（组合判断）

行为目的明确：行为序列通常是：登录-领券-下单！

下单商品客单价低：订单商品集中在单价为5~20元以下的商品

下单商品种类单一：订单商品种类超过一半为折扣商品

年/月商品订单量少：只对羊毛商品感兴趣

下单时间通常在深夜

年/月登录次数少：远远小于正常用户的平均值

注册时间短：常用IP和设备更换频繁

IP和设备出现重叠。

## 2 用户属性

IP属地、设备IME和手机号的风控事件：

用户是否在常用IP下单及使用不同IP下单的频率超过阈值。

用户是否在常用设备下单及使用不同设备下单的频率超过阈值

用户设备或IP的更换频率超过阈值

用户手机号的更换频率超过阈值

## 3 订单的风控事件

订单中指定价格的商品数量是否超过阈值。

订单中指定类目的商品价格是否超过阈值

下订单的设备(IMEI)是否出现重复并超过阈值

下单时间集中在阈值时间段

## 4 用户行为风控事件

某IP范围在最近1小时内注册账号数超过阈值

某账号在最近3分钟内登陆次数超过阈值

某账号在最近1小时内购买优惠商品超过阈值

某账号在最近7天内是否修改过手机号码

## 5 领取优惠券风控事件

- 某账号领券的行为路径是否"登录-领券-下单"
- 某账号领券数量是否超过阈值
- 某账号在最近3分钟内领券超过阈值

## 6 风控场景



| 值(EN)       | 值(CN)           |
| ------------ | ---------------- |
| SCEN_ACCOUNT | 注册与登录       |
| SCEN_SALE    | 购物车与下单     |
| SCEN_COUPON  | 营销活动与优惠券 |

## 7 规则名称




| 类型名称 | 举例                            |
| -------- | ------------------------------- |
| 静态规则 | 下单地址是否常用地址            |
| 统计规则 | 登录次数是否大于10次            |
| 关联规则 | 修改绑定手机号后是否马上下单    |
| 行为规则 | 行为序列通常是 "登录-领券-下单" |

## 8 规则字段和值



| 字段             | 值                            |
| ---------------- | ----------------------------- |
| IP归属地（省份） | 1.广东 2.江苏 3.黑龙江 4.四川 |
| IP行为           | 1.撞库2.漏洞扫描3.注入        |
| 设备指纹         | IMEI(手机序列号)              |
| 用户指纹         | 1.用户ID2.绑定手机号          |

## 9 风控规则和阈值



#### 规则类型

| 类型名称 | 举例                                |
| -------- | ----------------------------------- |
| 静态规则 | 如：下单地址是否常用地址            |
| 统计规则 | 如：登录次数是否大于10次            |
| 关联规则 | 如：修改绑定手机号后是否马上下单    |
| 行为规则 | 如：行为序列通常是 "登录-领券-下单" |

| 风控场景         | 规则类型 | 风控规则                                                  |
| ---------------- | -------- | --------------------------------------------------------- |
| 注册与登录       | 统计规则 | IP在最近1小时内注册账号数超过5次                          |
| 注册与登录       | 统计规则 | 账号在最近3分钟内登陆次数超过5次                          |
| 注册与登录       | 行为规则 | 用户在最近3天内修改过手机号码                             |
| 购物车与下单     | 静态规则 | 用户没有使用常用IP下单                                    |
| 购物车与下单     | 静态规则 | 用户没有使用常用设备下单                                  |
| 营销活动与优惠券 | 统计规则 | 券领取的数量超过100张                                     |
| 营销活动与优惠券 | 统计规则 | 使用券下单的商品库存超过100件                             |
| 营销活动与优惠券 | 统计规则 | 券领取的用户在最近3个月的登录次数小于2次                  |
| 营销活动与优惠券 | 静态规则 | 券领取的用户在最近1个月的IP更换频率超过3次                |
| 营销活动与优惠券 | 静态规则 | 券领取的用户在最近1个月的设备指纹更换频率超过3次          |
| 营销活动与优惠券 | 行为规则 | 使用券下单的用户的行为路径是“登录-领券-下单”              |
| 营销活动与优惠券 | 关联规则 | 使用券下单的用户的收货手机号存在多个订单相同数量超过3个   |
| 营销活动与优惠券 | 关联规则 | 使用券下单的用户的IP属地是否存在多个订单相同的数量超过5个 |

| 风控场景   | 事件类型              | 规则类型 | 风控规则                                                     | 羊毛党特征        |
| ---------- | --------------------- | -------- | ------------------------------------------------------------ | ----------------- |
| 注册与登录 | LOGIN                 | 统计规则 | 同一地区的IP在最近1小时内注册账号数超过50次                  | 群控设备/浅层作弊 |
| 注册与登录 | LOGIN                 | 统计规则 | 账号在最近1小时内登录次数超过5次                             | 群控设备/浅层作弊 |
| 账号       | ACCOUNT               | 行为规则 | 用户在最近3天内修改过用户信息且其他行为事件数量少于3         | 真人作弊          |
| 账号       | LOGIN                 | 统计规则 | 最近10天无登录且促销活动有登录                               | 真人作弊          |
| 购物       | SHOPPING              | 静态规则 | 用户没有使用常用IP下单                                       | 真人作弊          |
| 优惠券     | COUPON                | 统计规则 | 账号券领取的数量超过50张                                     | 群控设备/浅层作弊 |
| 优惠券     | LOGIN,SHOPPING        | 统计规则 | 券领取的用户在最近1个月的IP更换次数超过5次                   | 群控设备/浅层作弊 |
| 优惠券     |                       | 行为规则 | 使用券下单的用户的行为路径是"登录-领券-下单"                 | 真人作弊          |
| 优惠券     | LOGIN,SHOPPING,COUPON | 统计规则 | 购买商品类目数量小于3或者80%商品价格低于20元且90%商品复购次数低于2次 | 深层作弊          |
| 优惠券     | LOGIN,SHOPPING,COUPON | 统计规则 | 商品交易时间间隔低于30分钟                                   | 真人/团体作弊     |
| 优惠券     | LOGIN,SHOPPING,COUPON | 统计规则 | 优惠券领取账户是注册时间少于30分钟                           | 群控设备/浅层作弊 |
| 优惠券     | LOGIN,SHOPPING,COUPON | 行为规则 | 账号等级为L2以上的用户各行为之间时间间隔平均少于3分钟或者浏览行为停留平均时间少于3分钟 | 深层作弊          |