### Methodology

#### 1. Data Preparation & Structure

The dashboard is built on multiple quarterly mortgage-related datasets, each representing a distinct product stream: “Further Advance Under an Existing Mortgage”, “Further Advance Under a New Mortgage”, “Internal Product Transfer”, “Lifetime Mortgage”, and “Retirement Interest-Only Mortgage”. These datasets were ingested individually and structured in a fact-dimension model, with each dataset containing three essential fields:
- `account_open_date` – timestamp of the mortgage issuance, later transformed to a quarter-based granularity
- `category` – the type or classification of the mortgage product
- `number_of_sales` – the count of mortgage transactions per category and period

All datasets were unioned under a unified reporting layer and mapped against a common `Date` dimension table to enable consistent temporal slicing across visuals.

To enable comparative analysis, a new DAX measure named `%GT of Total number_of_sales` was created. This measure calculates the percentage contribution of a given category to the total number of sales across all categories within the selected context. This is useful for highlighting category-level dominance or trend shifts over time.

Additional calculated measures were grouped under `sales_by_loan_to_value_ratio`, providing month-over-month (`M%M`), current period, and delta metrics, although these are more prominently used in subsequent dashboard pages.

#### 2. Sales Composition & Comparative Analysis

The “Sales data analysis” page offers a dual-view breakdown of mortgage sales, analyzing both loan-to-value (LTV) ratios and borrower types. This structure enables stakeholders to monitor risk distribution and shifts in consumer behavior over time.

On the left side, transactions are segmented by LTV bands, ranging from <30% to >95%, highlighting the relative proportion of low- versus high-risk lending. These are visualized using stacked area charts to display temporal dynamics and radial bar charts for categorical comparison. Supporting KPI tables show the current and previous month’s sales volume per band, with calculated differences and month-over-month percentage change (M%M), implemented using DAX measures such as:

```DAX
ratio This month = CALCULATE(SUM(sales_by_loan_to_value_ratio[number_of_sales]), DATEADD('Date'[Date], 0, MONTH))
ratio Last month  = CALCULATE(SUM(sales_by_loan_to_value_ratio[number_of_sales]), DATEADD('Date'[Date], -1, MONTH))
ratio Difference  = [ratio This month] - [ratio Last month]
ratio M%M         = DIVIDE([ratio Difference], [ratio Last month])
```

On the right, the dashboard analyzes borrower types, including “First-time buyers”, “Remortgagors”, “Home movers”, and others. The layout mirrors the LTV section, featuring stacked area charts and data tables. Similar DAX logic is used to track borrower activity changes:

```DAX
borrower This month = CALCULATE(SUM(sales_by_type_of_borrower[number_of_sales]), DATEADD('Date'[Date], 0, MONTH))
borrower Last month  = CALCULATE(SUM(sales_by_type_of_borrower[number_of_sales]), DATEADD('Date'[Date], -1, MONTH))
borrower Difference  = [borrower This month] - [borrower Last month]
borrower M%M         = DIVIDE([borrower Difference], [borrower Last month])
```

This setup enables efficient month-over-month tracking of both lending risk exposure and borrower behavior, making it suitable for both operational insights and early trend detection.

#### 3. Regional and Product-Level Breakdown

The third page, “psd_mortgage_quarterly_data”, focuses on showing how mortgage sales vary across time, product type, and region.

At the top, there's a time series line chart that tracks total sales by quarter. It's mainly used to spot overall trends or sudden spikes in activity. Just next to it, a donut chart breaks down the share of Lifetime Mortgages compared to other types—making it easy to see which product dominates in the selected timeframe.

The bar chart on the right ranks regions based on total sales, with areas like the South West and London clearly standing out. This part is useful when you're trying to understand which regions drive the most activity or when comparing performance across different parts of the country.

On the left side, there's a flexible filter panel that lets you switch views based on different fields—like LTV bands, loan amounts, borrower age, credit history, sales channel, and more. This makes it easy to explore the data from different angles without having to touch the visual setup.


#### 4. Pure Protection Product Breakdown

The fourth page focuses on the pure protection segment—products like critical illness cover, income protection, and standalone critical illness.

At the top, there's a line chart showing overall sales trend over time, and a donut chart that splits the total into the main product categories. From this, it's easy to see how dominant critical illness products are compared to others.

What's unique here is the flexibility in drilling down: the filter panel on the left lets you regroup the data by things like whether the sale was advised or non-advised, who the provider was, payment structure (regular, single, or mixed), or product type. Depending on what’s selected, the visuals below update to show either a bar chart or a tree-style diagram that lays out the sales numbers by category and subcategory.

This layout makes it simple to compare how different selling approaches or product types perform over time, and where most of the volume comes from.

#### 5. Retail Investment Product Insights

The last page looks at retail investment sales across a range of financial products—things like pensions, ISAs, bonds, and other investment vehicles.

At the top, there's a familiar line chart showing overall trends over time, along with a donut chart that helps break down which product types contributed most to sales. Personal pensions clearly lead the pack, with ISAs and decumulation products also holding notable shares.

In the middle of the page, a tree diagram shows how each product category branches into different subcategories and payment types (e.g., regular vs single payments), helping illustrate how sales are distributed within each product family. To the right, bar charts provide a quick view of which subcategories are most popular—Self Invested Personal Pensions, for example, rank high.

Like the other pages, the left panel gives users a range of filters to regroup the data—whether by product type, provider status, or sector—so you can explore the numbers from whichever angle you need.




