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
3. They are calculated based on the filters that are applied by the report users. These filters combine to create the filter context.

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


## DAX Engines

DAX is powered by two internal engines:

1. Formula Engine
2. Storage Engine

Which work together to compress & encode raw data and evaluate DAX queries.

|              Formula Engine                        |                                Storage Engine                                            |    
|----------------------------------------------------|------------------------------------------------------------------------------------------|
| Recieves, interprets and executes all Dax Requests |  Compresses and encodes raw data, and only communicate with formula engine               |
| Processes the DAX query then generates a list of   |  Receives a query plan from Formula Engine, executes it, and returns a datacache         |
|   logical steps called a Query Plan.               |
| Works with datacache sent back from the storage    |  Note: There are two types of storage engines based on the connection type you are using |
  engine to evaluate the dax query and return        |  1. Vertipaq is used for data stored in-memory ( connected via import mode )
  a result                                           |  2. DirectQuery is used for data read directly from the source.                          |

  ## Query Evaluation in-depth

  Example Dax Query:
  
  South_Sales =
  CALCULATE(
  [customer_sales],
  FILTER(
  'Customer',
  'Customer'[region] = 'South'
  )


  1. Now this DAX query is received by the formula engine.
  2. Formula Engine interprets the query and sends a query plan to the storage engine.
  3. Storage engine executes the plan and sends a datacache back to the formula engine
  4. Formula Engine runs the DAX againist the datacache and evaluates the result.


  ## Vertipaq Columnar Data Structure

  Vertipaq uses a columnar data structure, which stores data as individual columns (rather than rows or full tables) to quickly and
  efficiently evaluate DAX queries.

This is how we look at the table:

  | product_id | product_group | product_category | wholesale_price |
  |------------|---------------|------------------|-----------------|
  | 1          | Whole Beans   | Coffee Beans     | 18              |
  | 2          | Whole Beans   | Loose Tea        | 7               |
  | 3          | Beverages     | Packaged Choco   | 20              |
  | 4          | Food          | Bakery           | 15              |


This is how vertipaq looks at the same table:

  | product_id | 
  |------------|
  | 1          | 
  | 2          | 
  | 3          |
  | 4          |


  | product_group | 
  |---------------|
  | Whole Beans   | 
  | Whole Beans   | 
  | Beverages     | 
  | Food          |


  | product_category |
  -------------------|
  | Coffee Beans     |
  | Loose Tea        |
  | Packaged Choco   | 
  | Bakery           |


  | wholesale_price |
  |-----------------|
  | 18              |
  | 7               |
  | 20              |
  | 15              |


  ## Vertipaq Compression and Encoding

  The goal of compression and encoding is to reduce the amount of memory needed to evaluate a dax query.

  Based on a sample data, one or More of the following methods will be used:

  1. Value Encoding : Mathematical process used to reduce the number of bits needed to store integer values.
  2. Hash Encoding : Identifies the disticnt string values and creates a table with indexes.
  3. Run Length Encoding : Reduces the size of a dataset by identifying repeated values found in adjacent rows

 ### Value Encoding

 It uses a mathematical process to determine relationships between the values in a column, and convert them into smaller values for storage. It only works for integer   values including currency, and cannot be applied to strings or floating - point values.

 | City ID |
 |---------|
 | 20014   |
 | 20206   |
 | 20215   |
 | 20009   |

 MAX = 20215
 15 bits needed to store this value.

 Vertipaq engine determines the relationship using mathematical process and stores it like this (removes 20 from front as that is common along the column)

 | City ID |
 |---------|
 | 14      |
 | 206     |
 | 215     |
 | 9       |

 Now, MAX = 215
requires only 8 bits needed to store this value.

and vertipaq remebers what matematical process it used to determine the relationship in that column

## Hash Encoding

It builds a dictionary of distinct items in a column, assigns a unique integer value to each item, and stores the data using index values rather than the full text strings. With hash encoding storage requirements are defined by the number of unique items in the column, NOT by the length of the string values themselves.

| Product             | Distinct Product | Distinct Index | Index |
|---------------------|------------------|----------------|-------|
| Coffee Beans        |  Coffe Beans     | 0              | 0     |
| Tea                 |  Bakery          | 1              | 2     |
| Bakery              |  Tea             | 2              | 1     |
| Bakery              |                  |                | 1     |
| Coffee Beans        |                  |                | 0     |
| Tea                 |                  |                | 2     |


## Run Length Encoding

It reduces the size of a column by replacing duplicate rows with a table containing each distinct value and count of instances.

| Numbers |
|---------|
| 10      |
| 10      |
| 9       |
| 9       |
| 10      |

This is how data is stored

| Numbers | Count |
|---------|-------|
| 10      | 3     |
| 9       | 2     |


Columns can have both Run Length and either Hash or Value Encoding. Compression type is determined by cardinality, number of repeat values, row count and datatype.





  
 
 
