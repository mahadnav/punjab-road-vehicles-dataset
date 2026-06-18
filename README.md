# Punjab Road Vehicles Dataset (2015–2023)

District-level registered vehicle counts for Punjab, Pakistan, sourced from the Punjab Bureau of Statistics. Covers 36 districts, 9 administrative divisions, 8 vehicle types, and 9 years (2015–2023).

**Source:** Punjab Bureau of Statistics — [Registered Motor Vehicles, District-wise Annual Series]

---

## Files

```
data/
├── raw/
│   └── pbs_vehicles_raw.csv           # Original PBS data, 2015–2023
└── processed/
    ├── pbs_vehicles_cleaned.csv        # After applying 7 data cleaning rules
    ├── pbs_vehicles_classified.csv     # Mapped to 9 emission inventory classes
    └── cleaning_audit_log.csv          # Every changed cell (old value → new value)

clean_transport.py                      # Applies cleaning rules; produces Cleaned data
classify_vehicles.py                    # Maps PBS types to vehicle classes; produces Classified data
```

---

## Columns

### Raw & Cleaned (`pbs_vehicles_raw.csv`, `pbs_vehicles_cleaned.csv`)

| Column | Description |
|---|---|
| Year | Calendar year (2015–2023) |
| Province | Province name (Punjab) |
| Division | Administrative division |
| District | District name |
| VehicleType | PBS vehicle category (see table below) |
| Count | Number of registered vehicles |

### Classified (`pbs_vehicles_classified.csv`)

Same as above plus:

| Column | Description |
|---|---|
| Vehicle_Class | Emission inventory class code (see table below) |

### Audit Log (`cleaning_audit_log.csv`)

| Column | Description |
|---|---|
| Year | Year of the corrected record |
| Province / Division / District / VehicleType | Record identifiers |
| Raw_Count | Original PBS value (blank = row was inserted) |
| Cleaned_Count | Corrected value after applying cleaning rule |
| Change_Type | `corrected` or `inserted (gap-fill)` |

---

## Vehicle Type Mapping

| PBS Category | Vehicle Class | Description |
|---|---|---|
| Motor Cycles/Scooters | 2W | Two-wheelers |
| Auto Rickshaws | 3W | Three-wheelers |
| Motor Cars/Jeeps/Station Wagons (85%) | 4W1 | Petrol passenger cars |
| Motor Cars/Jeeps/Station Wagons (15%) | 4W2 | Diesel cars / SUVs |
| Taxis | 4WT | Taxis |
| Pickups/Delivery Vans | LCV | Light commercial vehicles |
| Trucks | HDV | Heavy duty vehicles |
| Mini Buses/Buses/Flying/Luxury Coaches | BUS | Buses |
| Tractors | NRV | Non-road vehicles |
| Other Vehicles | — | Excluded |

---

## Data Cleaning Rules

Seven rules correct systematic artefacts in the raw PBS data. All corrections use linear interpolation between the nearest unflagged flanking years.

| Rule | Trigger | Year(s) | Types Affected |
|---|---|---|---|
| Fix 1 | v(2018) = v(2017) and v(2017) > 0 | 2018 | Rickshaws, Motorcycles, Pickups, Taxis, Tractors, Trucks |
| Fix 2 | 2022 row absent | 2022 | All |
| Fix 3 | v(2015) > 1.5 × v̄(2014, 2016) | 2015 | Trucks |
| Fix 4 | v(2017) > 1.5 × v̄(2016, 2018) | 2017 | Buses |
| Fix 5 | v(2017)/v(2016) < 0.75 and v(2018) = v(2017) | 2017–18 | Tractors |
| Fix 6 | v(2017) < 0.5 × v(2016) | 2017 | Other Vehicles |
| Fix 7 | v(2015) < 0.8 × v̄(2014, 2016) | 2015 | Pickups |

See `clean_transport.py` for full implementation.

---

## Coverage

- **Province:** Punjab
- **Districts:** 36
- **Years:** 2015–2023
- **Vehicle types:** 8 PBS categories → 9 vehicle classes
- **Raw rows:** 2,591
- **Cleaned rows:** 2,915 (includes 324 inserted 2022 gap-fill rows)

---

## Citation

> Punjab Bureau of Statistics (PBS). *Registered Motor Vehicles, District-wise, Annual Series 2015–2023.* Statistical Pocket Book. bos.punjab.gov.pk/statistical_pocket_book

---

## License

Data is derived from publicly available PBS statistics. Code is released under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/).
