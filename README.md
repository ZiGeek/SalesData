# 沃尔玛销售数据SQL

## 简介
这个项目旨在探索沃尔玛销售数据，以了解销售业绩最好的分支机构和产品、不同产品的销售趋势以及客户行为。该项目的目标是研究如何改进和优化销售策略。

## 项目目的

本项目的主要目标是通过分析沃尔玛的销售数据，深入了解不同因素对各分支机构销售的影响。

## 数据概况

该数据集来自[Kaggle沃尔玛销售预测竞赛](https://www.kaggle.com/c/walmart-recruiting-store-sales-forecasting)。该数据集包含来自沃尔玛三个不同分支机构（分别位于曼德勒、仰光和内比都）的销售交易数据。数据包含17列和1000行：

| 列名                      | 描述                                    | 数据类型          |
| :----------------------- | :------------------------------------- | :--------------- |
| invoice_id              | 销售发票编号                           | VARCHAR(30)      |
| branch                  | 销售所在分支机构                        | VARCHAR(5)       |
| city                    | 分支机构所在城市                        | VARCHAR(30)      |
| customer_type           | 客户类型                                | VARCHAR(30)      |
| gender                  | 购买者性别                              | VARCHAR(10)      |
| product_line            | 产品线                                 | VARCHAR(100)     |
| unit_price              | 每个产品的价格                         | DECIMAL(10, 2)   |
| quantity                | 销售数量                               | INT              |
| VAT                     | 购买税额                               | FLOAT(6, 4)      |
| total                   | 购买总成本                             | DECIMAL(10, 2)   |
| date                    | 购买日期                               | DATE             |
| time                    | 购买时间                               | TIMESTAMP        |
| payment_method          | 付款方式                               | DECIMAL(10, 2)   |
| cogs                    | 已售商品成本                           | DECIMAL(10, 2)   |
| gross_margin_percentage | 毛利率百分比                           | FLOAT(11, 9)     |
| gross_income            | 毛利收入                               | DECIMAL(10, 2)   |
| rating                  | 评分                                    | FLOAT(2, 1)      |


### 分析列表

1. 产品分析

> 对数据进行分析，了解不同的产品线，表现最佳的产品线以及需要改进的产品线。

2. 销售分析

> 此分析旨在回答产品的销售趋势问题。此分析的结果可以帮助我们衡量业务应用的每个销售策略的有效性，以及需要进行哪些修改以获得更多的销售额。

3. 客户分析

> 此分析旨在揭示不同的客户细分、购买趋势以及每个客户细分的盈利能力。

## 使用的方法

1. **数据整理：** 这是第一步，用于检查数据，确保检测到**NULL**值和缺失值，并使用数据替换方法来替换缺失或**NULL**值。

> 1. 建立数据库
> 2. 创建表并插入数据。
> 3. 选择包含空值的列。在创建表时，我们为每个字段设置了**NOT NULL**，因此空值已被过滤掉。

2. **特征工程：** 这将帮助我们从现有的列中生成一些新列。

> 1. 添加一个名为`time_of_day`的新列，以提供关于早上、下午和晚上销售情况的见解。这将有助于回答大部分销售发生在一天的哪个时间段的问题。

> 2. 添加一个名为`day_name`的新列，其中包含了交易发生的每个分支机构的所在星期的提取日期（星期一、星期二、星期三、星期四、星期五）。这将有助于回答每个分支机构在一周的哪一天最忙碌的问题。

> 3. 添加一个名为`month_name`的新列，其中包含了交易发生的每个月份的提取日期（一月、二月、三月）。有助于确定一年中哪个月份的销售额和利润最高。

3. **探索性数据分析（EDA）：** 进行探索性数据分析以回答列出的问题和项目的目标。


## Code

```sql


-- 创建数据库
CREATE DATABASE IF NOT EXISTS walmartSales;


-- 创建表格
CREATE TABLE IF NOT EXISTS sales(
	invoice_id VARCHAR(30) NOT NULL PRIMARY KEY,
    branch VARCHAR(5) NOT NULL,
    city VARCHAR(30) NOT NULL,
    customer_type VARCHAR(30) NOT NULL,
    gender VARCHAR(30) NOT NULL,
    product_line VARCHAR(100) NOT NULL,
    unit_price DECIMAL(10,2) NOT NULL,
    quantity INT NOT NULL,
    tax_pct FLOAT(6,4) NOT NULL,
    total DECIMAL(12, 4) NOT NULL,
    date DATETIME NOT NULL,
    time TIME NOT NULL,
    payment VARCHAR(15) NOT NULL,
    cogs DECIMAL(10,2) NOT NULL,
    gross_margin_pct FLOAT(11,9),
    gross_income DECIMAL(12, 4),
    rating FLOAT(2, 1)
);

-- 数据清洗
SELECT
	*
FROM sales;

-- 添加 time_of_day 列
SELECT
	time,
	(CASE
		WHEN `time` BETWEEN "00:00:00" AND "12:00:00" THEN "早上"
        WHEN `time` BETWEEN "12:01:00" AND "16:00:00" THEN "下午"
        ELSE "晚上"
    END) AS time_of_day
FROM sales;

-- 为表格添加 time_of_day 列
ALTER TABLE sales ADD COLUMN time_of_day VARCHAR(20);

-- 要使此代码工作，请关闭更新安全模式
-- 编辑 > 首选项 > SQL 编辑器 > 向下滚动并切换安全模式
-- 重新连接到 MySQL：查询 > 重新连接到服务器
-- 更新 time_of_day 列
UPDATE sales
SET time_of_day = (
	CASE
		WHEN `time` BETWEEN "00:00:00" AND "12:00:00" THEN "早上"
        WHEN `time` BETWEEN "12:01:00" AND "16:00:00" THEN "下午"
        ELSE "晚上"
    END
);

-- 添加 day_name 列
SELECT
	date,
	DAYNAME(date)
FROM sales;

-- 为表格添加 day_name 列
ALTER TABLE sales ADD COLUMN day_name VARCHAR(10);

-- 更新 day_name 列
UPDATE sales
SET day_name = DAYNAME(date);

-- 添加 month_name 列
SELECT
	date,
	MONTHNAME(date)
FROM sales;

-- 为表格添加 month_name 列
ALTER TABLE sales ADD COLUMN month_name VARCHAR(10);

-- 更新 month_name 列
UPDATE sales
SET month_name = MONTHNAME(date);

-- --------------------------------------------------------------------

-- 数据包含多少个不同的城市？
SELECT 
	DISTINCT city
FROM sales;

-- 每个分店位于哪个城市？
SELECT 
	DISTINCT city,
    branch
FROM sales;

-- --------------------------------------------------------------------
-- ---------------------------- 产品查询 ------------------------------
-- --------------------------------------------------------------------

-- 数据包含多少个不同的产品线？
SELECT
	DISTINCT product_line
FROM sales;

-- 最畅销的产品线是什么？
SELECT
	SUM(quantity) as 销售数量,
    product_line as 产品线
FROM sales
GROUP BY product_line
ORDER BY 销售数量 DESC;

-- 每个产品线的总收入是多少？
SELECT
	product_line as 产品线,
	SUM(total) as 总收入
FROM sales
GROUP BY product_line
ORDER BY 总收入 DESC;

-- 每个月的总收入是多少？
SELECT
	month_name AS 月份,
	SUM(total) AS 总收入
FROM sales
GROUP BY month_name 
ORDER BY 总收入 DESC;

-- 哪个月的成本最高？
SELECT
	month_name AS 月份,
	SUM(cogs) AS 成本
FROM sales
GROUP BY month_name 
ORDER BY 成本 DESC;

-- 每个产品线的总增值税是多少？
SELECT
	product_line as 产品线,
	AVG(tax_pct) as 平均增值税率
FROM sales
GROUP BY product_line
ORDER BY 平均增值税率 DESC;

-- 销售收入最高的城市是哪个？
SELECT
	branch as 分店,
	city as 城市,
	SUM(total) AS 总收入
FROM sales
GROUP BY city, branch 
ORDER BY 总收入 DESC;


-- 哪个产品线的增值税最高？
SELECT
	product_line as 产品线,
	AVG(tax_pct) as 平均增值税率
FROM sales
GROUP BY product_line
ORDER BY 平均增值税率 DESC;

-- 获取每个产品线并为每个产品线添加一个列，显示“Good”或“Bad”。
-- 如果销售数量高于平均销售数量，则为“Good”。

-- 计算平均销售数量
SELECT 
	AVG(quantity) AS 平均销售数量
FROM sales;

-- 添加 "remark" 列，根据平均销售数量判断产品线好坏
SELECT
	product_line as 产品线,
	CASE
		WHEN AVG(quantity) > 6 THEN "Good"
        ELSE "Bad"
    END AS 评价
FROM sales
GROUP BY product_line;

-- 哪个分店销售的产品数量高于平均销售数量？
SELECT 
	branch as 分店, 
    SUM(quantity) AS 销售数量
FROM sales
GROUP BY branch
HAVING 销售数量 > (SELECT AVG(quantity) FROM sales);

-- 按性别分组，找出每种性别最常见的产品线
SELECT
	gender as 性别,
    product_line as 产品线,
    COUNT(gender) AS 总数
FROM sales
GROUP BY gender, product_line
ORDER BY 总数 DESC;

-- 计算每个产品线的平均评分
SELECT
	ROUND(AVG(rating), 2) as 平均评分,
    product_line as 产品线
FROM sales
GROUP BY product_line
ORDER BY 平均评分 DESC;

-- --------------------------------------------------------------------
-- --------------------------------------------------------------------

-- --------------------------------------------------------------------
-- -------------------------- 顾客 -------------------------------
-- --------------------------------------------------------------------

-- 数据中有多少种独特的顾客类型？
SELECT
	DISTINCT customer_type as 顾客类型
FROM sales;

-- 数据中有多少种独特的付款方法？
SELECT
	DISTINCT payment as 付款方式
FROM sales;

-- 最常见的顾客类型是什么？
SELECT
	customer_type as 顾客类型,
	count(*) as 数量
FROM sales
GROUP BY customer_type
ORDER BY 数量 DESC;

-- 哪种顾客类型购买最多？
SELECT
	customer_type as 顾客类型,
    COUNT(*) as 购买次数
FROM sales
GROUP BY customer_type;

-- 大多数顾客的性别是什么？
SELECT
	gender as 性别,
	COUNT(*) as 性别数量
FROM sales
GROUP BY gender
ORDER BY 性别数量 DESC;

-- 不同分店的性别分布如何？
SELECT
	gender as 性别,
	COUNT(*) as 性别数量
FROM sales
WHERE branch = "C"
GROUP BY gender
ORDER BY 性别数量 DESC;

-- 哪个时间段顾客评分最高？
SELECT
	time_of_day as 时间段,
	AVG(rating) AS 平均评分
FROM sales
GROUP BY time_of_day
ORDER BY 平均评分 DESC;

-- 不同分店的不同时间段顾客评分如何？
SELECT
	time_of_day as 时间段,
	AVG(rating) AS 平均评分
FROM sales
WHERE branch = "A"
GROUP BY time_of_day
ORDER BY 平均评分 DESC;

-- 一周中哪一天的评分平均值最高？
SELECT
	day_name as 星期几,
	AVG(rating) AS 平均评分
FROM sales
GROUP BY day_name 
ORDER BY 平均评分 DESC;

-- 不同分店的一周中哪一天的销售次数最多？
SELECT 
	day_name as 星期几,
	COUNT(day_name) as 销售次数
FROM sales
WHERE branch = "C"
GROUP BY day_name
ORDER BY 销售次数 DESC;

-- --------------------------------------------------------------------
-- --------------------------------------------------------------------

-- --------------------------------------------------------------------
-- ---------------------------- 销售 ---------------------------------
-- --------------------------------------------------------------------

-- 每个时间段在星期日的销售次数是多少？
SELECT
	time_of_day as 时间段,
	COUNT(*) as 销售次数
FROM sales
WHERE day_name = "星期日"
GROUP BY time_of_day 
ORDER BY 销售次数 DESC;

-- 哪种顾客类型带来最高的销售收入？
SELECT
	customer_type as 顾客类型,
	SUM(total) AS 总销售收入
FROM sales
GROUP BY customer_type
ORDER BY 总销售收入 DESC;

-- 哪个城市的平均增值税率最高？
SELECT
	city as 城市,
    ROUND(AVG(tax_pct), 2) AS 平均增值税率
FROM sales
GROUP BY city 
ORDER BY 平均增值税率 DESC;

-- 哪种顾客类型支付的增值税最多？
SELECT
	customer_type as 顾客类型,
	AVG(tax_pct) AS 总增值税
FROM sales
GROUP BY customer_type
ORDER BY 总增值税;

```
