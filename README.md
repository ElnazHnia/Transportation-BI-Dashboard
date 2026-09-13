# Freight Peak Travel Times Dashboard 🚚📊

An interactive end-to-end Power BI analytics solution engineered to analyze freight dispatch behavior, optimize warehouse loading schedules, and identify bottleneck periods across UK regional hubs, destination cities, and transport lanes.

![Dashboard Preview](Screenshot/dashboard-screenshot.jpg)

## Executive Summary & Business Value

In freight logistics and supply chain operations, dispatch volumes fluctuate significantly throughout the day. Concentrated dispatch windows lead to warehouse staging congestion, driver idle times, and delayed linehaul movements. 

This dashboard provides dispatch coordinators and network planners with:
* Granular Visibility: 24-hour heatmaps evaluating dispatch volumes down to specific hourly slots across days of the week and geographic routes.
* Benchmark Analytics: Dual percentage measures (Selected% vs All%) contrasting regional subset behavior against global network baselines to isolate localized operational anomalies.
* Capacity Staging Optimization: 3-hour KPI blocks indicating high-volume windows that exceed operational thresholds (>15% of total daily volume).

## Key Features & Visual Architecture

1. Synchronized 24-Hour Heatmap Grid:
   * Aligned matrix visuals and top column bar charts providing synchronized vertical column alignment for hours 0 through 23.
   * Conditional background formatting applying dark-to-light gradient fills based on hourly dispatch counts.
2. Dynamic KPI Time Block Cards:
   * 8 discrete 3-hour aggregated volume cards (12am - 3am, 3am - 6am, etc.) showing raw dispatch counts alongside dual percentage calculations.
3. Context-Aware Visual Titles:
   * Dynamic heading labels leveraging ISFILTERED logic to automatically state active filter states directly within visual subtitles.
4. Interactive Route & Region Drill-Down:
   * Multi-level matrix hierarchy supporting seamless drill-down from Region -> City -> Lane Route.

## Data Model & DAX Architecture

The semantic model follows a Star Schema centered around fact_shipments, optimized for performant filtering across multidimensional slices.

[dim_location] ---> [fact_shipments] <--- [dim_services]
[dim_lane] ------> [fact_shipments] <--- [dim_customer]

### Advanced DAX Logic

1. Dynamic Subset Distribution (Selected%)
Calculates the proportion of dispatches occurring in a specific 3-hour block relative to the currently filtered dataset (e.g., London dispatches), while clearing card-level time block filters:

Selected% = 
VAR CurrentBlockDispatches = [# Dispatches]
VAR TotalSelectedDispatches = 
    CALCULATE(
        [# Dispatches], 
        REMOVEFILTERS(fact_shipments[TimeBlock_3HR]),
        REMOVEFILTERS(fact_shipments[TimeBlock_Sort])
    )
RETURN
DIVIDE(CurrentBlockDispatches, TotalSelectedDispatches, 0)

2. Network Benchmark Baseline (All%)
Computes the static network-wide baseline for each 3-hour time block across the entire historical dataset, ignoring external page slicers using ALLEXCEPT:

All% = 
VAR CurrentBlockBaseline = 
    CALCULATE(
        [# Dispatches], 
        ALLEXCEPT(
            fact_shipments, 
            fact_shipments[TimeBlock_3HR], 
            fact_shipments[TimeBlock_Sort]
        )
    )
VAR TotalDatasetDispatches = 
    CALCULATE(
        [# Dispatches], 
        REMOVEFILTERS(fact_shipments),
        REMOVEFILTERS(dim_lane),
        REMOVEFILTERS(dim_location),
        REMOVEFILTERS(dim_customer),
        REMOVEFILTERS(dim_services)
    )
RETURN
DIVIDE(CurrentBlockBaseline, TotalDatasetDispatches, 0)

3. Chronological Day-of-Week Sorting
To prevent circular dependency errors when sorting matrix rows logically (Monday through Sunday):

DayOfWeekSort = WEEKDAY(fact_shipments[DepartureDate], 2)

## Key Operational Insights

* Morning Peak Dispatch Window: A severe operational surge occurs between 6:00 AM and 8:00 AM, generating 32.25% of overall daily dispatch volume.
* Secondary Afternoon Surge: A second peak period occurs between 4:00 PM and 5:00 PM, accounting for 23.77% of daily departures.
* Regional Anomalies (Greater London): When filtering specifically for Greater London, early-morning dispatch volume (12am - 3am) rises to 3.14% compared to the national average of 2.89%, indicating elevated overnight processing demand.

## Data Dictionary & Star Schema

Table Name: fact_shipments | Entity Type: Fact | Key Attributes: ShipmentID, DepartureDate, DepartureHour, TimeBlock_3HR | Description: Central transactional log of freight shipments
Table Name: dim_location | Entity Type: Dimension | Key Attributes: LocationID, City, Region, Country | Description: Geographic origin/destination metadata
Table Name: dim_lane | Entity Type: Dimension | Key Attributes: LaneID, LaneName, OriginID, DestinationID | Description: Transport route corridors
Table Name: dim_services | Entity Type: Dimension | Key Attributes: ServiceID, ServiceTier, TransportMode | Description: Service-level agreements and transit modes
Table Name: dim_customer | Entity Type: Dimension | Key Attributes: CustomerID, CustomerName, AccountType | Description: B2B shipper client metadata

## How to View & Run the Dashboard

1. Clone the Repository: git clone https://github.com/ElnazHnia/Transportation-BI-Dashboard.git
2. Prerequisites: Install Power BI Desktop.
3. Open the File: Open Dashboard/Transportation-BI-Dashboard.pbix.
4. Data Source Mapping: If prompted for missing paths, redirect Power BI data source settings to the CSV files inside the Data Source/ directory.
