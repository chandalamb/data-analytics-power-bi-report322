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
