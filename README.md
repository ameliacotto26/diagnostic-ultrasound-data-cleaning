# Diagnostic Ultrasound Device Events and Recalls

## Project Overview

For this project, I cleaned and combined diagnostic ultrasound device data from multiple government sources. The goal was to take data that was originally nested, inconsistent, and incomplete and turn it into datasets that could be more easily analyzed and visualized.

I focused on three diagnostic ultrasound product codes:

- IYN - Pulsed Doppler Ultrasound System
- IYO - Pulsed Echo Ultrasound System
- ITX - Diagnostic Ultrasound Transducer

For the main analysis, I focused on 2025 data so the project would stay manageable while still including enough records to compare the different device types.

## Data Sources

I used three different sources:

1. FDA MAUDE adverse event reports  
   This data included reports of device malfunctions, injuries, deaths, and other events.

2. FDA Medical Device Recall data  
   This included recall information such as the recalling firm, product description, recall reason, root cause, and recall date.

3. AccessGUDID  
   I used UDI values from the MAUDE data to look up additional device information such as brand, model, and company name.

The data was accessed through APIs and returned mainly as JSON rather than as already-clean CSV files.

## Why the Data Was Messy

The original data required several cleaning steps because:

- Some fields were nested inside lists or data frames.
- There were missing values represented in different ways.
- Some fields contained blank strings or values such as "N/A".
- Product problem labels could be repeated within the same report.
- Manufacturer and recalling firm names were not always written consistently.
- Dates were stored as text instead of date variables.
- Recall descriptions contained extra spaces and line breaks.
- The three sources had different structures and variable names.
- Some UDI values did not return a matching AccessGUDID record.

## Cleaning Process

### MAUDE Data

I downloaded 1,688 MAUDE reports for the three ultrasound product codes.

I extracted device information from the nested device field, including:

- Brand name
- Manufacturer
- Model number
- Product code
- UDI

I converted blank strings and "N/A" values to missing values so missing data would be represented consistently.

The MAUDE data contained:

- 222 missing brand names
- 1 missing manufacturer
- 724 missing model numbers
- 758 missing UDI values

I also removed repeated product problem labels within individual reports and created a separate table where each report/problem combination could be stored as its own row.

The event dates and received dates were converted into actual date variables.

Manufacturer names were only combined when the differences were clearly formatting or spelling variations. I avoided combining company names when I could not be sure they represented the same legal entity.

### Recall Data

The FDA recall dataset contained 530 ultrasound recall records across the three product codes.

I converted the recall initiation dates into date variables and cleaned the long text fields by removing extra whitespace and line breaks.

I also standardized obvious variations in company names, including different versions of Philips Ultrasound and CIVCO Medical Instruments.

For the final comparison, I limited the recall data to recalls initiated in 2025 so the recall and MAUDE datasets represented the same time period. This resulted in 119 recall records.

### AccessGUDID Data

Out of the 1,688 MAUDE reports, 930 contained a UDI, representing 123 unique UDI values.

Because many reports shared the same UDI, I focused on the most common UDI values rather than sending an API request for every individual report.

AccessGUDID information was used to add device brand, model, and company information where a match was available. Missing matches were left as missing values instead of forcing an incorrect match.

## Final Results

After cleaning the data, I created common device type labels so the MAUDE and recall datasets could be compared.

For 2025:

| Device Type | MAUDE Reports | Recalls |
| --- | ---: | ---: |
| Diagnostic Ultrasound Transducer | 575 | 38 |
| Pulsed Doppler Ultrasound System | 621 | 81 |
| Pulsed Echo Ultrasound System | 492 | 0 |

Most MAUDE reports were classified as malfunctions. The recall data also showed differences in root causes between device types. Device Design was the most common recall root cause for Pulsed Doppler systems, while Process Control was the most common for Diagnostic Ultrasound Transducers.

## Final Cleaned Files

The project creates two main analysis-ready CSV files:

- `maude_ultrasound_2025_final.csv`
- `fda_ultrasound_recalls_2025_final.csv`

Additional RDS files are also saved during the cleaning process to preserve raw and intermediate versions of the data.

## Final Thoughts

This project showed how much work can be required before real-world data is ready for analysis. The three sources had different structures, nested fields, missing values, inconsistent naming, and repeated information. After cleaning and standardizing the data, I was able to compare ultrasound adverse event reports and recalls in a much clearer way.