# The 2014 Carlton Complex Fire - An Ownership Analysis
## Introduction
This GIS project was completed as an assigment in course #4 of the [UC Davis/Coursera GIS Certificate](https://cpe.ucdavis.edu/section/geographic-information-systems-gis-specialization-coursera) program, [Imagery, Automation, and Applications](https://www.coursera.org/learn/gis-applications?specialization=gis).  The goal of the project was to demonstrate fundamental ArcGIS skills in the areas of remote sensing analysis, classification, dataset correlation, and building and executing automated workflows.

## Summary
The Methow Valley, situated in the north-central portion of Washington State, is a picturesque rural area, supported largely by agricultural, recreational, and tribal industries.  With a climate classification of dry-summer humid continental, the region only receives 12 inches of rain per year, far less than the Washington State average of 38 inches per year.   Situated on the drier side of the Cascade Mountains,  it is one of the more wildfire-prone regions of the state.

From July 14 to August 24, 2014, one of the largest fires in Washington State history ripped through the area, leaving over a quarter million acres burned and hundreds of homes, businesses, farms, and ranches destroyed.   Two people were killed.

This project aims to identify the final destructive footprint of the fire and analyze the ownership of the land affected by the fire.  A project like this might be used for future fires to assist with coordination, cleanup, restoration, and prevention.  The project is designed to allow for any Landsat 8 imagary and is runtime-configurable so future Washington State fires can be analyzed.

## Methodology
Using Landsat 8 imagery acquired shortly after the fire, ArcMap supervised classification is used to identify the final footprint of the fire, as well as surrounding non-affected land and its associated type.   An automated workflow joins land ownership feature classes from the Washington State Department of Natural Resources in order to identify and quantify ownership of the land affected by the fire.

All processing steps are placed inside an automated workflowk, which allows for any Washington State wildfire to be analyzed using this tool, utilizing user-modifiable runtime parameters.

### Data Sources
Land ownership datasets are retrieved from the [Washington State DNR](https://data-wadnr.opendata.arcgis.com/).  Additionally, a simple Washington State boundary is used to constrain analysis of the Landsat 8 imagery.  All datasets are available as linked below.
* [WA DNR Managed Land Parcels](https://data-wadnr.opendata.arcgis.com/documents/wadnr::wa-dnr-managed-land-parcels-download/about) feature class
* [WA Major Public Lands (non-DNR)](https://data-wadnr.opendata.arcgis.com/documents/wadnr::wa-major-public-lands-non-dnr-download/about) feature class
* [Washington State Boundary](https://data-wadnr.opendata.arcgis.com/documents/wadnr::wa-state-boundary-download/about) feature class

A [Landsat 8](https://earthexplorer.usgs.gov/) 45/26 pass on 7/31/2014 provided a cloud free image of the freshly charred organic material.   This date is near the date in which the wildfire was fully contained, and proves to be the best possible Landsat 8 candidate image for the fire footprint analysis.

For convenience purposes, these three feature class datasets and the Landsat 8 image are checked into this git repository, although they are also available at the links above.

### Data Preparation
#### Parcel Ownership Feature Classes
1. As an optimization, a feature class was manually digitized to the general region of the fire. This is simply a one row feature class with a polygon feature used to constrain the area of classification analysis so the entire Landsat 8 raster need not be processed and other non-Carlton fires are not included in the classification.
2. Using the merge tool, a new feature class ‘government lands’ was created by merging the two parcel ownership feature classes.   The field calc.ulator was then used to statically populate the OWNER and MANAGER fields of this feature class for all DNR records.
3. The erase tool was used to generate a new feature class ‘private lands’ – all land which is within Washington state that is not part of the government_lands feature class.
4. Finally, the merge tool was used again to merge the government_lands and private_lands feature classes into an all_wa_lands feature class that contains land ownership information for the entire state in a standardized format.  Again the field calculator was used to statically populate ‘Private’ in the OWNER and MANAGER fields of the new private land records.  This is a reusable feature class which can be used for all future Washington State fires.

For reference, these feature classes generated in the data prepraration phase are included in this git repository.

#### Burn Scar Raster Band Mapping
Charred organic material has relatively low reflictivity on the visible spectrum, but rather high reflectivity in the short-wave infrared.  Additionally, healthy vegetation has a rather high reflectivity in the near infrared.  Remapping Landsat 8 bands as follows will result in the burn scar showing up as a highly distinct magenta, and healthy vegegation a distinct green, which will greatly assist in raster classification:

| Landsat 8 Band  | Mapped Color |
| ------------- | ------------- |
| 7 / SWIR 2 | Red  |
| 5 / NIR | Green  |
| 4 / Red | Blue  |

The output of this remapping, used by the supervised classification:

![Mapped Bands](./mapped_bands.png)








