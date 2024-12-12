
*Confidentiality Notice: Due to client confidentiality, only a screenshot of the PowerBI dashboard is provided. The full details of the dashboard are omitted.*

# PowerBI Dashboard for Sales and Employee Status Monitoring

## Overview
This PowerBI dashboard provides a comprehensive view of the company's sales, employee status, and product arrival times. The dashboard is designed to help users monitor key metrics, visualize data trends, and gain insights into operational efficiency. Due to client confidentiality, the full details of the dashboard are not shown here. Only a screenshot is available for demonstration purposes.

## Features
- **Main Page**
  - **Country Selector**: Allows users to select a specific country to filter the data.
  - **Branch Selector**: Filters data by branch of the company.
  - **Customer Information**:
    - **Customer Name**: Displays the name of the selected customer.
    - **Customer Code**: Shows the unique customer code associated with the selected branch.
    
:<img width="580" alt="main" src="https://github.com/user-attachments/assets/6999e75d-0491-4411-9e3f-9c1d07ab2d22" />
- **Overall Numbers**
  - Summarizes key metrics such as total sales, employee count, and average product arrival time for the selected filters.


- **Chart Visualizations**:
<img width="494" alt="tat days" src="https://github.com/user-attachments/assets/4f38d180-4028-469b-8b9c-058bcc0f61de" />

  - **Turn Around Time (TAT)**: Visualizes the average turnaround time for products from order to delivery.
  - **Approved Lead Time (ALT)**: Displays the approved lead time for each product.
  - **Quoted Days**: Show the difference between the quoted lead time and the actual delivery time.

- **Product Arrival**:
  - Provides a timeline view of product arrival for the selected branch and country, highlighting any delays or early arrivals.

## DAX Formulas
```DAX
{
    // Calculate Duplicate Count
    DuplicateCount = CALCULATE(
        COUNTROWS('Finished'),
        FILTER(
            'Finished',
            'Finished'[P/N] = EARLIER('Finished'[P/N])
        )
    ),

    // Replace duplicates with "xxx"
    NewP/N = IF(
        [DuplicateCount] > 1, 
        IF(
            LEN(FORMAT([P/N], "0")) > 4,
            LEFT(FORMAT([P/N], "0"), LEN(FORMAT([P/N], "0")) - 4) & "xxx",
            FORMAT([P/N], "0")
        ), 
        FORMAT([P/N], "0")
    ),

    // Quantity Since 2020
    Quantity Since 2020 = 
    IF(
        YEAR('In progress with WO'[Startdate MCO]) >= 2020, 
        'In progress with WO'[Qty], 
        0
    ),

    // Custom Column - Country
    Country = 
    IF(
        TEXT.CONTAINS([Source.Name], "L01"), 
        "SCS", 
        IF(
            TEXT.CONTAINS([Source.Name], "101"), 
            "DNR", 
            "Unknown"
        )
    ),

    // TAT Days
    TAT Days = DATEDIFF('Finished'[Startdate MCO], 'Finished'[Real End Date], DAY),

    // Quoted Days
    Quoted Days = DATEDIFF('Finished'[Startdate MCO], 'Finished'[QUOTED], DAY),

    // Approval Lead Time
    Approval Lead Time = DATEDIFF('Finished'[QUOTED], 'Finished'[APPROVED ON], DAY)
}



