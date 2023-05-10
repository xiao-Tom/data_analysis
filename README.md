# test
# 数据理解
数据集共包含有828934条数据，109个字段。数据为用户注册后7天内的用户行为汇总数据。
本文主要对以下10个字段进行分析。

| 序号 | 字段 | 字段解释 |
| --- | --- | --- |
| 1 | user_id | 玩家唯一ID |
| 2 | pay_price | 付费金额 |
| 3 | register_time | 玩家注册时间 |
| 4 | avg_online_minutes |  在线时长 |
| 5 | pvp_battle_count | 玩家与玩家之间的对战次数 |
| 6 | pvp_lanch_count | 主动发起pvp次数 |
| 7 | pve_win_count | pvp胜利次数 |
| 8 | pve_battle_count | 玩家与电脑之间的对战 |
| 9 | pve_lanch_count | 主动发起pve次数 |
| 10 | pve_win_count | pve胜利次数 |


主要从以下四个方面进行分析

- 新增玩家分析：从玩家数量、付费玩家占比、每日新增玩家展开分析
- 玩家活跃度分析：从不同用户在线时长、分布特征展开分析
- 玩家付费情况分析：从PUR，ARPPU，ARPU等关键指标得出的情况来展开分析
- 玩家游戏习惯分析：从不同玩家的pvp、pve这两个指标得出的情况来展开分析
# 数据分析
## 1 新增玩家分析
### 1.1 新增玩家数量
```sql
select count(distinct user_id) as 新增玩家数量
from tap_fun;
```
![9bb1317e864eac2c422b3d8c22f43cc.png](https://cdn.nlark.com/yuque/0/2020/png/1589762/1596807826046-af37701c-5566-4e47-b201-4148d0b7a2cf.png#align=left&display=inline&height=51&originHeight=51&originWidth=118&size=1266&status=done&style=none&width=119)
### 1.2 新增付费玩家数量
```sql
select count(distinct user_id) as 新增付费玩家数量
from tap_fun
where pay_price>0;
```
![ef568ee85a933cf8d4e9ee26b8847e7.png](https://cdn.nlark.com/yuque/0/2020/png/1589762/1596814272691-c400badf-0b9e-4567-bf1e-2f44a5e7436e.png#align=left&display=inline&height=50&originHeight=50&originWidth=141&size=1400&status=done&style=none&width=141)
### 1.3 付费玩家占比
```sql
select 新增付费玩家数量/新增玩家数量 as 付费玩家占比
from 
(select count(distinct user_id) as 新增付费玩家数量
 from tap_fun
 where pay_price>0) as t1,
 (select count(distinct user_id) as 新增玩家数量
  from tap_fun) as t2;
```
![a5186a6b56873333e62308465423b92.png](https://cdn.nlark.com/yuque/0/2020/png/1589762/1596814488858-fce1e6e1-fee6-4a30-aa51-f26ccdc289ab.png#align=left&display=inline&height=51&originHeight=51&originWidth=118&size=1122&status=done&style=none&width=118)
新增玩家有828934人，其中付费玩家有19549人，付费人数占注册总人数的2.36%
### 1.4 每日新增用户数量
```sql
select date(register_time) as 日期,count(distinct user_id) as 当日新增玩家
from tap_fun
group by 日期;
```
![5b11b0b355a35b44a38b9c705d8f433.png](https://cdn.nlark.com/yuque/0/2020/png/1589762/1596851704555-b5019b08-7374-477c-97ff-e823b836d5e1.png#align=left&display=inline&height=272&originHeight=272&originWidth=216&size=7731&status=done&style=none&width=216)
### 1.5 每日新增付费用户数量
```sql
select date(register_time) as 日期,count(distinct user_id) as 当日新增付费玩家
from tap_fun
where pay_price>0
group by 日期;
```
![5a16e5b8bb320ca18847c75d9789f43.png](https://cdn.nlark.com/yuque/0/2020/png/1589762/1596851476974-11ed1608-a27e-4679-9dde-0771b948711d.png#align=left&display=inline&height=272&originHeight=272&originWidth=239&size=7546&status=done&style=none&width=239)
将数据可视化：
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1589762/1596852724851-91ed5663-5121-4813-bba6-a33971afd716.png#align=left&display=inline&height=306&originHeight=306&originWidth=510&size=24856&status=done&style=none&width=510)

观察上图可知：
每日新增玩家在3月10日有一次大高峰增长，在3月13日有一次小高峰增长，这正是因为这两次时间点举办了游戏活动，但是活动一过，后续新玩家注册量并没有显著提升，可见这两次活动并没有给游戏的人气带来实质性的帮助。
活动还需要力度并且保持一定的时间维度，给玩家充分了解游戏的时间，才能持续提高游戏的热度。

## 2 玩家活跃度分析
### 2.1 全部玩家平均在线时长
```sql
select avg(avg_online_minutes) as 全部玩家平均在线时长
from tap_fun;
```
![35ffdaeb487c020518ee6c4d19fd396.png](https://cdn.nlark.com/yuque/0/2020/png/1589762/1596852928934-e3098df3-ed16-4f44-b400-1202931dd7d1.png#align=left&display=inline&height=51&originHeight=51&originWidth=166&size=1754&status=done&style=none&width=166)
### 2.2 付费玩家平均在线时长
```sql
select avg(avg_online_minutes) as 付费玩家平均在线时长
from tap_fun
where pay_price>0;
```
![486b67e1029ad65345e92e4fa3cd493.png](https://cdn.nlark.com/yuque/0/2020/png/1589762/1596853039752-05bb5055-acfc-41a8-a804-ff605a85857d.png#align=left&display=inline&height=52&originHeight=52&originWidth=166&size=1796&status=done&style=none&width=166)
明显可以看出，付费玩家的平均在线时长要远远大于全体玩家的平均值，活跃度比他们高的多。
### 2.3 在线时长的分布特点
使用箱线图来分析总体特征，先求出最大值、最小值、中位数和上下四分位数
```sql
select round(count(distinct user_id)/4) as 下四分位数,
round(count(distinct user_id)/2) as 中位数,
round(count(distinct user_id)/4*3) as 上四分位数
from tap_fun;
```
![11bbc67cc652a94dc60fe83590f88f4.png](https://cdn.nlark.com/yuque/0/2020/png/1589762/1596853291341-3c2c2080-1d29-45fe-a642-8e364b8d88ac.png#align=left&display=inline&height=53&originHeight=53&originWidth=270&size=2249&status=done&style=none&width=270)
```sql
#全部玩家在线时长箱线图关键值
SELECT
	min( avg_online_minutes ) AS 最小值,
	( SELECT avg_online_minutes FROM tap_fun ORDER BY avg_online_minutes LIMIT 207233, 1 ) AS 下四分位数,
	( SELECT avg_online_minutes FROM tap_fun ORDER BY avg_online_minutes LIMIT 414466, 1 ) AS 中位数,
	( SELECT avg_online_minutes FROM tap_fun ORDER BY avg_online_minutes LIMIT 621700, 1 ) AS 上四分位数,
	max( avg_online_minutes ) AS 最大值 
FROM
	tap_fun;
```
![11a585476ad092646bb8869e3337a44.png](https://cdn.nlark.com/yuque/0/2020/png/1589762/1596860581584-14f716cd-7052-4d50-9f0d-b3f5661f70cb.png#align=left&display=inline&height=51&originHeight=51&originWidth=435&size=2453&status=done&style=none&width=435)
```sql
#付费玩家人数的下四分位数、中位数、上四分位数
select round(count(distinct user_id)/4) as 下四分位数,
round(count(distinct user_id)/2) as 中位数,
round(count(distinct user_id)/4*3) as 上四分位数
from tap_fun
where pay_price>0;
```
![6cb1c5d8dc8242dd86cd3144fcb244c.png](https://cdn.nlark.com/yuque/0/2020/png/1589762/1596856046608-9a7fcb21-f908-490e-8714-3a8882e083e4.png#align=left&display=inline&height=52&originHeight=52&originWidth=271&size=2151&status=done&style=none&width=271)
```sql
#付费玩家的在线时长箱线图
SELECT
	min( avg_online_minutes ) AS 最小值,
	( SELECT avg_online_minutes FROM tap_fun WHERE pay_price > 0 ORDER BY avg_online_minutes LIMIT 4887, 1 ) AS 下四分位数,
	( SELECT avg_online_minutes FROM tap_fun WHERE pay_price > 0 ORDER BY avg_online_minutes LIMIT 9775, 1 ) AS 中位数,
	( SELECT avg_online_minutes FROM tap_fun WHERE pay_price > 0 ORDER BY avg_online_minutes LIMIT 14662, 1 ) AS 上四分位数,
	max( avg_online_minutes ) AS 最大值 
FROM
	tap_fun 
WHERE
	pay_price > 0;
```
![c5e3c10167067f612f502e4beaf939d.png](https://cdn.nlark.com/yuque/0/2020/png/1589762/1596860745978-d59bf13f-65a6-4bdb-bd72-410021517186.png#align=left&display=inline&height=52&originHeight=52&originWidth=434&size=2540&status=done&style=none&width=434)
全部玩家与付费玩家在线时长箱线图：
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1589762/1596861032635-cb77ca4c-c101-4299-a0df-fd2ce691e68c.png#align=left&display=inline&height=302&originHeight=302&originWidth=504&size=13909&status=done&style=none&width=504)

根据以上数据和图表可以得到如下结论：
全部玩家在线时长箱线图线下压缩的很严重，全部玩家在线时长下四分位数为0，75%的玩家平均在线只有大约1分钟，可见玩家流失的情况还是比较严重的。
付费用户的在线时长箱线图中，下四分位数约为31，中位数85，上四分位数191，也就是说，付费用户中，75%以上的用户在线时长都超过了30分钟。
## 3 玩家付费情况分析
**关键指标：**
AU（Active User）：活跃用户，这里定为游戏时长达15分钟的为活跃用户。
PU（Paying User）：付费用户
APA（Active Payment Account）：活跃付费用户数
ARPU（Average Revenue Per User）：平均每个活跃用户的收入，即可通过总收入/AU计算得出。
ARPPU（Average Revenue Per Paying User）：平均每个活跃付费用户收入，可通过总收入/APA计算得出。
PUR（Pay User Rate）：付费比率，可通过APA/AU计算得出
### 3.1 每个活跃玩家的收入
```sql
# ARPU = 总收入/AU
select count(distinct user_id) as AU,
(select sum(pay_price) from tap_fun) as 总收入,
(select sum(pay_price) from tap_fun)/count(distinct user_id) as ARPU
from tap_fun where avg_online_minutes>=15;
```
### ![2b3a49db08285ceea9df91ebd7f08b2.png](https://cdn.nlark.com/yuque/0/2020/png/1589762/1596865024686-d4827e7c-242b-416a-bf24-f2b57aa52de3.png#align=left&display=inline&height=53&originHeight=53&originWidth=379&size=2358&status=done&style=none&width=379)
### 3.2 每个活跃付费玩家的收入
```sql
# ARPPU = 总收入/APA
select count(distinct user_id) as APA,
(select sum(pay_price) from tap_fun) as 总收入,
(select sum(pay_price) from tap_fun)/count(distinct user_id) as ARPPU
from tap_fun
where avg_online_minutes>=15 and pay_price>0;
```
![57c7b72c08086b7b7573da8110144a0.png](https://cdn.nlark.com/yuque/0/2020/png/1589762/1596865113165-93e07384-1777-4a5b-aed5-a83c0b921a26.png#align=left&display=inline&height=55&originHeight=55&originWidth=379&size=2430&status=done&style=none&width=379)
### 3.3 付费率
```sql
# PUR = APA/AU
SELECT
	count( DISTINCT user_id ) AS APA,
	( SELECT count( DISTINCT user_id ) FROM tap_fun WHERE avg_online_minutes >= 15 ) AS AU,
	count( DISTINCT user_id )/(
    SELECT count( DISTINCT user_id ) 
	FROM tap_fun 
	WHERE avg_online_minutes > 0 ) AS PUR 
FROM
	tap_fun 
WHERE
	avg_online_minutes >= 15 
	AND pay_price > 0;
```
![4e2f0a94b02959c8e6b702a2c328bd6.png](https://cdn.nlark.com/yuque/0/2020/png/1589762/1596865808225-90c065b2-70d4-4f76-b3ea-d6359b961fbb.png#align=left&display=inline&height=53&originHeight=53&originWidth=222&size=1495&status=done&style=none&width=222)
对数据进行可视化：
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1589762/1596866110743-8dbee67d-b7eb-4cfe-b309-7e88adc4adec.png#align=left&display=inline&height=256&originHeight=288&originWidth=481&size=6997&status=done&style=none&width=428)
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1589762/1596866331801-cbfccac6-1e21-4b8b-9921-de1a41339d5a.png#align=left&display=inline&height=250&originHeight=280&originWidth=479&size=7195&status=done&style=none&width=428)
该游戏人均付费率ARPU很低，所明游戏的收入表现较差，但是对比ARPU，平均每个付费用户收入ARPPU很高，是ARPU的6倍有多，那就说明收费玩家的付费能力很强，针对这一点，我们可以做一些付费功能调整和优化，甚至专属大R玩家，让大R玩的更开心。
付费率只有可怜的2.89%，其实只要简单地开展一个首充活动，比如充1元可获得价值60元的大礼包，就能够很好地提高游戏的付费率了。付费率高，一样可以得到渠道青睐，获得更多推荐展示机会（行业俗称为“吸量”），间接提高游戏的热度。
注：大R，可理解为家里有矿的人，游戏的充值金额很大。每个游戏都有等级划分的制度，共有以下五种：废户、普通用户、VIP用户、SVIP用户、大R用户
## 4 玩家游戏习惯分析
**关键指标**
PVP（Player VS Player）：指的是玩家与玩家之间的对战，PVP，说通俗一点就是人对人。玩家与对立阵营，派别之间的玩家发生的战斗。通过完成击杀其他玩家获得荣誉，声望，装备道具等。
PVE(Player VS Enviroment)：指的是玩家与电脑之间的对战，也就是打怪，打副本之类的。
### 4.1 AU玩家的PVP情况
```sql
select AVG(pvp_battle_count) as 平均PVP次数,
sum(pvp_lanch_count)/sum(pvp_battle_count) as 主动发起PVP的概率,
sum(pvp_win_count)/sum(pvp_battle_count) as PVP胜利的概率
from tap_fun 
where avg_online_minutes>=15;
```
![2973a5c3cd57a7d3de10386d62ad181.png](https://cdn.nlark.com/yuque/0/2020/png/1589762/1596869176563-9774038e-bcf1-419a-89b3-3ca34cdf5fad.png#align=left&display=inline&height=53&originHeight=53&originWidth=468&size=3686&status=done&style=none&width=468)
### 4.2 APA玩家的PVP情况
```sql
select avg(pvp_battle_count) as 平均PVP次数,
sum(pvp_lanch_count)/sum(pvp_battle_count) as 主动发起PVP的概率,
sum(pvp_win_count)/sum(pvp_battle_count) as PVP胜利的概率
from tap_fun where avg_online_minutes>=15 and pay_price>0;
```
![7c34c482fde94cbdee0fe3338744904.png](https://cdn.nlark.com/yuque/0/2020/png/1589762/1596866780969-374c2a44-9b19-4a6e-b494-397691f125de.png#align=left&display=inline&height=55&originHeight=55&originWidth=467&size=3732&status=done&style=none&width=467)
### 4.3 AU玩家的PVE情况
```sql
select AVG(pve_battle_count) as 平均PVE次数,
sum(pve_lanch_count)/sum(pve_battle_count) as 主动发起PVE的概率,
sum(pve_win_count)/sum(pve_battle_count) as PVE胜利的概率
from tap_fun where avg_online_minutes>=15;
```
![e43b3bf781d18d9f7a9f0850b664bdc.png](https://cdn.nlark.com/yuque/0/2020/png/1589762/1596866901293-bfcbac4e-50ea-4e31-8e9d-75a0afe9557b.png#align=left&display=inline&height=48&originHeight=48&originWidth=461&size=3634&status=done&style=none&width=461)
### 4.4 APA玩家的PVE情况
```sql
select AVG(pve_battle_count) as 平均PVE次数,
sum(pve_lanch_count)/sum(pve_battle_count) as 主动发起PVE的概率,
sum(pve_win_count)/sum(pve_battle_count) as PVE胜利的概率
from tap_fun where avg_online_minutes>=15 and pay_price>0;
```
![85308c3f4ffabe41eeeeeb9c3ad52ca.png](https://cdn.nlark.com/yuque/0/2020/png/1589762/1596866992724-2509450a-f105-4d55-9dea-1dc732d7ac1c.png#align=left&display=inline&height=51&originHeight=51&originWidth=463&size=3797&status=done&style=none&width=463)
将数据可视化：
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1589762/1596868952288-3828ec9b-4bcc-473b-ac99-cad937dfe1b3.png#align=left&display=inline&height=288&originHeight=288&originWidth=481&size=10927&status=done&style=none&width=481)

从上图可以看出，APA玩家的平均PVE和PVP次数都要高于AU玩家两倍左右，显然APA玩家更愿意花费更多时间在这个游戏上。
![image.png](https://cdn.nlark.com/yuque/0/2020/png/1589762/1596868977416-2a77c13d-4c5d-466c-8db1-2b90d70fc852.png#align=left&display=inline&height=302&originHeight=302&originWidth=492&size=13046&status=done&style=none&width=492)

在PVE活动中，APA玩家主动发起进攻的概率和胜利的概率与AU玩家基本持平，其中主动发起PVE的概率非常高，可见游戏玩家还是比较熟悉游戏规则，基本上能主动刷副本打怪获取资源或者等级的提升。另外游戏的PVE难度也不高，玩家的PVE胜率高达90%，可见游戏体验较为友好。
在PVP活动中，APA玩家主动发起进攻的概率和胜利的概率要明显高于AU玩家，在享受游戏对战乐趣的过程中，往往更能够获取胜利。
# 结论

---

#### 1 新增用户分析

- 新增玩家有828934人，其中付费玩家有19549人，付费人数占注册总人数的2.36%。
- 每日新增玩家在3月10日有一次大高峰增长，在3月13日有一次小高峰增长。推断可能是举办了游戏活动，但是游戏活动一过，后续新用户注册量并没用显著提升，可见这两次活动并没有给游戏的人气带来实质性的帮助。
- 在这两次活动举办期间，每日新增付费用户并没有提升，反而趋于下降，可见这两次活动主要是为了提高游戏的热度。
- 建议加大活动力度，并保持一定的时间维度，给玩家充分了解游戏的时间，才能持续提高游戏热度。
#### 2 玩家活跃度分析

- 全部玩家平均在线时长11.74分钟，付费玩家平均在线时长135.87分钟，付费玩家的平均在线时长要远远大于全体玩家的平均值，活跃度比他们大得多。
- 75%的玩家平均在线时长不超过1分钟，可见玩家流失的情况还是比较严重的。
- 付费用户中，75%以上的用户在线时长都超过了30分钟，说明付费用户更加愿意投入时间到该款游戏中。
#### 3 玩家付费情况分析

- 付费率PUP比较低，只有2.70%。其实只要简单的开展一个首充活动，比如充1元可获得价值60元的大礼包，就能很好地提高游戏的付费率了。付费率高，一样可以得到渠道青睐，获得更多推荐展示机会（行业俗称为“吸量”），间接提高游戏的热度。
- 目前较好的手游每日ARPU超过5元；一般的手游ARPU在3~5元之间；ARPU低于3元则说明表现较差。该手游平均每用户收入ARPU很低，说明游戏的收入表现较差，但是平均每付费用户收入ARPPU很高，说明大R的付费能力强，针对这一点，我们可以做一些付费功能的调整和优化，甚至专属大R的客服，让付费用户玩得更开心。
#### 4 付费玩家习惯分析

- APA玩家的平均PVE和PVP次数都要高于AU玩家两倍左右，显然APA玩家更愿意花费更多时间在这个游戏上
- 在PVP活动中，APA玩家主动发起进攻的概率和胜利的概率要明显高于AU玩家，在享受游戏对战乐趣的过程中，往往更能够获取胜利。
- 在PVE活动中，APA玩家主动发起进攻的概率和胜利的概率与AU玩家基本持平，其中主动发起PVE的概率非常高，可见游戏玩家还是比较熟悉游戏规则，基本上能主动刷副本打怪获取资源或者等级的提升。另外游戏的PVE难度也不高，玩家的PVE胜率高达90%，可见游戏体验较为友好。

