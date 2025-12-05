# Satellite Line-of-Sight (LoS) & Coverage Analysis Tool

Project Overview

This project is a Python-based computational simulation designed to model and predict satellite communication gaps ("shadows") in complex, mountainous terrain.

It was originally developed to aid Search and Rescue (SAR) teams by identifying areas where emergency communication devices (like iPhones with Emergency SOS, Garmin inReach, etc.) might fail due to terrain obstruction.

The tool fuses high-resolution Digital Elevation Models (DEMs) with real-time Two-Line Element (TLE) satellite orbital data to perform a high-density, parallelized line-of-sight analysis.

Key Features

Multi-Constellation Support: capable of analyzing Globalstar (Apple Emergency SOS), Starlink (Direct to Cell), Iridium (Garmin inReach), and Inmarsat.

Automatic DEM Processing: Automatically detects the coordinate system (projection) of any uploaded GeoTIFF DEM and converts it for global analysis.

Parallel Processing: Uses CPU multi-core processing (joblib) to perform millions of ray-casting calculations efficiently.

High-Fidelity Ray-Casting: "Walks" along the terrain profile for every single look-angle to determine true physical obstructions.

Dynamic Visualization: Outputs interactive HTML maps (Folium) and KML files (Google Earth) showing "Visible" vs. "Blocked" zones.

How It Works

1. Constellation Selection

At the start of the script, you are prompted to select the target satellite network. The system dynamically fetches the latest orbital data (TLEs) from CelesTrak based on your choice:

Globalstar: Fetches the operational LEO constellation used by Apple's iPhone 14/15/16.

Starlink (Direct to Cell): Filters for the newest V2 Mini satellites capable of LTE connections.

Starlink (Full): Loads the entire mega-constellation (~6,000+ sats) for future-state density analysis.

Iridium: Loads the Iridium NEXT constellation used by Garmin inReach.

Inmarsat: Loads the geostationary (GEO) fleet.

2. Automatic Georeferencing

The tool is designed to be "drag-and-drop" with DEM files.

Input: You provide a path to a .tif (GeoTIFF) file from OpenTopography or USGS.

Auto-Detection: The script uses rasterio and pyproj to read the file's internal metadata. It automatically detects if the file is in UTM (meters), State Plane, or WGS84.

Translation: It creates a coordinate transformer to seamlessly convert between the satellite's global position (Lat/Lon) and the terrain's local grid (Meters) without user intervention.

3. Configurable Density

The analysis is fully configurable to trade off between speed and resolution:

MESH_RESOLUTION: Controls the spatial density.

Set to 50 for a quick 50x50 grid test (2,500 points).

Set to 100 or higher for high-resolution field maps.

viz_step_minutes: Controls the temporal resolution.

Set to 10 minutes for a quick overview.

Set to 1 minute or 30 seconds to catch fast-moving LEO passes (critical for Starlink).

4. The Core Engine (Ray-Casting)

For every point on the ground and every satellite in the sky:

The script calculates the Azimuth and Elevation to the satellite.

It traces a line ("ray") from the observer towards the satellite.

It samples the terrain elevation at fixed steps along that line.

If the terrain height ever exceeds the ray's height, the line-of-sight is BLOCKED.

Installation & Setup

1. Environment

This project requires Python 3.10+ and several geospatial libraries. It is recommended to use Anaconda to manage dependencies (especially GDAL).

conda create --name sat-analysis python=3.10
conda activate sat-analysis
conda install -c conda-forge rasterio skyfield folium joblib tqdm numpy pyproj simplekml


2. Running the Analysis

Place your DEM file (e.g., mtBaldy.tif) in a known directory.

Open the Jupyter Notebook (satellite_los_analysis.ipynb).

Run the cells sequentially.

When prompted, enter the number for your desired network (e.g., 2 for Starlink).

The script will auto-detect your CPU cores and begin the parallel analysis.

3. Outputs

maps/coverage_map.html: Interactive web map. Open in any browser.

maps/coverage.kml: 3D overlay. Open in Google Earth Pro for the best visualization.

output/connectivity_detailed.csv: Raw data log of every connection window.

Credits

Author: Caleb Commisso
Institution: University of California, Irvine (ENGRMAE 199)
Advisor: Professor David Kirkby
