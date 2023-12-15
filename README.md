# OSDA-HomeWork

## LazyFCA

### Binarization
According to my observations, the best way of binarizing a dataset is the following: one-hot-encode all categorical values (pd.get_dummies method) and quantile-based discretization for integer and float values (Discretize variable into equal-sized buckets based on quantiles - pd.qcut method). The only thing left is to choose the best values for the number of quantiles. And again, according to my observations, the more the bins, the better the classification (this is not always true, that is why we use cross validation to estimate best number of quantiles).  
Speaking about the $\alpha$ value, in most cases changing it did not result in prediction changes.

### Pattern classification
To choose from the three possible methods and $\alpha$ values CV Grid Search was used with 11 possible values for $\alpha$: [0., 0.1, 0.2, ..., 1]. If the dataset was too big to calculate SV scores in appropriate amount of time, the size of the dataset was reduced.
And then estimator with the best parameters was refitted to bigger dataset.

### Estimating performance
F1 and Accuracy scores were estimated as mean in a 5-fold cross validation and put in a talbe in decreasing order wrt to F1 score.  
Notice that if classifer was not able to predict the target it was treated as false classification. 

## Results

### Bank Application 

#### Binarization
Features' quantile counts that result in the best scores are:
<ul>
  <li>Chilren: 1</li>
  <li>Annual Income: 8</li>
  <li>Employed Days: 5</li>
  <li>Family Members: 2</li>
</ul>

#### Pattern classification  
<ul>
  <li>best method: "standard"</li>
  <li>best $\alpha$ value: 0</li>
</ul>

#### Scores
|        Model       |   F1  | Accuracy |
|:------------------:|:-----:|:--------:|
|    Random Forest   | 0.529 |   0.926  |
|      CatBoost      | 0.522 |   0.918  |
| Logistic Regression| 0.502 |   0.903  |
|    Decision Tree   | 0.417 |   0.860  |
|         KNN        | 0.413 |   0.891  |
| Pattern classifier | 0.372 |   0.911  |
|       xGboost      | 0.321 |   0.906  |
|Binarized classifier| 0.302 |   0.862  |
|     GaussianNB     | 0.012 |   0.894  |

As we can see, Binarized classifier did not perform best, but still managed to get score close to that of xGboost. Whereas Pattern classifier eveng managed to outperform xGboost classifier and got pretty high accuracy. The reason for low scores may be the following. The target of this dataset is approval or rejection of some application. It seems, that in real world the decision was made by human and for applicants with same (or similair) information the decisions could be different.

### Bank Marketing 
Here the dataset was too big for FCA algorithms (>43k samples). That is why I ranfomly picked only 2,5% of the dataset (stratified wrt target) to asses LazyFCA performance. </br>

#### Binarization
Features' quantile counts that result in best scores are:
<ul>
  <li>Age: 8</li>
  <li>Balance: 8</li>
  <li>Campaign: 2</li>
</ul>

#### Pattern classification  
<ul>
  <li>best method: "ratio-support"</li>
  <li>best $\alpha$ value: 1</li>
</ul>

#### Scores
|        Model       |   F1  | Accuracy |
|:------------------:|:-----:|:--------:|
|    Decision Tree   | 0.273 |   0.818  |
|    Random Forest   | 0.251 |   0.878  |
| Pattern classifier | 0.248 |   0.605  |
|         KNN        | 0.232 |   0.819  |
| Logistic Regression| 0.211 |   0.873  |
|Binarized classifier| 0.171 |   0.844  |
|      CatBoost      | 0.118 |   0.884  |
|       xGboost      | 0.066 |   0.883  |
|     GaussianNB     | 0.024 |   0.882  |

Here Binarized classifier takes intermediate place and even manages to outperform CatBoost and xGboost. Whilst Pattern classifier got top 3 in the list of classifers. The reasons for such bad performance of all models may be the same as in the previous dataset (here the target is a label of term deposit subscription).

### Breast Cancer

#### Binarization
<ul>
  <li>Here all attributes are numerical and the best quantile count is 25 for all attributes</li>
</ul>

#### Pattern classification  
<ul>
  <li>best method: "standard-support"</li>
  <li>best $\alpha$ value: 1</li>
</ul>

#### Scores
|        Model       |   F1  | Accuracy |
|:------------------:|:-----:|:--------:|
|       xGboost      | 0.950 |   0.963  |
|      CatBoost      | 0.949 |   0.961  |
| Logistic Regression| 0.948 |   0.961  |
|    Random Forest   | 0.945 |   0.960  |
|Binarized classifier| 0.917 |   0.937  |
|     GaussianNB     | 0.914 |   0.939  |
|         KNN        | 0.910 |   0.935  |
|    Decision Tree   | 0.906 |   0.931  |
| Pattern classifier | 0.542 |   0.372  |

Again Binarized classifier takes intermidate place. But more than that, it managed to give high scroes overall. It seems that Binarized classifier works good when numerical values are variedly distributed (in the previous examples numerical values tended to have very close values). This "good" distribution allowed us to split numerical values into 25 bins each. At the same time pattern classifier seems to struggle when all attributes are numerical.

### Fraud Detection
Here the dataset was very big again (>500k samples). That is why I ranfomly picked only 0,5% of the dataset (stratified wrt target) to asses LazyFCA performance. </br>

#### Binarization
<ul>
  <li>All attributes are again numerical and the best quantile count is 5 for all attributes</li>
</ul>

#### Pattern classification  
<ul>
  <li>best method: "standard"</li>
  <li>best $\alpha$ value: 0</li>
</ul>

#### Scores
|        Model       |   F1  | Accuracy |
|:------------------:|:-----:|:--------:|
|      CatBoost      |   1   |     1    |
|       xGboost      | 0.978 |   0.979  |
|    Decision Tree   | 0.960 |   0.961  |
|    Random Forest   | 0.952 |   0.953  |
| Logistic Regression| 0.952 |   0.953  |
|     GaussianNB     | 0.929 |   0.932  |
|         KNN        | 0.926 |   0.930  |
|Binarized classifier| 0.751 |   0.787  |
| Pattern classifier | 0.668 |   0.501  |

Here LazyFCA performed worst with far lower scroe than the rest of the classifiers. The reason for that may be because other classifiers learn on the entire dataset (which is 200 times more than that of LazyFCA's). Maybe if LazyFCA algorithms "saw" more data, they would have learned more cocnepts and performed better, but unfortunately, it would take much more time. Pattern classifier scored worst whit faee lower scores than classicalm ML classifiers. This again proves that this classifier struggles when all attributes are numerical.

#### Heart Attack

#### Binarization
Features' quantile counts that result in best scores are:
<ul>
  <li>Age: 6</li>
  <li>trtbps: 6</li>
  <li>chol: 6</li>
  <li>thalachh: 6</li>
  <li>oldpeak: 3</li>
</ul>

#### Pattern classification  
<ul>
  <li>best method: "ratio-support"</li>
  <li>best $\alpha$ value: 0.5</li>
</ul>

#### Scores
|        Model       |   F1  | Accuracy |
|:------------------:|:-----:|:--------:|
|    Random Forest   | 0.865 |   0.845  |
|      CatBoost      | 0.863 |   0.845  |
| Logistic Regression| 0.862 |   0.845  |
|       xGboost      | 0.862 |   0.842  |
|Binarized classifier| 0.858 |   0.845  |
| Pattern classifier | 0.833 |   0.800  |
|     GaussianNB     | 0.832 |   0.809  |
|    Decision Tree   | 0.828 |   0.798  |
|         KNN        | 0.820 |   0.805  |

Here LazyFCA classifiers manage to keep up with SOTA algorithms of classification, namely boosting and baggin algorithms.

#### Heart Failure

#### Binarization
<ul>
  <li>Here the best quantile count is 4 for all numerical attributes</li>
</ul>

#### Pattern classification  
<ul>
  <li>best method: "standard"</li>
  <li>best $\alpha$ value: 0</li>
</ul>

#### Scores
|        Model       |   F1  | Accuracy |
|:------------------:|:-----:|:--------:|
|       xGboost      | 0.896 |   0.883  |
|    Random Forest   | 0.892 |   0.878  |
|Binarized classifier| 0.889 |   0.875  |
|     GaussianNB     | 0.881 |   0.867  |
|      CatBoost      | 0.879 |   0.863  |
| Logistic Regression| 0.877 |   0.861  |
| Pattern classifier | 0.869 |   0.847  |
|         KNN        | 0.863 |   0.840  |
|    Decision Tree   | 0.852 |   0.838  |

Binarized classifier is again on par with SOTA algorithms and even manages to outperform CatBoost. Whereas Pattern classifier did not perform so well.

#### Mushrooms
The dataset is again very big(8k samples), that is why I ranfomly picked 30% of the dataset (stratified wrt target) to asses LazyFca performance.

#### Binarization
<ul>
  <li>In this dataset all attributes are categorical</li>
</ul>

#### Pattern classification  
<ul>
  <li>best method: "standard-support"</li>
  <li>best $\alpha$ value: 0</li>
</ul>

#### Scores
|        Model       |   F1  | Accuracy |
|:------------------:|:-----:|:--------:|
|    Decision Tree   |   1   |     1    |
|    Random Forest   |   1   |     1    |
|       xGboost      |   1   |     1    |
|      CatBoost      |   1   |     1    |
|Binarized classifier|   1   |     1    |
| Pattern classifier |   1   |     1    |
|         KNN        | 0.999 |   0.999  |
| LogisticRegression | 0.966 |   0.967  |
|     GaussianNB     | 0.825 |   0.842  |

Great performance, on par with SOTA classifiers.

### Conclusion.
Seems that Binarized Classifier manages to perform good when all data is categorical and/or numerical attributes have "good" distribution (the one for which values can be discretize in many equal-sized bins).
As for Pattern classifier, it seems to perform bad when all attributes are numerical.
