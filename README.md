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

### Data and Analysis

For the first round of logistic regression and decision tree classifiers, I used data from the Oregon Offshore surface mooring and 200 meter platform, from January 1st to September 15th, 2017. 

I gathered my data using the OOI API and the [OOI Data Access & Visualization page](https://ooinet.oceanobservatories.org/data_access/#CE)

<br/>

Data sources: 

| Node                     | Instrument | Reference designator        | Method         | Stream                           |
|--------------------------|------------|-----------------------------|----------------|----------------------------------|
| Surface Mooring          | METBK      | CE04OSSM-SBD11-06-METBKA000 | recovered_host | metbk_a_dcl_instrument_recovered |
| Shallow Profiler (SF01B) | CTD        | CE04OSPS-PC01B-4A-CTDPFA109 | streamed       | ctdpf_sbe43_sample               |
| 200m Platform (PC01B)    | CTD        | CE04OSPS-PC01B-4A-CTDPFA109 | streamed       | ctdpf_optode_sample              |

<br/>

Variables used in final models:

| Variable                |Units|Source|Description                                                                               |
|-------------------------|---|---|------------------------------------------------------------------------------------------|
| time                    | |Every instrument| Time of measurement collection, contains dates and times from the year 2017               |
| practical salinity      | |200m Platform (PC01B)| Seawater practical salinity at a depth of 200 meters, collected by the 200 meter platform |
| seawater temperature    | |200m Platform (PC01B)| Seawater temperature at a depth of 200 meters, collected by the 200 meter platform        |
| sea surface temperature | |Shallow Profiler (SF01B)| Seawater temperature at the sea surface, collected by the surface mooring METBK package   |
| CUTI                    | |[ERD](https://oceanview.pfeg.noaa.gov/products/upwelling/dnld)| Upwelling index value, imported from the ERD, positive values indicate upwelling and negative values indicate downwelling (?)|
| upwelling               | |[ERD](https://oceanview.pfeg.noaa.gov/products/upwelling/dnld)| Target variable, a one-hot encoded version of the CUTI data: 0 for CUTI <= 0 and 1 for CUTI > 0              |

<br/>

In the data from the surface mooring, sea surface temperature (SST) was stable between January to April around 10 to 11 degrees C. SST was up to 17 degrees in the summer months, but highly irregular - there are several sudden decreases in SST between June and October, potential signals of coastal upwelling. A visual of the pressure measurements from the shallow profiler showed that the profiler might have remained at one position for a siginificant number of months in 2017, from early January to the end of July. Similarly, the 200 meter platform saw a sudden change in seawater pressure and dissolved oxygen between July and August. These datasets may be annotated or flagged on the OOI data access portal. 

Some plots of the profiler measurements in August show the expected relationships between depth and environmental variables - shallow water is warmer, less saline, less dense, and more oxygenated than water at 200 meters. When SST and seawater temperature recorded by the 200 meter platform are plotted on shared axes, the visible trends are inverse to each other - SST increases while the temperature at 200 meters decreases between February and August. I wonder if this inverse relationship is also caused by upwelling! 

Outliers from the 2017 platform and METBK data were identified using z-scores, which is the signed number of standard deviations each data point is above the mean ([source](https://towardsdatascience.com/ways-to-detect-and-remove-the-outliers-404d16608dba)). I used a threshold of 3 to identify outliers to drop, which made up only 0.6561% of the data. 

---

### Modeling and Results

I chose to use logistic regression and decision tree classifiers from the [scikit-learn library](https://scikit-learn.org/stable/index.html). These models are simple to implement for binary classification and allow for interpretability, so we can investigate model coefficients and feature importance to identify the best environmental variables to use as features in upwelling classification. There's also potential for using unsupervised methods to look for clusters in the data as a stretch goal. 

For the first iteration of modeling, I selected sea_surface_temperature, seawater_temperature, and practical_salinity from 01/01/17 - 09-14/17. The baseline model here, following the size of the majority class, is around 61.951%. There's strong multicollinearity in these features, so I made one logistic regression with a single feature: seawater_temperature, because it had the strongest correlation (-0.39) with the CUTI index. This model had a train accuracy of 68.9951%, and a test accuracy of 68.8415%, which beat the baseline accuracy significantly. 

Next, I created a logistic regression model using sea_surface_temperature, seawater_temperature, and practical_salinity, with PolynomialFeatures to create interaction terms and GridSearchCV to select for the best method of regularization (l1 or l2). This was my attemp at accounting for multicollinearity in this model. The best estimator returned by the grid search had a C value of 1 (may require further tuning) and a penalty of l1. This estimator had a train accuracy of 74.6179%, and a test accuracy of 75.3668%, another significant improvement in exchange for model complexity. 

Finally, I created a decision tree classifier using GridSearchCV, and found a best estimator with an alpha of 0 (may require further tuning), max_depth of 9, min_samples_leaf of 5, and min_samples_split of 20. The train accuracy of this model was 81.8893%, and the test accuracy was 79.4084%. Raising the possible max_depth value led to overfitting.

TODO: 
- compare other model metrics (recall, precision)
- discuss misclassified data

---

### Future work

There's a lot of avenues for future work with this project! Some of it may be caried over into my own future work, but here are a few quick suggestions and items from my list of stretch goals if you're interested in adding anything: 

- Use unsupervised clustering methods on T-S plots and see what they think of the different water bodies. 
- Add more variables than just those from the CTD and METBK instruments, or add more sites, or increase the number of observations passed to the models.
- Interpolate the profiler data to a regular 2D grid and feed it into models, potentially using PCA or some other dimensionality reduction or feature extraction. 
- Consider creating other classification models, like KNN, KMeans, or Naive Bayes. 

---

### Resources

The following links are included for myself and others.

[Learn how to do data requests with the OOI API](https://datalab.marine.rutgers.edu/2020/05/data-requests-the-easy-way-with-the-ooi-api/#top)

[OOILAB Python Toolbox](https://datalab.marine.rutgers.edu/2020/11/my-ooilab-python-toolbox/)

[OOI Python Examples](https://datalab.marine.rutgers.edu/category/python-examples/)

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
