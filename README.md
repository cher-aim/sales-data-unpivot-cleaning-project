# Sales Data Unpivot and Cleaning Project
> [!NOTE]
> This uncleaned data is from this website. [1.Badly Structured Sales Data 1](https://foresightbi.com.ng/microsoft-power-bi/dirty-data-samples-to-practice-on/). 

## About Dataset
> The data source owner has already assigned the task to clean the data by re-arranging the data into the correct 4 columns. This dataset has been a mix of rows and columns everywhere.

ps. You can find the .xlsx file by clicking [here](https://github.com/cher-aim/sales-data-cleaning-unpivot/tree/main/source_file) 

### 1. load data and define the column headers
> Initially, the data is loaded with generic column names that lack descriptive meaning. The first SQL query renames these columns to accurately represent the data they contain, covering various shipping modes (First Class, Same Day, Second Class, Standard Class) across different segments (Consumer, Corporate, Home Office).

```sql
SELECT 
  string_field_0 AS order_id,
  double_field_1 AS consumer_first_class,
  double_field_2 AS consumer_same_day,
  double_field_3 AS consumer_second_class,
  double_field_4 AS consumer_standard_class,
  double_field_6 AS corporate_first_class,
  double_field_7 AS corporate_same_day,
  double_field_8 AS corporate_second_class,
  double_field_9 AS corporate_standard_class,
  double_field_11 AS home_first_class,
  double_field_12 AS home_same_day,
  double_field_13 AS home_second_class,
  double_field_14 AS home_standard_class
 FROM `cher-pre-project.exc_one.sales` 
 WHERE string_field_0 != 'Grand Total'
```
This query serves as a preparatory step, ensuring the data is in a readable format before the transformation process begins.


## Dataset Column Descriptions

| Column Header             | Data Type | Description                                                   |
|---------------------------|-----------|---------------------------------------------------------------|
| `order_id`               | STRING    | Unique identifier for each order                              |
| `consumer_first_class`    | FLOAT     | Sales to consumer segment shipped via first-class delivery      |
| `consumer_same_day`      | FLOAT     | Sales to consumer segment shipped via same-day delivery       |
| `consumer_second_class`   | FLOAT     | Sales to consumer segment shipped via second-class delivery       |
| `consumer_standard_class` | FLOAT     | Sales to consumer segment shipped via standard class delivery     |
| `corporate_first_class`  | FLOAT     | Sales to corporate segment shipped via first-class delivery       |
| `corporate_same_day`  | FLOAT     | Sales to corporate segment shipped via same-day delivery      |
| `corporate_second_class`| FLOAT     | Sales to corporate segment shipped via second-class delivery      |
| `corporate_standard_class` | FLOAT    | Sales to corporate segment shipped via standard class delivery     |
| `home_first_class`       | FLOAT     | Sales to home office segment shipped via first-class delivery      |
| `home_same_day`          | FLOAT     | Sales to home office segment shipped via same-day delivery    |
| `home_second_class`     | FLOAT     | Sales to home office segment shipped via second-class delivery     |
| `home_standard_class`   | FLOAT     | Sales to home office segment shipped via standard class delivery   |




### 2. unpivot data
> The core of this project lies in the "unpivoting" process, where the data structured in a wide format is transformed into a long format. This restructuring is necessary for several reasons:
- **Analysis Readiness:** Long format data is generally more suitable for analysis in tools like PowerBI and Tableau.
- **Segment & Ship Mode Differentiation:** It allows for a clear distinction between segments (Consumer, Corporate, Home Office) and shipping modes (First Class, Same Day, Second Class, Standard Class).
- **Flexibility:** Unpivoted data can be more easily manipulated and filtered according to analytical needs.

```sql

SELECT
  order_id,
  'consumer' AS segment,
  'first class' AS ship_mode,
   consumer_first_class AS sales
FROM `cher-pre-project.exc_one.sales_1`
WHERE consumer_first_class IS NOT NULL
UNION ALL

SELECT
  order_id,
  'consumer' AS segment,
  'same day' AS ship_mode,
  consumer_same_day AS sales
FROM `cher-pre-project.exc_one.sales_1`
WHERE consumer_same_day IS NOT NULL

UNION ALL

SELECT
  order_id,
  'consumer' AS segment,
  'second class' AS ship_mode,
  consumer_second_class AS sales
FROM `cher-pre-project.exc_one.sales_1`
WHERE consumer_second_class IS NOT NULL

UNION ALL

SELECT
  order_id,
  'consumer' AS segment,
  'standard class' AS ship_mode,
  consumer_standard_class AS sales
FROM `cher-pre-project.exc_one.sales_1`
WHERE consumer_standard_class IS NOT NULL

-- Repeat the pattern for other combinations

UNION ALL

SELECT
  order_id, 
  'corporate' AS segment,
  'first class' AS ship_mode,
  corporate_first_class AS sales
FROM `cher-pre-project.exc_one.sales_1`
WHERE corporate_first_class IS NOT NULL

UNION ALL

SELECT
  order_id,
  'corporate' AS segment,
  'same day' AS ship_mode,
  corporate_same_day AS sales
FROM `cher-pre-project.exc_one.sales_1`
WHERE corporate_same_day IS NOT NULL

UNION ALL

SELECT
  order_id,
  'corporate' AS segment,
  'second class' AS ship_mode,
  corporate_second_class AS sales
FROM `cher-pre-project.exc_one.sales_1`
WHERE corporate_second_class IS NOT NULL

UNION ALL

SELECT
  order_id,
  'corporate' AS segment,
  'standard class' AS ship_mode,
  corporate_standard_class AS sales
FROM `cher-pre-project.exc_one.sales_1`
WHERE corporate_standard_class IS NOT NULL

-- Repeat the pattern for other combinations

UNION ALL

SELECT
  order_id,
  'home office' AS segment,
  'first class' AS ship_mode,
  home_first_class AS sales
FROM `cher-pre-project.exc_one.sales_1`
WHERE home_first_class IS NOT NULL

UNION ALL

SELECT
  order_id,
  'home office' AS segment,
  'same day' AS ship_mode,
  home_same_day AS sales
FROM `cher-pre-project.exc_one.sales_1`
WHERE home_same_day IS NOT NULL

UNION ALL

SELECT
  order_id,
  'home office' AS segment,
  'second class' AS ship_mode,
  home_second_class AS sales
FROM `cher-pre-project.exc_one.sales_1`
WHERE home_second_class IS NOT NULL

UNION ALL

SELECT 
  order_id,
  'home office' AS segment,
  'standard class' AS ship_mode,
  home_standard_class AS sales
FROM `cher-pre-project.exc_one.sales_1`
WHERE home_standard_class IS NOT NULL;

```
> This SQL script uses a series of **SELECT** statements combined with **UNION ALL** to compile all combinations of order ID, segment, ship mode, and sales into a single, unified table. 

For each **SELECT** query:
- order_id is selected directly without transformation.
- The segment (e.g., 'consumer', 'corporate', 'home office') is specified explicitly in the query.
- The ship mode (e.g., 'first class', 'same day', 'second class', 'standard class') is also specified explicitly.
- Sales amounts are selected from their respective columns based on the segment and ship mode being processed.

> It's crucial to include the **WHERE** clause to exclude null values, ensuring that only rows with actual sales data are included in the final dataset.

## Data Transformation: Pivot to Unpivot

The goal of this step is to transform the dataset from a pivoted (wide) format, where shipping modes and customer segments are spread across multiple columns, to an unpivoted (long) format. This transformation makes the dataset more suitable for analysis and visualization.

### Original Pivoted (Wide) Format Example

Here's a simplified view of the original dataset in its wide format:

| order_id | consumer_first_class | consumer_standard_class | corporate_first_class | corporate_standard_class |
|----------|----------------------|-------------------------|-----------------------|--------------------------|
| 001A     | 100.0                | NULL                  | NULL                  | NULL                     |
| 002B     | NULL                | NULL                    | NULL               | 260.0                    |
| 003C     | NULL                 | 230.0                   | NULL                  | NULL                     |

In this format, each row represents an order, with separate columns for sales amounts across different shipping modes and customer segments.

### Transformed Unpivoted (Long) Format Example

After transformation, the dataset takes on a long format, consolidating shipping modes and customer segments into single columns:

| order_id | segment    | ship_mode       | sales |
|----------|------------|-----------------|-------|
| 001A     | consumer   | first class     | 100.0 |
| 002B     | consumer   | standard class  | 200.0 |
| 003C     | consumer   | standard class  | 230.0 |

In the unpivoted format, each row now represents a unique combination of `order_id`, `segment`, `ship_mode`, and `sales`, making the data more accessible for analysis and reporting.








