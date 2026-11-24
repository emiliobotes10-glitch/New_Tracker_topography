# Drought Tracker (updated version)

This repository holds the updated version of my research project, made after the skripsie was submitted. The project is an application that lets a user anywhere in South Africa check whether their location is in a drought, using rainfall.

The application runs as a website at https://newtrackertopography-pxvnbzwjfswfrahrjcxzpx.streamlit.app

The Android version has not yet been updated with the changes below.

## What changed

The rainfall thresholds now take the shape of the land into account. In the submitted version, the thresholds of Smith (2023) were interpolated between rain gauges, so a town in a valley could get thresholds far above its own rainfall. The thresholds are now scaled with the SANBI (2021) mean annual rainfall map, which accounts for mountains and valleys.

When a drought is found, the application now also estimates how often a drought like it occurs, for example about once in 20 years. This uses the results of Smith (2023) for droughts lasting 1 to 24 months.

## What the application does

The user starts by choosing one of two options.

The first option is for users who have at least 12 months of rainfall data. They upload a daily rainfall file from the South African Weather Service (SAWS), or they fill in one of the two templates in this repository. The application checks how much of the data is missing. It then adds up the rainfall for each month and for each 12 month period. The latest 12 month total is compared with two rainfall thresholds for the user's location. The first threshold is the rainfall at SPI = 0, which is treated as normal. The second is the rainfall at SPI = -1, which is treated as the start of a drought.

The second option is for users who have no rainfall data. The application uses CHIRPS satellite rainfall for the user's location, which starts in January 1981. It fits a gamma distribution to the rainfall record and calculates SPI-12 for each month.

Both options give one of five results. Safe means the rainfall is normal or above normal. Dry Spell means the rainfall is below normal, but there has been no drought recently. Drought means the rainfall is below the drought threshold. Recovering means the rainfall has risen above the drought threshold after a drought, but is still below normal. Uncertain means the rainfall is below normal, but missing data or a short record makes it unclear which of these applies.

If the result is Drought or Recovering, the application also reports how many months the rainfall has been below normal, and how often a drought of that length and severity occurs.

## Missing data

A rainfall file is rejected if more than 15% of its days are missing. A warning is shown if between 10% and 15% are missing.

A month is treated as missing if it has 11 or more missing days, or 5 or more missing days in a row. If a month has fewer missing days than that, its total is scaled up to cover them. A final month that is not complete is left out.

## Files

| File | What it is |
|---|---|
| `combined_tracker_v1.py` | The website, including all the calculations |
| `threshold_spi12_normal_ratio-sanbi2021_idw-all-p2.tif` | The rainfall threshold for SPI = 0 |
| `threshold_spi12_drought_ratio-sanbi2021_idw-all-p2.tif` | The rainfall threshold for SPI = -1 |
| `sdf_station_table.csv` | The values used to estimate how often a drought occurs |
| `chirps_sa_monthly.nc` | CHIRPS monthly rainfall for South Africa from January 1981 |
| `chirps_sa_recent.nc` | CHIRPS months added after the file above was built. This file appears after the first update. |
| `update_chirps.py` | Adds new CHIRPS months when they are published |
| `Daily_Rainfall_Template.xlsx` and `Monthly_Rainfall_Template.xlsx` | Templates that users fill in with their own rainfall |
| `requirements.txt` | The Python packages the application needs |

## Keeping the CHIRPS rainfall up to date

A script checks every day whether a new month of CHIRPS rainfall has been published. When a new month is available, it is added to this repository. The website then uses it without any further work.

## Limitations

The application only works for locations inside South Africa. The thresholds from Smith (2023) are based on rainfall data up to 2021. Estimates of droughts rarer than about once in 50 years are uncertain. CHIRPS tends to overestimate low rainfall and underestimate high rainfall. The website can be slow to respond when it has not been used for a while.
