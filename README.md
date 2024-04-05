# Data Analytics Power BI Report

This project is to produce a report that will present a high-level business summary tailored for C-suite executives, and also give insights into their highest value customers segmented by sales region, provide a detailed analysis of top-performing products categorised by type against their sales targets, and a visually appealing map visual that spotlights the performance metrics of their retail outlets across different territories.

The project will be based on the following scenario:

A medium-sized international retailer who is keen on elevating their business intelligence practices. With operations spanning across different regions, they've accumulated large amounts of sales from disparate sources over the years.

Recognizing the value of this data, they aim to transform it into actionable insights for better decision-making. The goal is to use Microsoft Power BI to design a comprehensive Quarterly report. This will involve extracting and transforming data from various origins, designing a robust data model rooted in a star-based schema, and then constructing a multi-page report.

## Importing the data


This project involves importing multiple data tables through various methods.

**Orders**: Imported from an Azure SQL Database, this table requires selection under the Azure heading in the "get data" function. After inputting correct database details, transformations were applied to ensure data usability. Initially, the [Card Number] column was deleted for privacy. The [Order Date] and [Shipping Date] columns were then split into date and time columns. Null values in the [Order Date] column were filtered out to maintain data integrity. Column renaming was performed to align with Power BI conventions for consistency and clarity.

**Products**: Loading this data requires selecting the Text/CSV option in "get data." The Remove Duplicates function ensures uniqueness in the product_code column. The Column From Examples feature generates new columns from the weight column for values and units. Data type conversion and error value replacement are performed, followed by the creation of a new weight in kg column for consistency. Redundant columns are removed using Power Query Editor.

**Stores**: Azure Blob Storage is accessed using the Get Data option to import the Stores table. Column renaming aligns with Power BI conventions for clarity and consistency.

**Customers**: This table comprises multiple CSV files with the same column format, each representing a region. The Customers folder is imported using the Folder data connector in Power BI. Combine and Transform merges the files into one query. A Full Name column is created by combining [First Name] and [Last Name] columns.

## Data Model

**Date Table**: The date table runs from the start of the year containing the earliest date in the Orders['Order Date'] column to the end of the year containing the latest date in the Orders['Shipping Date'] column. This is the DAX formula that was used to create the Date Table:
![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/912c136d-297d-4203-8fa7-9f837b1b8614)

The following columns are then added to the date table:

- Day of Week = FORMAT([Date], "dddd")
- Month Number (i.e. Jan = 1, Dec = 12 etc.) = [Date].[MonthNo]
- Month Name = [Date].[Month]
- Quarter = [Date].[Quarter]
- Year = [Date].[Year]
- Start of Year = STARTOFYEAR(Dates[Date])
- Start of Quarter = STARTOFQUARTER(Dates[Date])
- Start of Month = STARTOFMONTH(Dates[Date])
- Start of Week = [Date] - WEEKDAY([Date], 2) + 1

Schema: The relationships should form a star schema as follows:

- Orders[product_code] to Products[product_code]
- Orders[Store Code] to Stores[store code]
- Orders[User ID] to Customers[User UUID]
- Orders[Order Date] to Date[date]
- Orders[Shipping Date] to Date[date]

![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/7c153e3d-5bc9-4ef9-b055-b6470cfdf2c0)

**Measures Table**: This table will be used to store the measures created throughout the project to keep them orgainsed and not cluster the original tables.

The first addition to this table will be some of the key measures:
- Total Orders that counts the number of orders in the Orders table.
  
  ![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/8b911fc0-23ab-48a5-ba3a-3b5c7f642c57)

- Total Revenue that multiplies the Orders[Product Quantity] column by the Products[Sale_Price] column for each row, and then sums the result.
  
  ![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/3343a31b-4433-4d08-bb14-34f94fb4798a)

- Total Profit which performs the following calculation: For each row, subtract the Products[Cost_Price] from the Products[Sale_Price], and then multiply the result by the Orders[Product Quantity]. Sums the result for all rows.
  
  ![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/31f5ecbc-1590-47a3-93bc-089ab3a1e995)

- Total Customers that counts the number of unique customers in the Orders table. This measure needs to change as the Orders table is filtered, so do not just count the rows of the Customers table.
  
  ![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/3b1f3304-7a11-41cf-b021-863d6cf4b934)

- Total Quantity that counts the number of items sold in the Orders table.
  
  ![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/021f7d27-f9ed-41ed-adfa-01e8d6bce4f3)

- Profit YTD that calculates the total profit for the current year.
  
  ![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/1d827453-2e3f-46e2-9574-40862d9af04d)

- Revenue YTD that calculates the total revenue for the current year.
  
  ![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/be375ab0-171e-4d47-b4f2-cfd9a69799d5)

**Date and Geography Hierarchies**:

Date hierarchy:

- Start of Year
- Start of Quarter
- Start of Month
- Start of Week
- Date

Geography hierarchy:

- World Region
- Country
- Country Region

## The Customer Details Page

The page provides an in-depth look at which Customers from all stores are spending the most, with the option to filter by time-frame and region.

**Headline Card Visuals**: Two visuals at the top of the page to highlight important information in an easily digestible format.

For the **Unique Customers visual**, add a card visual for the [Total Customers] measure and rename the field.

For the **Revenue per Customer visual**, a new [Revenue per Customer] measure is needed. This should be the [Total Revenue] divided by the [Total Customers].

![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/ca29e6bb-4c33-4d37-b4b7-d7bd25b59e20)

![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/94d7b03a-3ebe-43c8-abc6-cf2872561dc9)

**Summary Charts**: These charts can be found underneath the card visuals. Each chart will provide information about the total customers.
- The **Donut Chart** visual created shows the total customers for each country, using the Users[Country] column to filter the [Total Customers] measure.
- 
  ![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/827fdd23-3ac7-4db8-abec-2c2cb7bf0844)
  
- The **Column Chart** visual shows the number of customers who purchased each product category, using the Products[Category] column to filter the [Total Customers] measure.
  
  ![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/6a3273aa-0411-44dc-846e-5b9af5eaa5d8)
  
- **Line Chart**: The Line Chart visual shows [Total Customers] on the Y axis, and the Date Hierarchy for the X axis. Allowing users to drill down to the month level, but not to weeks or individual dates. With the addition of a trend line, and a forecast for the next 10 periods with a 95% confidence interval.
  ![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/99d04595-52a3-4db0-8c0b-db5ab208b3af)
  
- **Top 20 Customers Table**: This displays the top 20 customers, filtered by revenue. It shows each customer's full name, revenue, and number of orders. Conditional formatting has been applied to the revenue column, to display data bars for the revenue values (seen in green in the image below). This makes it much clearer to see which customer has brought in the most revenue.
  ![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/ceb722cd-04ef-445c-ae4e-83a6ae23c875)
  
- **Top Customer Cards**: A set of three card visuals that provide insights into the top customer by revenue. They display the top customer's name, the number of orders made by the customer, and the total revenue generated by the customer.
  
- ![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/8a97b0af-3893-4bd9-ae26-ef56805a22b4)

**Date Slicer**: A slicer in the between slicer style to allow users to filter the page by year.

![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/dd0ae183-2f57-4527-acdc-fbff6f91a4b5)

**Page View**:

![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/f9d38eb8-ad89-498b-bb48-27310c99f4ef)

## The Executive Summary Page

This page gives an overview of the company's performance as a whole, so that C-suite executives can quickly get insights and check outcomes against key targets.

**Card Visuals**: Three cards that span about half of the width of the page. One for Total Revenue, Total Orders and Total Profit measures. Using the Format > Callout Value pane to ensure no more than 2 decimal places in the case of the revenue and profit cards, and only 1 decimal place in the case of the Total Orders measure.

![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/0a8a646d-aa6f-4403-ab8d-b30bb610549c)

**Line Chart**: A copy of the line graph from the Customer Detail page, with the following changes:

- X axis set to the Date Hierarchy, with only the Start of Year, Start of Quarter and Start of Month levels displayed
- Y axis set to the Total Revenue

![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/530ec21f-de9b-4bb6-8007-e7c2ab21cc54)

**Donut Charts**: A pair of donut charts, showing Total Revenue broken down by Store[Country] and Store[Store Type] respectively.
  
![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/063502bb-be88-4f97-a7dc-be971b9a9f2e)

**Bar Chart**: A bar chart showing number of orders by product category. This can be completed quickly using the a copy of the Total Customers by Product Category donut chart from the Customer Detail page. Using the on-object Build a visual pane and changing the visual type to Clustered bar chart.Also the X axis field needs to be changed from Total Customers to Total Orders.
  
![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/d3027fe5-fa30-4686-b81a-96c07c271e24)

**KPI Visuals**: To make KPIs for Quarterly Revenue, Orders and Profit a set of new measures for the quarterly targets need me be made. These measure are:

1. Previous Quarter Profit
2. Previous Quarter Revenue
3. Previous Quarter Orders
4. 5% Target growth in each measure compared to the previous quarter

KPI for the revenue: The Value field is Total Revenue, Trend Axis set to Start of Quarter and Target is Target Revenue

In the Format pane,the values are set as follows:

- Trend Axis: on
- Direction : High is Good
- Bad Colour : red
- Transparency : 15%
- The Callout Value set to show only to 1 decimal place.

The KPI's for orders and profit are made in the same way but with their respective Total's and Target's.

![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/92566672-055e-45c2-a5e6-4f055168e820)

### Page View:

![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/b25fcea6-55b4-4cb1-bb19-deb101e70b1c)

## The Product Detail Page

The purpose of this page is provide an in-depth look at which products within the inventory are performing well, with the option to filter by product and region.

**Gauge Visuals**: A set of three gauges, showing the current-quarter performance of Orders, Revenue and Profit against a quarterly target, with a 10% quarter-on-quarter growth target in all three metrics.

To achieve this new DAX measures for the three metrics, and for the quarterly targets of each metric. The DAX formula for Revenue provided below.

![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/5f1b989a-8b9e-4d23-8b58-0f8d0d9955c3)

The maximum value of the gauges are set to the target, so that the gauge shows as full when the target is met. Conditional formatting is applied to the callout value (the number in the middle of the gauge), so that it shows as red if the target is not yet met, and blue otherwise.

(This space is for a screen shot of the Gauge Visuals once they are working)

**Card Visuals**: For this page, two card visuals are used to display the top product by both total revenue and orders. This is achieved by using the filter pane and filtering Product[Description] by revenue and orders respectively then setting the filter type to Top N and then setting N to one.

![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/1afafb53-d13f-43cd-97ca-4fb9dff079d1)

**Area Chart**: An area chart that shows how the different product categories are performing in terms of revenue over time.
This chart the following fields:

- X axis - Dates[Start of Quarter]
- Y axis - Total Revenue
- Legend - Products[Category]

![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/ad472510-35a1-4ddb-b6b3-633252450a93)

This chart shows that there are clearly two categories (homeware and toys-and-games) which bring in the majority of the total revenue.

**Top Products Table**: The top 10 products table has the following fields:

- Product Description
- Total Revenue
- Total Customers
- Total Orders
- Profit per Order

![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/bc065a1a-9b8c-4b96-becd-4aef91464e72)

**Scatter Graph**: This visual shows which product ranges are both top-selling items and also profitable. For this a new calculated column [Profit per Item] is needed in the Products table.

![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/1659e935-bc8e-4b67-8a0b-ba568eece88c)

The visual is configured as follows:
- Values - Products[Description]
- X Axis - Products[Profit per Item]
- Y Axis - Products[Total Quantity]
- Legend - Products[Category]

![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/bb838b57-3536-49b6-89d3-0cdee15b2b09)

**Slicer Toolbar**: Slicers allow users to control how the data on a page is filtered. Thus it would be ideal to have a pop-out toolbar which can be accessed from a navigation bar On the left-hand side of the report.

A button at the top of the navigation bar is used to open the slicer panel with the tooltip text set to Open Slicer Panel. The Slicer Panel is the same height as the page, and about 3-5X the width of the navigation bar itself. It contains two slicers, Product Category and Country respectively. A back button in the top right can hide the slicer toolbar when it's not in use.

![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/a6db0f2f-f64c-4c7d-aefa-fed3d72a6ecc)
![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/66e2b3b2-6b7f-47fa-a00a-c5f9a871b4d4)

## Store Map Page

**Map Visual**: The Geography hierarchy is assigned to the Location field, and ProfitYTD to the Bubble size field.

The controls of the map are as follows:

Auto-Zoom: On
Zoom buttons: Off
Lasso button: Off

![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/9c5d1bce-ba9f-4b3a-9e5a-a2b6590df247)

**Stores Drillthrough Page**: The stores drillthough page summarises each store's performance. It includes the following visuals:

- A table showing the top 5 products, with columns: Description, Profit YTD, Total Orders, Total Revenue
- A column chart showing Total Orders by product category for the store
- Gauges for Profit YTD against a profit target of 20% year-on-year growth vs. the same period in the previous year. The gauges require additional measures: Profit Goal and Revenue Goal, which should be a 20% increase on the previous year's year-to-date Alt text
- A Card visual showing the currently selected store

The drillthough page can be seen below.

![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/29ba9607-4816-4e4b-b194-769a02c72364)

**Stores Tooltip**: This is what pops up when a location on the map visual is hoverd over. It consists of the profit guage and the store card visuals.

![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/8f126434-8b65-492c-bae6-15b840be75e9)

## Cross-Filtering and Navigation
**The cross-filtering:**

1. Executive Summary Page: Product Category bar chart and Top 10 Products table have been changed to not filter the card visuals or KPIs

2. Customer Detail Page:
- Top 20 Customers table no longer filters any of the other visuals
- Total Customers by Product Donut Chart doesn't affect the Customers line graph
- Total Customers by Country donut chart doesn't affect Total Customers by Product donut Chart

3. Product Detail Page: Orders vs. Profitability scatter graph and Top 10 Products table no longer filter any other visuals.

**Navigation Bar:** The navigation bar consists of four buttons for the individual report pages. There is a white version for the default button appearance, and a blue one so that the button changes colour when hovered over with the mouse pointer. (These are the ones mentioned in the slicer toolbar section above).

![image](https://github.com/chandalamb/data-analytics-power-bi-report322/assets/154320747/6a9ba21d-c859-4a38-a0b4-e4d6c77723ca)

## Metrics for Users Outside the Company Using SQL
The data is stored on a Postgres database server hosted on Microsoft Azure.

The table and column names in this database are different from the ones in Power BI.

To help understand the differences, there is a 'Table information' folder of this project. This folder includes:

- a list of the tables in the database
- a list of the columns in the table for each table

**SQL queries:** For this last section, there is another folder called 'SQL Questions'. This folder contains both a sql file and a csv file for each of the following questions:

1. How many staff are there in all of the UK stores?
2. Which month in 2022 has had the highest revenue?
3. Which German store type had the highest revenue for 2022?
4. Create a view where the rows are the store types and the columns are the total sales, percentage of total sales and the count of orders
5. Which product category generated the most profit for the "Wiltshire, UK" region in 2021?
