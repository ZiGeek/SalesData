# SalesData

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






