# DAX in Power BI

## What is DAX ?
1. DAX stands for DATA ANALYSIS EXPRESSION.
2. DAX is a programming language that is used throughout MS power BI for creating Calculated Columns, Measures and Tables.
3. DAX is a collection of functions, operators that can be used in a formula or expression to calculate or return one more values.
4. By using DAX we can perform data analysis.

## Calculated Columns
1. These are used to perform row by row calculation.
2. For example profit, any flag type columns etc.
3. They are stored in the table as a column
4. Calculated Columns Values are stored in Power BI .pbix file. Each Calculated Column will increase the space that is used in that file and potentially increase the refresh time.
5. Calculated Columns doesnot work for Aggregation function(SUM,AVG,MAX etc.)

## Measures
1. Measures are calculated on demand. Power BI calculates the correct value when the user requests it.
2. Measures do not add to overall diskspace of the .pbix file.
3. They are calculated based on the filters that are used by the report users. These filters combine to create the filter context.

## Filter Context
Filter Context is a set of filters applied to the data model before the evaluation of the dax starts.

Products Table :

| Product Key | Brand   |
|-------------|---------|
| 1           | BMW     |
| 2           | Maruti  |
| 3           | Huyndai |

Sales Table:

| Product Key | Sales Amount   |
|-------------|----------------|
| 1           | 10000000       |
| 2           | 500000         |
| 1           | 5000000        |
| 2           | 800000         |
| 3           | 1000000        |
| 3           | 900000         |


Table Visualization:

|  Brand   |  Sum Of Sales Amount  |
|----------|-----------------------|
|  BMW     |  15000000             |
|  Maruti  |  1300000              |
|  Huyndai |  1900000              |

Let's say we want to see total sales of Maruti Brand. So behind the scenes this is how it works, Firstly both the tables are connected to each other through Product key and has one to many cardinality where the filter flows from Product Table to Sales Table. Now let's understand filter context step by step:
Set of filters applied -
In the products table firstly Maruti brand is filtered and associated product key is captured.

Products Table :

| Product Key | Brand   |
|-------------|---------|
| 2           | Maruti  |


Based on the product key the sales table is filtered.

Sales Table:

| Product Key | Sales Amount   |
|-------------|----------------|
| 2           | 500000         |
| 2           | 800000         |

Now the aggregation is applied on the sales table sales amount column for produtc key 2 which maruti brand.

|  Brand   |  Sum Of Sales Amount  |
|----------|-----------------------|
|  Maruti  |  1300000              |

Same filtering happens in the same way for other product keys as well.


## Row Context
1. It evaluates row by row.
2. It checks the condition row by row and evaluates
3. When you create a calculated column, row context will be created automatically.
4. The expression used for calculated column will not be work for measures
5. since, calculated columns will not work for aggregation functions.

Person Details:

| Name | Age |
|------|-----|
| A    | 18  |
| B    | 10  |
| C    | 25  |
| D    | 35  |
| E    | 28  |
| F    | 38  |
| G    | 17  |

Let's say we have a problem statement that say's we need to check voting eligibility for the given dataset. Which means we need to check row by row so which leads us to create a calculated column because it automatically creates row context.

Eligible(Calculated Column) = IF (
                'Person'[Age] >= 18,
                "T",
                "F"
              )

              
| Name | Age | Eligible |
|------|-----|----------|
| A    | 18  | T        |
| B    | 10  | F        |
| C    | 25  | T        |
| D    | 35  | T        |
| E    | 28  | T        |
| F    | 38  | T        |
| G    | 17  | F        |


## Data & Storage Types

DAX uses 6 Datatypes to store values:

| DAX Data Type | Power BI Data Type    | Storage Type                             | Example                          |
|---------------|-----------------------|------------------------------------------|----------------------------------|
| Integer       | Whole Number          | 64-bit                                   | Max : 9,223,372,036,854,775,807  |
| Decimal       | Decimal Number        | Double-precision floating value          | 64-bit precision                 |
| Currency      | Fixed Decimal Number  | Fixed Decimal Number(Stored as integere) | 317.9899                         |
| DateTime      | DateTime, Date , Time | 64-bit                                   | 1/1/2020 12:00p = 43830.50       |
| Boolean       | True/False            | True/False                               | True/False                       | 
| String        | Unicode string        | 16-bit                                   | "Ravi Teja" = "RAVI TEJA"        |
