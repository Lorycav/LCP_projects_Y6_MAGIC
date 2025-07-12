# MAGIC telescope data analysis

## Description

The project is a Python-based data analysis tool for the MAGIC telescope. It focuses on the analysis of Monte Carlo generated data to simulate the registration of high-energy gamma particles in a ground-based atmospheric Cherenkov gamma telescope using the imaging technique. The data are labelled as *signal* and *background* according to the origin of the gamma particles.

Attribute Information:

1. `fLength`: continuous # major axis of ellipse [mm] 
2. `Width`: continuous # minor axis of ellipse [mm] 
3. `Size`: continuous # 10-log of sum of content of all pixels [in #phot] 
4. `Conc`: continuous # ratio of sum of two highest pixels over fSize [ratio] 
5. `Conc1`: continuous # ratio of highest pixel over fSize [ratio] 
6. `Asym`: continuous # distance from highest pixel to center, projected onto major axis [mm] 
7. `M3Long`: continuous # 3rd root of third moment along major axis [mm] 
8. `M3Trans`: continuous # 3rd root of third moment along minor axis [mm] 
9. `Alpha`: continuous # angle of major axis with vector to origin [deg] 
10.`fDis`\: continuous # distance from origin to center of ellipse [mm] 
11.`class`: g,h # gamma (signal), hadron (background) 

## Table of Contents

1. [Study of the dataset features and comparison between signal and background events](#study-of-the-dataset-features-and-comparison-between-signal-and-background-events)
2. [ Study of features correlation](#features-correlation)
3. [Rescaling Data with Respect to Energy](#rescaling-data-with-respect-to-energy)
4. [Principal Component Analysis (PCA)](#principal-component-analysis-pca)
5. [Classification Using Machine Learning techniques](#classification-using-machine-learning)
6. [Study of Gammaness and Alpha Cuts](#study-of-gammaness-and-alpha-cuts)
7. [Computation of Observation Time](#computation-of-observation-time)

## Study of the dataset features and comparison between signal and background events

Separate the data according to their class in order to study their features both for signal and background. For each feature we compute its mean and standard deviation and compare them for the two classes.

## Features Correlation

Explore the correlation between different features in the data both for signal and background.

## Rescaling Data with Respect to Energy

Compare the `fWidth` and `fLength` parameters' distribution applying a cut on `fSize`, that is proportional to the number of photons, so to the primary ray energy. We consider a value of `fSize` bigger than its mean plus its standard deviation and a value lower than its mean minus its standard deviation. 

## Principal Component Analysis (PCA)

PCA is a statistical technique for reducing the dimensionality of the problem, so the complexity. The main idea is to study whether there are directions in the original feature space (i.e., a linear combination of them) where the data show significant patterns in their distribution. To achieve this, PCA involves the calculation of eigenvectors and eigenvalues from the covariance matrix. These eigenvectors are used to construct a transformation matrix that realigns the dataset into a new coordinate system based on the covariance matrix. In this transformed space, the axes are oriented along the directions of maximum variance. Specifically, the axis corresponding to the eigenvector with the highest eigenvalue represents the direction of maximum variance, while the direction of minimum variance correspondsto the eigenvector with the smallest eigenvalue. It is then possible to reduce the dimensionality of the features by transforming it into the space of principal components and consider only the n most significant ones, so the n features that define the best approximation of the dataset in a lower dimensional space. In order to better understand the dataset we will perform PCA differently for signal and background.

## Classification Using Machine Learning

The dataset, divided into two classes, outlines a generic binary classification problem. The approach presented is based on the study and optimization of four different machine learning models and in-depth analysis of the model that shows the best performance. The classification models introduced here and then further investigated are:
- K-Nearest Neighbor (K-NN)
- Support Vector Machine (SVM) with kernels
- Feedforward Neural Network
- Random Forest.

The metrics selected to evaluate the performance of each classifier obtained include ROC curve, area underneath ROC (AUC) and quality factor $Q=\frac{\epsilon_{\gamma}}{\sqrt{\epsilon_h}}$, where $\epsilon_{\gamma}$ represent the percentage of correctly classified signals and $\epsilon_h$ the percentage of hadrons classified as signal. The challenge, referred to a real physics case, is the ability to discriminate gamma rays from background noise while minimizing false positives, i.e. background events classified as gamma rays.

We want to highlight that it is therefore not possible to adopt accuracy as the main metric for model performance evaluation because it gives equal importance to signal and background. Consequently, our primary goal is to maximize the quality factor and the area under the ROC curve, not paying too much attention at the number of signal events classified as background, in order to obtain better quality data.

The best models for each type of classifier are obtained through a grid search, trained and evaluated on 70% of the dataset. Once each best classifier is identified, it will then be tested on the remaining 30% to fine-tune the hyperparameters and evaluate its overall quality metrics.

## Study of Gammaness and Alpha Cuts

The objective is to find the best values for the parameters Gammaness and Alpha Cut defined as follows:
- $gammaness$: discriminant parameter that measures the likelihood that an event originated from a primary gamma source;
- $alpha cut$: values of the parameter `fAlpha` used as treshold to select the data.\
The values of the angle alpha increases of 1° at each cut; for the gammaness values, instead, we consider a range from 0.6 to 0.9. We do not consider values larger than 0.9 since a threshold bigger than 0.9 would be a too strong requirement in our case not applicable in case of real data.\
To find the best values for these two parameters, we perform a grid search computing the value of Q for every point of the grid, looking for its highest value. For this computation we apply the best classifier again to "test 1".

## Computation of Observation Time

Compute the osbervation time we would need to have, in order to get the results we got, while observing the Crab Nebula instead. To do so we refer to the energy spectrum of the nebula measured by experiments HEGRA [4] and MAGIC [5], in particular we use the functions used in the article to fit the experimental spectrum along with the parameters of the best fit.
The energy spectrum is $\frac{dN}{dE}$ with $N=\frac{n_{\gamma}}{S\cdot\Delta t}$, where $S$ is the collection area of the telescope, assumed to be $10^9 cm^2$, $n_{\gamma}$ is the number of observed gammas (the sum of false and true positives coming from our best classificator, random forests) and $\Delta t$ is the observation time we want to estimate. It is clear that:
    
$\Delta t=\frac{n_{\gamma}}{S\cdot I}$ with $I=\int_{0.05 TeV}^{50 TeV} \frac{dN}{dE} dE$


## Requirements

- Python 3.x
- Required Python packages:
    * `numpy`
    * `pandas`
    * `seaborn`
    * `scipy`
    * `matplotlib`
    * `sklearn`

## Data

The dataset can be downloaded using

```bash
wget https://archive.ics.uci.edu/ml/machine-learning-databases/magic/magic04.data
```

## References

[1] Bock, R.K., Chilingarian, A., Gaug, M., Hakl, F., Hengstebeck, T., Jirina, M., Klaschka, J., Kotrc, E., Savicky, P., Towers, S., Vaicilius, A., Wittek W. (2004). 
Methods for multidimensional event classification: a case study using images from a Cherenkov gamma-ray telescope. 
Nucl.Instr.Meth. A, 516, pp. 511-528. 

[2] P. Savicky, E. Kotrc. 
Experimental Study of Leaf Confidences for Random Forest. 
Proceedings of COMPSTAT 2004, In: Computational Statistics. (Ed.: Antoch J.) - Heidelberg, Physica Verlag 2004, pp. 1767-1774. 

[3] J. Dvorak, P. Savicky. 
Softening Splits in Decision Trees Using Simulated Annealing. 
Proceedings of ICANNGA 2007, Warsaw, (Ed.: Beliczynski et. al), Part I, LNCS 4431, pp. 721-729.

[4] Aharonian, F. et al.
The Energy Spectrum of TeV Gamma-Rays from the Crab Nebula as measured by the HEGRA system of imaging air Cherenkov telescopes
Astrophys. J. 539 (2000) 317-324

[5] Aleksic, J. et al.
Measurement of the Crab Nebula spectrum over three decades in energy with the MAGIC telescopes
Journal of High Energy Astrophysics, 5–6 (2015) 30-38.

