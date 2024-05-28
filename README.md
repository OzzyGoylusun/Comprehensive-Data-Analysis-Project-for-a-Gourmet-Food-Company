
# TBD

## Table of Contents

- [Exploratory Data Analysis](#exploratory-data-analysis)
- [Findings](#findings)
- [Recommendations](#recommendations)

### Project Overview
---
This comprehensive data analysis project is designed to assist a gourmet food company, named Northwind, with increasing its overall revenue by proposing a number of actions for consideration of the corporation. 

In this respect, the data analysis comprises of seven (7) different components, four of whose outcomes, described below, have been visualised by means of a Power BI dashboard with elegant design principles in mind:

* Revenue Analysis
* Sales Analysis
* Customer Analysis (with an RFM component)
* Product-Price Analysis

The remaining three analyses, described below, have been visualised using a combination of Python's Matplotlib and Seaborn libraries:

* Supplier Analysis
* Shipper Analysis
* Inventory Optimisation Analysis

****

### Data Sources

Results of our data analysis hinge on corresponding SQL analysis derived from the Northwind databased populated with the file, **Northwind Project DB Query**

### Tools

- PostgreSQL Server - Data Analysis
  - [Download Here](https://www.postgresql.org/download/)
- [Anaconda Navigator: ](https://www.anaconda.com/download)
  - To access the Jupyter Notebook for **Python**
- Power BI Desktop - Data Manipulation, Analysis and Visualisation
  - [Download here](https://www.microsoft.com/en-us/download/details.aspx?id=58494)

### Data Preparation

### Exploratory Data Analysis

EDA involved exploring the commercial data to answer some key questions, including but not limited to:

1. What are the top 10 highest-revenue countries and the top 10 countries with the highest-revenue-per-order up to date?
2. Is there any seasonality observed when it comes to the company's monthly number of sales?
3. How many percent of customers classified through an RFM analysis as **"Loyal Customers"** or **"Champions"** have overall received less discount than the average discount rate?
4. How many percent of all suppliers have performed below par in terms of order processing speed where statistical significance exist?
5. Is there any opportunity cost surrounding the proper engagement of shippers to curtail the overall logistics expenses per top-performing country?

### Data Analysis

In my humble opinion, while conducting the supplier analysis in order to gauge each supplier's order processing performance, I needed to filter in all the orders that were single-handedly processed by one particular supplier:

```sql
...
WITH FILTER_FOR_IDENTIFICATION_OF_ORDERS_SUPPLIED_SINGLEHANDEDLY AS
(
	SELECT ORDER_ID,
		COUNT(ROW_COUNTER)

	FROM SINGLE_MULTIPLE_SUPPLIED_ORDER_INFO
	GROUP BY 1
	HAVING (COUNT(ROW_COUNTER) = 1)--This assists with filtering out orders that have been processed by multiple suppliers, as it introduces complexity into suppliers' order processing performance analysis
) 
...
```
Afterwards, there turned out to be some suppliers who did not process a significant number of orders. To achieve statistical significance, I also needed to filter out all the suppliers whose order numbers remained below par, based on the resulting dataset's median value.

```sql
WITH FILTERED_SUPPLIER_AVERAGE_DELIVERY_SPEED_AND_ORDERS_PROCESSED AS
	(
      ...
      SELECT SUPPLIER_NAME,
		SUPPLIER_COUNTRY,
		AVERAGE_PROCESSING_SPEED_IN_DAYS,
		NUMBER_OF_ORDERS_PROCESSED,
		CASE WHEN (SELECT PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY NUMBER_OF_ORDERS_PROCESSED)
 			   FROM SUPPLIER_AVERAGE_DELIVERY_SPEED_AND_ORDERS_PROCESSED)::NUMERIC(10,2) > NUMBER_OF_ORDERS_PROCESSED THEN 'Statistically Insignificant'
                     ELSE 'Statistically Significant'
		     END AS STATISTICAL_IMPORTANCE

      FROM SUPPLIER_AVERAGE_DELIVERY_SPEED_AND_ORDERS_PROCESSED --We are only taking into account the suppliers that have processed a 'significant' number of orders
) 
...
```
In the end, filtering out these suppliers as well resulted in a clearer dataset on which to conduct this statistical analysis in order to evaluate the supplier performance in an accurrate fashion.

### Findings

The critical analysis results are summarised as follows:

1. While **the US, Germany, Brazil and France** are among the top highest-revenue generating countries, **Austria, Ireland and Belgium** turn out to generate the most revenue per order placed for the company.
2. There appears to be seasonality where the company experiences a significant drop in total sales during the months **May and June**.
3. A whopping **~43% of all customers RFM-classified as 'Loyal Customers' or 'Champions'** have been awarded below-the-par average discount rate, which is 7%.
4. Only **~39% of all suppliers**, where there exist statistical significance, have managed to process orders at a faster pace than the average processing day, which hovers around 8.32 days.
5. There are notable variances in logistics companies' fees even within the same country, for example in **Belgium**.

### Recommendations

Based on the analysis above, I propose the following recommendations:

* Emphasize maintaining a consistently high total number of sales in countries like the US, Germany and Brazil where a greater number of sales has shown a positive correlation with higher revenue generated.
* Introduce several new products into the existing product portfolio, such as summer-beginner-friendly treats, to offset the declining number of sales in May and June.
* Offer a discount rate higher than the average to all 'Loyal Customers' and 'Champions' and communicate with them how their substantial spending has enabled them to benefit from such a rate.
* Compile a mitigation plan for underperforming suppliers to accelerate their overall order processing.
* Pinpoint the shipping company charging the lowest rates within each country and engage exclusively with it.

### Limitations

As the dataset was based upon a static SQL file, it is inherently deprived of any prospective data which can impact subsequent strategical and/or tactical decisions that the gourmet food company may choose to make.

### References

No references needed as part of this project.
