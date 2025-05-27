# I. Introduction
Using SQL likes handle **datetime, aggregate, and window functions** to display revenue, retention rate of customer, index related to stocks (trend of stock, stock/sales).
# II. Requirements
1.	Google Cloud Platform account
2.	Project on Google Cloud Platform
3.	Google BigQuery API enabled
4.	SQL query editor or IDE
# III. Dataset Access
1.	Sign in to your Google Cloud Platform account and create a new project.
2.	Go to the BigQuery console and choose your newly created project.
3.	In the navigation menu, click on 'Add Data' and select 'Search a project.'
# IV. Exploring the Dataset
**Query 1: Calc Quantity of items, Sales value & Order quantity by each Subcategory in L12M**

**_SQL code_**
```SQL
select
      format_date("%b %Y",t1.ModifiedDate) as period
      ,t3.Name as name
      ,sum(t1.OrderQty) as qty_item
      ,sum(t1.LineTotal) as total_sales
      ,count(distinct t1.SalesOrderID) as order_cnt
from `adventureworks2019.Sales.SalesOrderDetail` as t1
left join `adventureworks2019.Production.Product` as t2 
                                          on t1.productid = t2.productid
left join `adventureworks2019.Production.ProductSubcategory` as t3
                                           on cast(t2.ProductSubcategoryID as int) = t3.ProductSubcategoryID
where date(t1.ModifiedDate) >= 
              (select date_sub(max(date(ModifiedDate)) , interval 12 month)
                 from `adventureworks2019.Sales.SalesOrderDetail`)
group by period, name
order by period desc,name;
```
**_Query result_**

![image](https://github.com/user-attachments/assets/9277eeb1-3d88-4a5c-8366-e2e15635b767)

**Query 2: Ranking Top 3 TeritoryID with biggest Order quantity of every year. If there's TerritoryID with same quantity in a year, do not skip the rank number.**

**_SQL code_**

```SQL
with raw_data as (
  select 
      extract(year from t1.ModifiedDate) as period
      ,t3.Name as name
      ,sum(t1.OrderQty) as qty_item
  from `adventureworks2019.Sales.SalesOrderDetail` as t1
  left join `adventureworks2019.Sales.Product` as t2 
                                              on t1.productid = t2.productid
  left join `adventureworks2019.Production.ProductSubcategory` as t3 
                                              on t2.ProductCategoryID = t3.ProductCategoryID
  group by  period, name
  order by period desc,name
)
select
    name
    ,qty_item
    ,lead(qty_item) over(partition by name order by period desc) as prv_qty
    ,round((qty_item 
        - lead(qty_item) over(partition by name order by period desc))
          /lead(qty_item) over(partition by name order by period desc),2) as qty_diff
from raw_data
order by qty_diff desc
limit 3;
```


**_Query result_**

![image](https://github.com/user-attachments/assets/f8c93705-0647-49be-b40b-882b78584b17)

**Query 3: Calc Total Discount Cost belongs to Seasonal Discount for each SubCategory.**

**_SQL code_**

```SQL
with raw_data as(
    select 
          extract(year from t1.ModifiedDate) as year
          ,TerritoryID
          ,sum(OrderQty) as order_cnt
    from `adventureworks2019.Sales.SalesOrderDetail` as t1
    join `adventureworks2019.Sales.SalesOrderHeader` as t2
      using (SalesOrderID)
    group by year, TerritoryID
    order by order_cnt desc
),
  raw_data_2 as (
    select 
          *
          ,dense_rank() 
              over(partition by year order by order_cnt desc) as rk
    from raw_data
    order by year desc
  )
select *
from raw_data_2
where rk <= 3;
```

**_Query result_**

![image](https://github.com/user-attachments/assets/65cfa049-31ee-4a25-b76b-2907ccdc2046)


**Query 4: Retention rate of Customer in 2014 with status of Successfully Shipped (Cohort Analysis)**

**_SQL code_**

```SQL
with raw_data as (
  select
      extract(year from t1.ModifiedDate) as year
      ,t3.Name as name
      ,t1.UnitPrice as Price
      ,t1.OrderQty AS Item_qty
      ,t5.DiscountPct as discount
      ,t5.type as type
      ,(t1.UnitPrice*t1.OrderQty*t5.DiscountPct) as discount_cost
FROM `adventureworks2019.Sales.SalesOrderDetail` as t1
left join `adventureworks2019.Production.Product` as t2 on t1.productid = t2.productid
left join `adventureworks2019.Production.ProductSubcategory` as t3 on cast(t2.ProductSubcategoryID as int) = t3.ProductSubcategoryID
left join `adventureworks2019.Sales.SpecialOfferProduct` as t4 on t1.productid = t4.productid
left join `adventureworks2019.Sales.SpecialOffer` as t5 on t4.SpecialOfferID = t5.SpecialOfferID
where t5.type like '%Seasonal Discount%'
group by year, name, price, discount,type,Item_qty
)
select
      distinct year
      ,name
      ,sum(discount_cost) as total
from raw_data
group by year, name;
```

**_Query result_**

![image](https://github.com/user-attachments/assets/aae31f4a-1bae-4e44-805e-628cb9eb6c22)

**Query 5: Calc Ratio of Stock / Sales in 2011 by product name, by month. Order results by month desc, ratio desc. Round Ratio to 1 decimal.**

**_SQL code_**

```SQL
with infor_customer as (
    select
        extract(month from ModifiedDate) as month_order
        ,extract (year from ModifiedDate) as year
        ,CustomerID
    from `adventureworks2019.Sales.SalesOrderHeader` 
    where Status = 5
        and extract (year from ModifiedDate) = 2014
    order by month_order, customerID
),
raw_data_1 as (
    select *
        ,row_number() over(partition by customerID order by month_order) as rank
    from infor_customer
),
first_order as (
    select 
        distinct month_order as month_join
        ,customerID
    from raw_data_1
    where rank =1
),
raw_data_2 as (
    select 
        distinct month_order
        ,t1.CustomerID
        ,month_join
    from infor_customer as t1
    join first_order as t2 using (customerID)
),
raw_data_3 as(
    select
        *
        ,concat('M',(month_order - month_join)) as month_diff
    from raw_data_2
    order by customerID, month_order 
)
select
    distinct month_join
    ,month_diff
    ,count(customerID) as customer_cnt
from raw_data_3
group by month_join, month_diff
order by month_join;
```

**_Query result_**

![image](https://github.com/user-attachments/assets/ecc2e020-f708-4277-a69b-509cd1ea6cb6)

**Query 6: No of order and value at Pending status in 2014**

**_SQL code_**

```SQL
with raw_data_1 as(
  select
        extract(month from t1.ModifiedDate) as month
        ,extract(year from t1.ModifiedDate) as year
        ,t2.name as name
        ,sum(t1.StockedQty) as stock_qty
  from `adventureworks2019.Production.WorkOrder` as t1
  left join `adventureworks2019.Production.Product` as t2 using(productID)
  where extract(year from t1.ModifiedDate) = 2011
  group by month, year, name
  order by month desc, name
),
raw_data_2 as (
  select
      month
      ,year 
      ,name
      ,stock_qty
      ,lead(stock_qty,1) over(partition by name order by month desc) as stock_prv
  from raw_data_1
)
select 
      *
      ,round(100*((stock_qty-stock_prv)/stock_prv),1) as diff
from raw_data_2
order by name;
```

**_Query result_**

![image](https://github.com/user-attachments/assets/b57f903b-7776-4359-b5f9-69b914e3e2ed)

**Query 7: Calc Ratio of Stock / Sales in 2011 by product name, by month. Order results by month desc, ratio desc. Round Ratio to 1 decimal.**

**_SQL code_**

```SQL
with sales_data as (
   select
      extract(month from t1.ModifiedDate) as month
      ,extract(year from t1.ModifiedDate) as year
      ,t1.productid as productid
      ,t3.Name as name
      ,sum(t1.OrderQty) as sales
   from `adventureworks2019.Sales.SalesOrderDetail` as t1
   left join `adventureworks2019.Production.Product` as t2 
                  on t1.productid = t2.productid
   left join `adventureworks2019.Production.ProductSubcategory` as t3
                  on cast(t2.ProductSubcategoryID as int) = t3.ProductSubcategoryID
   where extract(year from t1.ModifiedDate) = 2011
   group by month, year, productid, name
   order by month desc,name
),
stock_data as (
   select
        extract(month from t1.ModifiedDate) as month
        ,extract(year from t1.ModifiedDate) as year
        ,t1.productid as productid
        ,t2.name as name
        ,sum(t1.StockedQty) as stock_qty
  from `adventureworks2019.Production.WorkOrder` as t1
  left join `adventureworks2019.Production.Product` as t2 using(productID)
  where extract(year from t1.ModifiedDate) = 2011
  group by month, year, productid, name
  order by month desc, name     
)
select 
      s1.month as month
      ,s1.year as year
      ,s1.productid as productid
      ,s1.name as name
      ,s1.stock_qty as stock_qty
      ,s2.sales as sales_qty
      ,round((s1.stock_qty/s2.sales),1) as ratio
from stock_data as s1
left join sales_data as s2 using(productID)
order by month desc, ratio desc;
```

**_Query result_**

![image](https://github.com/user-attachments/assets/7ae00833-22e6-443b-93a9-9be3f22e84af)

**Query 8: No of order and value at Pending status in 2014**

**_SQL code_**

```SQL
select
      extract(year from ModifiedDate) as year
      ,status
      ,count(distinct PurchaseOrderID) as order_cnt
      ,sum(TotalDue) as value
from `adventureworks2019.Purchasing.PurchaseOrderHeader`
where extract(year from ModifiedDate) = 2014
      and status = 1
group by year, status;
```

**_Query result_**

![image](https://github.com/user-attachments/assets/ecc6d428-5eee-48ca-ad8a-83db87b62e7c)




























