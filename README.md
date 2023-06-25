# DAX in Power BI

## What is DAX ?
1. DAX stands for DATA ANALYSIS EXPRESSION.
2. DAX is a programming language that is used throughout MS power BI for creating Calculated Columns, Measures and Tables.
3. DAX is a collection of functions, operators that can be used in a formula or expression to calculate or return on more values.
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
