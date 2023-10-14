# Anomaly detection in Time Series Data of Wind Turbines
Identifying anomalies in wind turbine performance using wind turbine operational data produced during a single year.

This repository contains the final 4-week capstone project on Anomaly detection in Time Series Data of Wind Turbines.

The project was done in collaboration with [Fluence Nispera](https://www.linkedin.com/company/nispera-ag/).

## Purpose

Fluence, a global leader in energy storage and digital applications for renewable energy, offers a wind power plant monitoring platform. This platform helps wind power plant operators understand plant performance and identify opportunities for improvement. The ability to automatically detect deviations from normal operation of turbines in wind power plants is one of the most sought-after features of such a platform. But understanding what is “normal” operation is especially challenging as not all wind turbines, nor all wind power plant locations, are created equal. What might be normal operation in one location or turbine, might represent a less than optimal operation in another. Our team’s challenge was to identify anomalies in wind turbine performance using wind turbine operational data produced during a single year.

### Anomaly Cloud Clustering with Isolation Forest and KMeans

Investigation of machine learning based approaches, especially those which required no human intervention, was of interest to Fluence. As a first step, we applied several machine learning models designed for anomaly detection from the PyOD and Scikit Learn libraries, including [Local Outlier Factor (LOF)](https://scikit-learn.org/stable/modules/generated/sklearn.neighbors.LocalOutlierFactor.html), [Cluster By Local Outlier Factor (CBLOF)](https://pyod.readthedocs.io/en/latest/pyod.models.html#module-pyod.models.cblof), [Copula Based Outlier Detector (COPOD)](https://pyod.readthedocs.io/en/latest/pyod.models.html#module-pyod.models.copod), [Gaussian Mixture Model (GMM)](https://pyod.readthedocs.io/en/latest/pyod.models.html#module-pyod.models.gmm) and [Isolation Forest](https://pyod.readthedocs.io/en/latest/pyod.models.html#module-pyod.models.iforest). 

While all models were able to identify anomalies, visual inspection revealed that Isolation Forest was best at correctly identifying anomalous data points. LOF provided comparable yet inferior results. CBLOF, COPOD, and GMM struggled to differentiate between anomalous and normal data. 

![Select Comparison of ML Model Outputs](/ML_Comparison.png)



Despite having the most promising results of the models tested, the performance of Isolation Forest varied widely depending on the data set. In many cases Isolation Forest failed to deliver the same quality results as statistically based approaches, defining areas of “normal” data as anomalous. 

![Additional Example of Isolation Forest Output](reports/figures/Isolation Forest.png)



Because of this shortcoming, the use of Isolation Forest alone did not seem sufficient. However, our team came up with an idea which might reduce the impact of the mislabeled data, while also given the opportunity to change the anomaly label resolution from individual data points to larger time periods, labelling the entire time period as anomalous. 

After Isolation Forest was used to label the entire data set (consisting of one year of data), the data set was split into weeks. Any data which Isolation Forest labelled as ‘normal’ was removed from each week’s data, leaving only a ‘cloud’ of anomalous data. Each cloud of data was then feed to a [KMeans model](https://scikit-learn.org/stable/modules/generated/sklearn.cluster.KMeans.html) and clustered into one of two groups. 

![Anomaly Cloud](reports/figures/Anomaly Cloud.png)


Regardless of their inherent normality, each week of data contained some volume of anomalies identified by the Isolation Forest model. By using KMeans to cluster the weeks into two groups, weeks which were ‘more normal’ would group together while weeks which were ‘less normal’ would group together as well. The results of this approach were mixed, but did show promise in specific situations.

This combined model was able to successfully identify power curtailments in three data sets; ID06, ID07, and ID14. These datasets were the only in the provided data to have clear and sustained power curtailments. As long as the change in operation was clear and sustained this model did a great job of identifying and labelling this change in operation mode. 
















![Promising Results](reports/figures/ACC - Promising Results.png)


However, this approach struggles to deliver clear signals when the operation of the turbine is erratic, or there are multiple failure modes present in the same dataset. In such cases, weeks which seemed very similar were labelled as different and weeks which were clearly very different were not singled out. 

![Less-Than-Clear Labels](reports/figures/ACC - Struggles.png)



* Due to proprietary data restrictions, the code for this project is not publicly available
