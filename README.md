# Identifying Coastal Upwelling from Environmental Variables

This is my capstone project for the Data Science Immersive program from General Assembly. 

---

### Problem Statement

For this project, I'm building a model to identify periods of coastal upwelling off the coast of Oregon using data collected by the Ocean Observatories Initiatve (OOI). I intend to use environmental variables, such as seawater temperature, salinity, and dissolved oxygen, as features in classification models, and I'll be labeling my target variable using the CUTI upwelling index. The OOI has several instrument packages off the Washington and Oregon coasts; for this project, I'll be focusing on the Oregon Offshore location, located offshore from Newport, Oregon, around 44 degrees North. The instrument packages found here include a surface mooring that has a bulk meteorology package, a shallow profiler that collects data in the upper ~200 meters of the water column, a stationary platform located at a depth of 200 meters, and a deep profiler that collects data in the lower portion of the water column.

---

### Notebooks in this repo

[01_data_collection](https://github.com/dgumustel/coastal-upwelling/blob/main/notebooks/01_data_collection.ipynb) - This notebook requests and pulls data from the OOI via their API. It's currently set up to download data from the surface mooring, shallow profiler, and 200 meter platform in 2017 and 2018 at the Oregon Offshore location. It will create a new directory called `coastal_upwelling_output` parallel to this repository where it will save these datasets as `.csv`s. 

[02_data_analysis_2017](https://github.com/dgumustel/coastal-upwelling/blob/main/notebooks/02_data_analysis_2017.ipynb) - This notebook contains exploratory data analysis for the 2017 data from the surface mooring, shallow profiler, and 200 meter platform. This includes creating plots to investigate data availability, sensor depths, and changes in environmental variables over time. This notebook requires the user to manually download the CUTI upwelling index data and place it into the `coastal_upwelling_output` directory - see detailed instructions in notebook.

[03_data_analysis_2018](https://github.com/dgumustel/coastal-upwelling/blob/main/notebooks/03_data_analysis_2018.ipynb) - This notebook is identical to the 02_data_anaysis_2017 notebook, but investigates the data from 2018. 

[04_preprocessing](https://github.com/dgumustel/coastal-upwelling/blob/main/notebooks/04_preprocessing.ipynb) - This notebook reads in the 2017 METBK data, 2017 platform data, and CUTI upwelling index data and merges them into one dataframe along time. It then saves the resulting dataframe for modeling. 

[05_modeling](https://github.com/dgumustel/coastal-upwelling/blob/main/notebooks/05_modeling.ipynb) - This notebook contains logistic regression and decision tree models trained on the data saved at the end of notebook 04. This notebook also contains model evaluations and comparisons, and a brief investigation into the mislcassified data.

[T-S_plots](https://github.com/dgumustel/coastal-upwelling/blob/main/notebooks/T-S_plots.ipynb) - This notebook contains temperature-salinity plots made using the 2018 profiler data. This notebook is the start of a stretch goal to use unsupervised clustering methods on T-S plots.

[misc_code](https://github.com/dgumustel/coastal-upwelling/blob/main/notebooks/misc_code.ipynb) - This notebook contains miscellaneous snippets of code that I've written but don't currently need that I'd like to hold onto for potential use later. 

---

### Data

For the first round of logistic regression and decision tree classifiers, I used data from the Oregon Offshore surface mooring and 200 meter platform, from January 1st to September 15th, 2017. 

| Variable                | Description                                                                               |
|-------------------------|-------------------------------------------------------------------------------------------|
| time                    | Time of measurement collection, contains dates and times from the year 2017               |
| practical salinity      | Seawater practical salinity at a depth of 200 meters, collected by the 200 meter platform |
| seawater temperature    | Seawater temperature at a depth of 200 meters, collected by the 200 meter platform        |
| sea surface temperature | Seawater temperature at the sea surface, collected by the surface mooring METBK package   |
| CUTI                    | Upwelling index value, imported from the ERD, positive values indicate upwelling and negative values indicate downwelling (?) |
| upwelling               | One-hot encoded version of the CUTI data: 0 for CUTI <= 0 and 1 for CUTI > 0              |

---

### Resources

The following links are included for myself and others.

[Learn how to do data requests with the OOI API](https://datalab.marine.rutgers.edu/2020/05/data-requests-the-easy-way-with-the-ooi-api/#top)

[Notesbooks for profiler downsampling](https://github.com/ooi-data-review/2018-data-workshops/tree/master/chemistry/examples/extras2/Profiler_Downsampling)

[Data aggregation and resampling](https://github.com/ooi-data-review/2018-data-workshops/blob/master/chemistry/examples/extras1/Aggregation_and_Resampling.ipynb)

[OOI FAQs](https://oceanobservatories.org/faqs-knowledgebase/)

[OOI data explorations](https://datalab.marine.rutgers.edu/data-explorations/)

[Interpreting QC variables and results](https://oceanobservatories.org/knowledgebase/interpreting-qc-variables-and-results/)

[Notebook for deciphering OOI data quality flags](https://github.com/ooi-data-review/2018-data-workshops/blob/master/chemistry/examples/extras2/Deciphering_OOI_Data_Quality_Flags.ipynb)

[Article about time series classification problems](https://towardsdatascience.com/time-series-classification-with-deep-learning-d238f0147d6f)

[Ethical machine learning resources](https://github.com/EthicalML/awesome-production-machine-learning)

[GitHub project boards](https://docs.github.com/en/github/managing-your-work-on-github/about-project-boards#templates-for-project-boards)

[Upwelling indices](https://oceanview.pfeg.noaa.gov/products/upwelling/intro)

[Explore the CUTI index](https://heatherwelch.shinyapps.io/shiny_jacox_cuti/)
