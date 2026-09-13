# Freight Peak Travel Times Dashboard 🚚📊

An interactive Power BI analytics dashboard built to analyse peak freight dispatch times, hourly departure trends, and regional capacity across time blocks, regional hubs, and destination cities.

![Dashboard Preview](Screenshot/dashboard-screenshot.jpg)


## 🗂️ Data Dictionary & Star Schema

* **`fact_shipments`**: Main transactional table containing `ShipmentID`, `DepartureDate`, `DepartureHour`, `TimeBlock_3HR`, `TimeBlock_Sort`, and dispatch metrics.
* **`dim_location`**: Geolocation hierarchy (`Region`, `Country`, `City`, `LocationID`).
* **`dim_services`**: Service levels and transport modes (`ServiceTier`, `TransportMode`).
* **`dim_lane`**: Origin and destination route identifiers (`LaneName`, `OriginLocationID`, `DestinationLocationID`).


## ⚙️ How to View & Run the Dashboard

1. Clone or download this repository.
2. Ensure you have **Power BI Desktop** installed (latest version recommended).
3. Open `Dashboard/Transportation-BI-Dashboard.pbix`.
4. If prompted to update data source paths, point the file parameters to the CSV files inside the `Data Source/` folder.
