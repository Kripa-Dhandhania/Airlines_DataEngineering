# ASG Airlines – End-to-End Data Engineering Case Study

## 1. Project Overview

This project implements an end-to-end data engineering pipeline for airline operational and transactional data.
The pipeline processes flight, booking, passenger and payment datasets through ingestion, data quality validation, cleansing, transformation, quarantine handling, analytical modelling and KPI aggregation.
The final analytical datasets are connected to Power BI to provide interactive insights into flight operations, airline performance, route performance, booking behaviour and data quality.

## 2. Objectives

The primary objectives of the project are:

- Ingest airline datasets from the provided source file.
- Establish a structured Bronze, Silver and Gold data architecture.
- Identify and handle missing, duplicate, invalid and inconsistent records.
- Detect temporal anomalies in flight timestamps.
- Isolate invalid records through a quarantine process.

## 3. Dataset

The source dataset is provided as an Excel workbook containing four datasets:
| Flights | Flight schedules, routes, airlines and timestamps |
| Bookings | Passenger booking and booking-status information |
| Passengers | Passenger demographic and contact information |
| Payments | Payment transactions associated with bookings |

## 4. Technology Stack

### Data Processing
- Python
- Pandas
- NumPy

### Storage
- CSV-based Bronze, Silver and Gold layers

### Analytics
- Power BI

### Development
- Jupyter Notebook
- Visual Studio Code

## 5. Architecture

The implemented architecture follows a Medallion-style approach.

Source Excel
    |
Bronze Layer
    |
Data Quality Validation
    |
Quarantine
    |
Silver Layer
    |
Gold Data Model
    |
KPI Aggregations
    |
Power BI

The Bronze layer preserves ingested source data.
The Silver layer contains cleaned and standardized datasets.
The Gold layer contains analytical fact and dimension tables together with KPI aggregations.
Records failing critical validation checks are isolated in the quarantine layer rather than silently deleted.

## 6. Data Pipeline

### Step 1 – Data Ingestion

The Excel workbook is read using Python and Pandas.

The four source datasets are extracted and stored in the Bronze layer:

- flights.csv
- bookings.csv
- passengers.csv
- payments.csv

The Bronze layer acts as the initial persisted representation of the source data.

### Step 2 – Data Quality Validation

Data quality validation is performed before analytical transformation.

The implemented checks cover:

- Missing values
- Duplicate identifiers
- Invalid categorical values
- Invalid passenger identifiers
- Invalid payment amounts
- Timestamp inconsistencies
- Duration inconsistencies
- Overnight flights
- Referential integrity
- Sensitive information handling

A total of 23 data-quality rules were implemented.

A DQ report is generated containing:

- Dataset
- Rule ID
- Severity
- Action
- Failed record count
- Description
- Validation timestamp

## 7. Quarantine Strategy

Records failing critical validation checks are not silently deleted.

## 8. Silver Layer Transformation

The Silver layer contains cleaned and standardized datasets.

### Flights

Transformations include:

- Standardizing column names
- Standardizing flight identifiers
- Standardizing airline and airport codes
- Handling missing airline values
- Parsing timestamps
- Calculating flight duration
- Identifying overnight flights

### Bookings

Transformations include:

- Standardizing identifiers
- Standardizing booking status
- Handling invalid status values
- Creating booking-status flags
- Identifying bookings associated with quarantined flights

### Passengers

Transformations include:

- Standardizing names
- Handling missing last names
- Validating Aadhaar identifiers
- Hashing Aadhaar values
- Hashing email values
- Masking phone numbers
- Creating age groups
- Removing raw sensitive fields from the analytical layer

### Payments

Transformations include:

- Converting payment amounts to numeric values
- Identifying invalid amounts
- Standardizing payment methods
- Creating payment validity/status indicators

## 9. Privacy and PII Protection

The passenger dataset contains personally identifiable information.

Sensitive fields are protected before the analytical layer.

The implemented approach includes:

- Aadhaar hashing using SHA-256
- Email hashing
- Phone number masking
- Removal of raw sensitive fields from the Silver analytical dataset

This ensures that sensitive information is not unnecessarily exposed in downstream analytical datasets.


## 10. Referential Integrity

The pipeline identified one booking, B1636, referencing quarantined flight SJ192.
The booking is retained because the booking itself is a valid transactional record.
However, the associated flight reference is marked as quarantined so that the booking does not incorrectly contribute to validated flight-operational metrics.

## 11. Gold Data Model

The Gold layer follows a dimensional modelling approach.

### Dimensions

- dim_airline
- dim_route
- dim_passenger
- dim_payment_method
- dim_date

### Fact Tables

- fact_flight
- fact_booking
- fact_payment

## 12. KPI Aggregation

The following analytical aggregation datasets are generated:

- flight_kpis.csv
- route_kpis.csv
- airline_kpis.csv
- booking_kpis.csv
- payment_kpis.csv
- payment_method_kpis.csv
- dq_kpis.csv

## 13. Power BI Dashboard

The Power BI report contains five analytical sections:

### Page 1 – Executive Operations

Provides a high-level overview of:

- Total flights
- Average flight duration
- Overnight flights
- Total bookings
- Total revenue
- Flights by airline
- Booking status distribution

### Page 2 – Flight Performance

Focuses on:

- Average flight duration
- Minimum and maximum duration
- Overnight operations
- Airline duration comparison
- Overnight percentage by airline

### Page 3 – Route Intelligence

Provides:

- Traffic by route
- Average duration by route
- Overnight flights by route
- Anomaly monitoring
- Route performance summary

### Page 4 – Airline Performance

Provides:

- Flight volume
- Airline market share
- Average duration
- Overnight percentage
- Airline comparison

### Page 5 – Data Quality & Anomaly Command Center

Provides visibility into:

- DQ violations
- DQ rules
- Quarantined flight records
- Temporal anomalies
- Referential integrity warnings
- Quarantined payment records

