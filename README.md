# D4D Senegal datasets

This repository contains Senegal communication, mobility, and spatial datasets from the Data for Development (D4D) project.

## Dataset overview

- **Set1**: district-to-district SMS and voice communication for 123 districts.
  - Includes total, hourly, weekday, weekly, and monthly aggregations.
  - Temporal files contain `time_bucket, source_district, target_district, value`.
  - Total files contain `source_district, target_district, value`.
- **Set2**: indicators for 1,666 mobile-phone tower areas.
  - Includes contacts, interactions, call duration, mobility radius, at-home activity, nocturnal activity, and text response rate.
  - `arrond01`-`arrond25` are 25 measurement windows averaged by `average.py`.
  - No timestamps are supplied, so these windows cannot be confirmed as hourly, daily, weekly, or monthly.
- **Set3**: aggregated indicators for 123 arrondissements, including communication and mobility behavior.

An **arrondissement** is a local administrative area, similar to a district or sub-district.

## Spatio-temporal extent

### Spatial extent

The supplied geometries use longitude/latitude coordinates in decimal degrees:

- Senegal outline: approximately **11.36 W to 17.54 W** and **12.31 N to 16.69 N**.
- Set2 tower locations: approximately **11.37 W to 17.53 W** and **12.36 N to 16.65 N**.
- Set1 and Set3 cover the same Senegal-wide district/arrondissement geography.
- Spatial units are districts, arrondissements, and tower Voronoi service areas; they are not individual GPS trajectories.

### Temporal extent

The source files do not provide a clear overall start and end date. The available temporal resolution is:

- **Set1 hourly:** hour buckets `0-23`.
- **Set1 weekday:** weekday buckets `1-7`.
- **Set1 weekly:** week buckets `1-52`.
- **Set1 monthly:** month buckets `1-12`.
- **Set1 total files:** all observations combined, with no time bucket.
- **Set2:** 25 repeated measurement windows (`arrond01`-`arrond25`), with no timestamps or documented cadence. The combined file is their per-tower average.
- **Set3:** one aggregated indicator table with no time index; its observation window is not documented in the supplied files.

These are temporal aggregates or recurring calendar buckets, not a documented longitudinal time series. Set1 week aggregation uses week number without an explicit year, so different years could be combined if present in the source.

## Hadoop processing

The Hadoop/Pig jobs in `hadoop/` show how Set1 was generated:

1. Load timestamped tower-level SMS or voice records.
2. Extract hour, ISO weekday, month, and calendar week.
3. Aggregate communication by time period and tower pair.
4. Convert tower IDs to district IDs with Spark scripts.
5. Sum duplicate district-to-district pairs.

Set3 Hadoop processing tracks user movements between arrondissements after sorting events by time and removing repeated locations.

## Visualization notebook

Run [descriptive_sets_summary.ipynb](descriptive_sets_summary.ipynb) to:

- Inspect dimensions and data quality.
- Plot Senegal, district, arrondissement, and all tower locations.
- Map Set1 text and voice connectivity.
- Plot Set1 hourly, weekday, weekly, and monthly profiles.
- Map Set2 and Set3 indicators.
- View rankings, correlations, and Set2 coverage by measurement window.

## Important caveats

- Indicator units and exact observation windows are not fully documented in the supplied files.
- Set2 raw files contain different numbers of observed towers; missing values should be checked.
- Set2 map scripts use different delimiters: the combined file is comma-separated, while raw files are tab-separated.
- Tower polygons are Voronoi service areas, not individual user GPS tracks.
- Set1 week aggregation uses week number without an explicit year in the Pig code.
