# SQL_PROJECT_ZEPTO
## DESCRIPTION:
SQL_PROJECT_ZEPTO is a data analytics project that demonstrates how SQL can be used to extract, transform, and analyze real-world business data inspired by Zepto, a hyperlocal grocery delivery startup.

This project utilizes PostgreSQL for data querying and Google Colab for data visualization, leveraging Matplotlib and Seaborn to present insights visually. It aims to mimic real-world reporting scenarios like customer behavior, product trends, and operational metrics.

## TECH STACK:
Database: PostgreSQL

Visualization: Google Colab, Matplotlib, Seaborn

Language: SQL, Python

Libraries: pandas, matplotlib, seaborn

## Analytical Insights
This section highlights the core data analysis performed using SQL for querying and Python (Colab) for visualization.
To analyze customer behavior, sales trends, and product performance based on Zepto-style delivery data.

### 1. Data Overview
Brief summary of your dataset:

-> Tables Column: s_no, category, mrp, discountPercent, availableQuantity, discountSellingPrice, weightInGms, outOfStock, quantity.

-> Total Rows: e.g., orders: 3,732 rows

-> Key Columns: s_no, category, name, mrp, discountedSellingPrice

###  2. Table Creation in PostgreSQL
The raw data (CSV files) were first imported and structured into PostgreSQL using appropriate data types and constraints.

```
create table zepto(
	s_no SERIAL PRIMARY KEY,
	Category VARCHAR(150),
	name VARCHAR(150) NOT NULL,
	mrp NUMERIC(8,2),
	discountPercent NUMERIC(5,2),
	availableQuantity INTEGER,
	discountedSellingPrice NUMERIC(8,2),
	weightInGms INTEGER,
	outOfStock BOOLEAN,
	quantity INTEGER
);
```



### 3. Data Cleaning Process
Before performing analysis, the dataset was cleaned to ensure accuracy and consistency.

#### Checking Null values
```
select * from zepto
where category is null
	or name is null
	or mrp is null
	or discountpercent is null
	or availablequantity is null
	or discountedsellingprice is null
	or weightingms is null
	or outofstock is null
	or quantity is null;
```

#### Check specifically price might be zero or not
```
select name,mrp,discountedsellingprice
from zepto
where mrp = 0 or discountedsellingprice = 0;
```
if present -> Delete the row
```
delete from zepto
where mrp = 0 or discountedsellingprice =0;
```

#### Converting price into Rupees
```
update zepto
set mrp = mrp/100.0,
discountedsellingprice = discountedsellingprice/100.0;
```
BEFORE
<img width="1534" height="204" alt="Screenshot 2025-07-25 095214" src="https://github.com/user-attachments/assets/6fe2cbf2-98eb-46f1-8389-16b62d1ea20a" />
AFTER
<img width="1580" height="206" alt="Screenshot 2025-07-25 102126" src="https://github.com/user-attachments/assets/643e49a5-407b-40de-ad22-864ad42e6f29" />

### 4. Data Analysis
This section focuses on analyzing business data extracted from PostgreSQL using SQL and visualized in Google Colab with Python libraries like Matplotlib and Seaborn. The goal is to uncover meaningful patterns, trends, and actionable insights related to customer behavior, sales performance, and operational efficiency — simulating a real-world scenario similar to Zepto's fast-paced delivery model.

#### 1. List out the category
```
select distinct category
from zepto
order by category;
```
<img width="258" height="488" alt="Screenshot 2025-07-25 094823" src="https://github.com/user-attachments/assets/5e17436a-2798-405a-8e4b-a539647d4711" />

#### 2. Product In Stock Vs Out of Stock
```
select outofstock,count(s_no)    -- false means "In Stock"
from zepto						 -- true means "Out of Stock"
group by outofstock;
```
<img width="246" height="115" alt="Screenshot 2025-07-25 095925" src="https://github.com/user-attachments/assets/74094bf1-0071-48e6-b9ea-ea79c5ae49de" />

Graphical Analysis

<img width="973" height="606" alt="Screenshot 2025-07-25 125652" src="https://github.com/user-attachments/assets/578f4ee2-6430-477d-a184-96b634bcc802" />

#### 3. Average MRP by product category
```
select category, ROUND(AVG(mrp),2) as Avg_mrp
FROM zepto
GROUP BY category
ORDER BY Avg_mrp DESC;
```
<img width="347" height="486" alt="image" src="https://github.com/user-attachments/assets/9df68aa5-962a-47fe-ab5f-7018e762aeaf" />

Graphical Analysis

<img width="1226" height="604" alt="image" src="https://github.com/user-attachments/assets/cd125854-8e8e-43f2-b0f5-d9f7bc4c1629" />

#### 4. Which categories offer the deepest discounts(highes avg. discount%)
```
select category, ROUND(AVG(discountpercent),2) AS avg_discount_pct
FROM zepto
GROUP BY category
ORDER BY avg_discount_pct DESC;
```
<img width="404" height="479" alt="Screenshot 2025-07-25 134400" src="https://github.com/user-attachments/assets/109c8a0e-fb50-4b0d-a3d7-db8c164c73fc" />

Graphical Analysis

<img width="1221" height="591" alt="image" src="https://github.com/user-attachments/assets/bea5a307-48ab-497b-aa2c-dadd530c2083" />

#### 5. Top 10 Products by available stock (availablequantity)
```
select category,name,availablequantity
from zepto
order by availablequantity DESC
limit 10;
```
<img width="837" height="358" alt="Screenshot 2025-07-25 134830" src="https://github.com/user-attachments/assets/dfda9685-5cd6-45c0-94f5-a316042928ca" />

#### 6. Out-of-stock rate by category
```
select category,
	sum(case when outofstock then 1 else 0 end)::float
	/count(*) AS pct_out_of_stock
from zepto
group by category
order by pct_out_of_stock DESC;
```
<img width="434" height="488" alt="Screenshot 2025-07-25 135204" src="https://github.com/user-attachments/assets/1774b580-95a4-4239-8ec5-da53b705c2cf" />

Graphical Analysis

<img width="1222" height="600" alt="image" src="https://github.com/user-attachments/assets/5deca235-22d0-458f-a1fa-b6521bec5963" />

#### 7. Categories whether the product is light,medium, or heavy based on weight
```
select category,
	name,
	weightingms,
	case
		When weightingms < 200 THEN 'LIGHT'
		WHEN weightingms between 200 and 500 THEN 'MEDIUM'
		ELSE 'HEAVY'
	END AS weight_class
FROM zepto;
```
<img width="1080" height="512" alt="image" src="https://github.com/user-attachments/assets/abe73458-de30-476a-adc8-51a82e5757c5" />

#### 8. Total discount amount lost per product  (MRP - selling price) * avialbe quantity
```
SELECT
  name,
  category,
  ROUND((mrp - discountedsellingprice) * availablequantity, 2) AS total_discount_loss
FROM public.zepto
ORDER BY total_discount_loss DESC
LIMIT 10;
```
<img width="711" height="356" alt="image" src="https://github.com/user-attachments/assets/ad74ec2f-130a-4b9d-a976-9df8eec09bb9" />

Graphical Analysis

<img width="1229" height="597" alt="Screenshot 2025-07-25 140456" src="https://github.com/user-attachments/assets/21a9ea89-ee74-4e98-9025-14cb2121113b" />

#### 9. Average product weight per category
```
select category,
	ROUND(AVG(weightingms),2) AS avg_weight
FROM zepto
GROUP BY category
ORDER BY avg_weight DESC;
```
<img width="362" height="482" alt="image" src="https://github.com/user-attachments/assets/412545ef-7e9b-411d-86cd-88f437e4fe85" />

Graphical Analysis

<img width="1102" height="725" alt="image" src="https://github.com/user-attachments/assets/bb826dc1-c39b-415e-9a20-936e76bb1b23" />

#### 10. Compare total MRP value vs. total discounted value
```
SELECT
  ROUND(SUM(mrp * availablequantity), 2) AS total_mrp_value,
  ROUND(SUM(discountedsellingprice * availablequantity), 2) AS total_discounted_value,
  ROUND(SUM((mrp - discountedsellingprice) * availablequantity), 2) AS total_loss
FROM zepto;
```
<img width="505" height="78" alt="image" src="https://github.com/user-attachments/assets/a093e89d-5790-4067-952a-376d53b34056" />

Graphical Analysis

<img width="853" height="598" alt="image" src="https://github.com/user-attachments/assets/8c1d95da-b2c0-44e7-b9ac-d8460fcba784" />

#### 11. Create product summary per category (count, min/max/avg MRP and discount)
```
SELECT
  category,
  COUNT(*) AS total_products,
  ROUND(MIN(mrp),2) AS min_mrp,
  ROUND(MAX(mrp),2) AS max_mrp,
  ROUND(AVG(mrp),2) AS avg_mrp,
  ROUND(AVG(discountpercent),2) AS avg_discount
FROM zepto
GROUP BY category
ORDER BY total_products DESC;
```
<img width="798" height="485" alt="image" src="https://github.com/user-attachments/assets/1c568f50-a4a6-4a2a-9386-fb6215efe480" />

## Conclusion
This project demonstrates how structured data from a real-world business model like Zepto can be analyzed using PostgreSQL for querying and Python (Matplotlib, Seaborn) for visualization. Through SQL-based analysis and visual exploration, we uncovered key insights about customer behavior, product performance, and revenue trends.

The findings provide actionable insights that could help a delivery-based business optimize operations, improve customer targeting, and boost overall efficiency. The combination of clean data design, efficient queries, and compelling visualizations makes this a well-rounded data analytics project.












