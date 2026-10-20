# SwiftLog Logistics: Supply Chain Analytics and Business Intelligence

AnalystLab Africa Experience Lab, Week 5, Data Analytics track.
Author: Yawa Jacqueline GBEDEVI, Junior Data Analyst.

## What this project is

SwiftLog Logistics Limited is a simulated Nigerian logistics company with delayed deliveries, inventory imbalances and little visibility into its key metrics. This project turns 11 raw datasets into a five-page Power BI dashboard, a set of DAX measures, an executive report and a presentation for management.

Data covers 1 January to 8 September 2026 (5,000 orders, 1,000 customers, 250 products, 150 suppliers, 15 warehouses in Abuja, Kano and Lagos).

## Headline findings

- Only 7.11M of 21.67M in order value was realised through delivered orders. 32.5% of orders were cancelled and 34.3% are still pending.
- 63.7% of shipments were delayed (3 days or more), with an average delay of 3.57 days. Late delivery is the most frequent return reason.
- Demand is steady (577 to 654 orders a month) and spread across customers, products and cities.
- Stock is large (about 1.0M units) and turns slowly, yet 380 stock records fall under the low-stock threshold.
- Supplier performance could not be measured because no table links suppliers to products or shipments.

## Repository structure

```
Week5_DataAnalytics_SwiftLog_JacquelineGbedevi/
├── Data/
│   └── Original datasets (11 CSV files and the data dictionary)
├── Dashboard/
│   ├── SwiftLog_Analytics.pbix
│   ├── SwiftLog_KPI_Measures.dax
│   └── SwiftLog_Theme.json
├── Report/
│   ├── Executive_BI_Report.pdf
│   └── Dashboard_Screenshots.pdf
├── Presentation/
│   └── Executive_Presentation.pptx
├── Images/
│   └── Data model and dashboard screenshots
├── README.md
└── References/
```

## How to open the dashboard

1. Open `Dashboard/SwiftLog_Analytics.pbix` in Power BI Desktop.
2. If Power BI asks for the data source path, point it to the `Data/` folder.
3. Use the navigation buttons on the left of each page. The slicers (month, order status, product category, warehouse location) are synchronised across pages.

`SwiftLog_KPI_Measures.dax` contains every measure and can be pasted into the DAX query view. It ends with a control query that returns the expected totals. `SwiftLog_Theme.json` is the colour theme.

## Dashboard pages

| Page | Content |
| --- | --- |
| Executive Dashboard | KPI cards, revenue trend, monthly orders, delivery status, returns by reason |
| Shipment and Delivery | On-time rate, average delay, delayed shipments by warehouse and by location |
| Customer Analytics | Top customers, revenue by city, order frequency, active customers per month |
| Inventory and Warehouse | Stock level, turnover, low stock, warehouse comparison |
| Supplier and Product | Revenue by category and product, best-selling products, returned products |

## Data model

Star schema built in Power BI. Facts and dimensions are linked as follows (one-to-many, single direction).

| One side | Many side | Key |
| --- | --- | --- |
| Customers | Orders | CustomerID |
| Calendar | Orders | Date to OrderDate |
| Orders | OrderItems | OrderID |
| Products | OrderItems | ProductID |
| Orders | Shipments | OrderID |
| Warehouses | Shipments | WarehouseID |
| Shipments | Delivery_Performance | ShipmentID |
| Orders | Returns | OrderID |
| Products | Inventory | ProductID |
| Warehouses | Inventory | WarehouseID |
| Warehouses | Employees | WarehouseID |

The Suppliers table is kept in the model but not related to any other table, because the source data has no supplier key.

## Business rules

| Rule | Definition |
| --- | --- |
| Realised revenue | Quantity multiplied by unit price, for orders with status Delivered |
| Delayed shipment | 3 or more days of delay. On time means 0 to 2 days, matching the Status field of the delivery performance table |
| Return rate | Total returns divided by total orders |
| Inventory | Each of the 4,000 inventory records is a separate stock record and is summed |
| Region | Location of the warehouse the shipment left from |
| Delivery time | Not available, so delay days are used as a proxy |
| Warehouse utilisation | Proxy only (stock compared with the largest warehouse), because capacity data does not exist |

## Data preparation (Power Query)

- Imported the 11 datasets, checked types, primary keys and foreign keys.
- Converted `OrderDate` from text (format m/d/yyyy) to date, and `UnitPrice` from text to decimal.
- Standardised text columns (trim and consistent casing).
- Built a `Calendar` table (date, year, quarter, month, month number, year-month).
- Added `LineTotal` (quantity multiplied by unit price) to the order items, `DeliveryStatus` to the shipments, and a record key to the inventory.
- Disabled loading for helper queries used only for checks.

## Data quality log

| Finding | Size | Treatment |
| --- | --- | --- |
| Shipments exist for orders that were cancelled or are pending | 3,342 of 5,000 | Kept, order status slicer on every page |
| Returns recorded on orders that were never delivered | 335 of 500 | Kept, counted by a dedicated measure |
| Inventory repeats the same warehouse and product pair | 4,000 records for 2,455 distinct pairs | Kept as separate records |
| Delivery_Performance covers only part of the shipments | 1,000 of 5,000 | Same rule applied to all shipments through DelayDays |
| No supplier key in products or shipments | 150 suppliers | Supplier performance not measured |

## Limitations

- September is incomplete because the data stops on 8 September.
- The inconsistencies between order status, shipments and returns point to a recording problem that should be fixed at the source.
- Several indicators are proxies (delay days, warehouse utilisation, units-based turnover).
- The currency is not specified in the source data.
- Product-level return counts credit a return to every product on the returned order, so they are for comparison only.

## Tools

Power BI Desktop (Power Query, DAX, report design), Microsoft PowerPoint for the presentation.


## Acknowledgements

Project brief and datasets provided by AnalystLab Africa.
