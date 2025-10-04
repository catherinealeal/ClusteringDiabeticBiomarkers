# Clustering Blood Biomarkers to Identify Patients at Risk of Diabetes

## Introduction + Goal 

Diabetes is a chronic condition where the body cannot properly regulate blood sugar, and it can lead to serious complications if left untreated. Risk for diabetes is influenced by many biological factors, including age, weight, cholesterol levels, and kidney function.

**The goal of this project is to cluster patients using clinical data to identify patterns associated with diabetes.** The models I’ll be using are K-Means and Spectral Clustering. Each model will group patients based on their health features without using the diagnosis labels, allowing us to see if the clusters correspond to diabetic and non-diabetic patients.

The process will be as follows: first, preprocess and standardize the data, then fit both clustering algorithms and visualize the resulting clusters using PCA. Next, evaluate and compare the models using both internal and external metrics, such as silhouette score, ARI, and NMI. Finally, interpret the results to determine whether these unsupervised methods could be useful for identifying patients at risk of developing diabetes.

## Data Description 

The dataset used in this project is from Kaggle and contains information on patients with clinical measurements related to cardiovascular and kidney health. Each of the 5132 rows represents one patient, and each of the 9 columns provides a biological or clinical feature:

- Age
- Gender 
- BMI 
- Chol 
- TG: triglyceride level in blood
- HDL: high-density lipoprotein (“good” cholesterol)
- LDL: low-density lipoprotein (“bad” cholesterol)
- Cr: creatinine level; indicative of kidney function
- BUN: blood urea nitrogen; indicative of kidney and liver function

The Diagnosis column indicates whether a patient has diabetes (1) or not (0). This column will not be used to train the clustering models but will be used to evaluate the results.

Learn more about the data [here](https://www.kaggle.com/datasets/simaanjali/diabetes-classification-dataset).

## Analysis 

Before training, I want to visualize the true clusters. 

![image](https://github.com/catherinealeal/ClusteringDiabeticBiomarkers/blob/main/images/truePlot.png)

The clusters have quite a lot of overlap around the center (0,0), so we'll see if the separation along PC1 is signifigant enough for the clustering algorithms to pick up. 

### Algorithm 1: K-means

K-Means is a partition-based clustering algorithm that works by assigning each data point to the nearest cluster centroid, then updating the centroids iteratively until the assignments no longer change. The algorithm assumes that clusters are roughly spherical, have similar size and density, and are separable in the feature space. In this project, K-Means makes sense as a starting point because the clinical features are all continuous numeric values, and there are two natural groups in the data: diabetic and non-diabetic patients. Using K-Means provides a simple baseline to see if these two groups can be distinguished based purely on the patients’ biomarkers.

![image](https://github.com/catherinealeal/ClusteringDiabeticBiomarkers/blob/main/images/KMPlot.png)

The K-Means clusters are separated roughly straight down the middle along PC1 = 0, with the two groups appearing fairly even in size. This aligns with the algorithm’s tendency to find spherical, similarly sized clusters, and suggests that it is primarily using the strongest linear separation in the data to assign patients to clusters.

### Algorithm 2: Spectral Clustering

Spectral Clustering is a graph-based clustering algorithm that first constructs a similarity graph between data points, often using nearest neighbors, and then computes a low-dimensional embedding from the graph’s eigenvectors. The final clusters are obtained by running K-Means on this transformed space. Unlike K-Means, Spectral Clustering does not assume that clusters are spherical or of similar size, which allows it to capture more complex or irregular cluster shapes. In this project, Spectral Clustering is useful because the PCA plot suggests some overlap in the center of the data, so a method that considers the connectivity and overall structure of the points may be able to separate diabetic and non-diabetic patients more effectively.

![image](https://github.com/catherinealeal/ClusteringDiabeticBiomarkers/blob/main/images/SCPlot.png)

Compared to Kmeans, the Spectral clusters show a different pattern. The clusters are not aligned along a single principal component and the group sizes appear very uneven. This reflects SC’s ability to capture more complex structures in the data.

### Performance Metrics

To evaluate the clustering results, I’ll use a combination of internal and external metrics. 
- The **silhouette score** is an internal metric that measures how well each point fits within its assigned cluster compared to other clusters, with higher values indicating more well-separated clusters. Since we also have the true diabetes labels, I can use external metrics to see how well the clusters correspond to actual patient outcomes.
- The **Adjusted Rand Index (ARI)** measures the similarity between the predicted clusters and the true labels while correcting for chance, making it especially useful when group sizes are uneven.
- Similarly to ARI, **Normalized Mutual Information (NMI)** quantifies the shared information between the clusters and the true labels, providing a normalized measure of agreement.

Together, these metrics allow me to assess both the quality of the clusters in the feature space and their relevance to predicting diabetes.

![image](https://github.com/catherinealeal/ClusteringDiabeticBiomarkers/blob/main/images/metrics.png)

## Results 

Based on the plots and evaluation metrics, the two algorithms captured different aspects of the data, but neither produced clusters that perfectly correspond to diabetes status.

K-Means produced two fairly even clusters separated along PC1 = 0, which aligns with its assumptions of spherical and similarly sized clusters. The silhouette score of 0.227 indicates that the clusters are moderately cohesive, while the ARI (0.178) and NMI (0.116) suggest a weak correspondence between the clusters and the true diabetes labels. This implies that K-Means is largely capturing the overall linear separation in the data but is not reliably identifying diabetic patients.

On the other hand, Spectral Clustering generated clusters that were uneven in size and not aligned along a principal component. Its higher silhouette score (0.390) shows that the clusters are internally more cohesive in the transformed space, but the ARI (0.117) indicates that this structure does not match the true labels well. From the plots, it appears that SC tends to identify diabetic patients with extreme biomarker values while missing those with more average values. 

## Conclusion 

Overall, these results suggest that while clustering can reveal patterns in patient biomarker data, unsupervised methods like K-Means and Spectral Clustering are not particularly effective at identifying diabetes across all patients. The clusters do not reliably separate diabetic and non-diabetic patients, likely because the groups overlap in the feature space and do not form distinct shapes that these algorithms assume. However, Spectral Clustering appears to capture patients with more extreme biomarker values, which could make it useful for identifying high-risk or clearly diabetic patients, even if it fails to detect those with more borderline or average values. For general prediction of diabetes, supervised methods trained on the diagnosis labels might still provide more accurate results, but clustering could offer some value for flagging the highest-risk cases.

View the full project [here](https://github.com/catherinealeal/ClusteringDiabeticBiomarkers/blob/main/Analysis.ipynb). 
