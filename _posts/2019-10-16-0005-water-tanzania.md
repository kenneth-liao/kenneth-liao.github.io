---
title: "Improving Access to Clean, Safe Drinking Water in Tanzania"
date: 2019-10-16
tags: [Social Impact, Multinomial Classification, Logistic Regression, Random Forest]
header:
  overlay_image: "/images/0005-water-tanzania/Waterorg_Resources_Header.original.jpg"
  overlay_filter: 0.5
  teaser: "/images/0005-water-tanzania/Waterorg_Resources_Header.original.jpg"
excerpt: "Classification model for predicting functional status of water pumps in Tanzania."
mathjax: "true"
# This sets the table of contents on the right of the page
toc: true
toc_label: "Table of Contents"
toc_icon: "chart-line"
toc_sticky: "true"
---

# Improving Access to Clean, Safe Drinking Water in Tanzania

## Motivation
Tanzania is recognized by the UN as a least developed country, or LDC<sup>1</sup>. LDCs are, in part, defined by the UN as "low-income countries confronting severe structural impediments to sustainable development"<sup>2</sup>. One critical impediment the country has struggled with for decades has been supplying and maintaining access to clean and safe drinking water for Tanzanians. In 2017, a reported 24.8 million citizens lacked access to 'at least basic' water<sup>3</sup>. For those fortunate enough to have access to this mandatory resource at any given time, it is never gauranteed.

In most of the rural parts of the country, much of the potable water is accessed via water pumps that draw water from local sources. Around 60,000 hand-pumps are installed in sub-Saharan Africa every year, but between 30 to 40% of those do not function at anyone one time<sup>4</sup>! These non-functioning pumps have realized an estimated loss of 1.2 billion USD over the last 2 decades<sup>4</sup>. One of the biggest challenges with maintaining non-functioning pumps is identifying the pumps that are in need of repair or have completely broken down. Once the functional status of a pump has been identified, the appropriate resources and man-power can be deployed to target sites in need of repair or replacement. Unfortunately, there is no efficient system to-date to tackle this monumental problem. The local governments and organizations tackling the water crisis simply do not possess enough time, resources, or man power to check every pump in the country. Identifying the functional status of these water pumps is therefore the focus of this work.

Taarifa is an open-source platform for crowd-sourced reporting and triaging of infrastructure related issues. Together with the Tanzanian Ministry of Water, data has been collected for thousands of water pumps throughout Tanzania. The goal of this project is to be able to predict the condition of these water pumps to improve allocation of resources and pump maintenance, reduce pump downtime, and ensure basic water access for tens of millions of Tanzanians.

***The Data***

The dataset is provided by Taarifa, together with the Tanzanian Ministry of Water and is hosted by DrivenData.org:

https://www.drivendata.org/competitions/7/pump-it-up-data-mining-the-water-table/page/23/

## EDA
59,400 unique water pumps described by 39 features comprise the dataset. Each water pump is represented exactly once in the dataset and thus the shape of the training data is 59,400x39. Figure 1 below illustrates the large class imbalance with the following breakdown: 54.3% - functional, 7.3% - functional but require repair, 38.4% - non functional. This stratification had to be taken into account when creating train/test splits.

#### Figure 1 | Pump Status Classes
<script src="https://cdn.plot.ly/plotly-latest.min.js"></script>
<iframe width="100%" height="550" frameborder="0" scrolling="no" src="/images/0005-water-tanzania/pump-conditions.html"></iframe>

#### What is the most common type of pump?

The top 10 types of pumps are shown in Table 1. The majority of pumps use gravity as the pumping mechanism. Nira and Tanira pumps are the most popular model of handpumps represented in this dataset. However, the india mark ii is the world's most widely used water handpump<sup>5</sup>. The majority of these hand pumps are designed to serve communities of around 300 people.

#### Table 1 | Pump Count by Pump Type
| extraction_type | Pump Count |
| :----: | :----: |
| gravity	| 26780 |
| nira/tanira | 8154 |
| other | 6430 |
| submersible | 4764 |
| swn 80 | 3670 |
| mono | 2865 |
| india mark ii | 2400 |
| afridev | 1770 |
| ksb | 1415 |
| other - rope pump | 451 |

#### Which type of pump has the highest number of non functional pumps?

Table 2 shows that the number of non functional pumps within each pump type does not follow the same trend as the total number of pumps. The pump type with the highest number of non functional pumps is, not surprisingly, `gravity`. The second however is `other` with 5,195 non functional pumps. Of course `other` is likely a catch-all term for any pump that didn't fit into any of the other pump types. Therefore, we can't know exactly what type of pump it is. Interestingly, even though there are 1500+ more `nira/tanira` total pumps than `other`, `other` contains over 3,000 more non functional pumps than `nira/tanira`. It will be interesting to see which pump type the model is best able to classify.

#### Table 2 | Highest Non Functional Pump Counts
| extraction_type | functional | functional needs repair | non functional | 
| :----: | :----: | :----: |
| gravity | 16048.0 | 2701.0 | 8031.0 | 
| other | 1029.0 | 206.0 | 5195.0 | 
| nira/tanira | 421.0 | 641.0 | 2092.0 | 
| submersible | 2626.0 | 227.0 | 1911.0 | 
| mono | 1082.0 | 129.0 | 1654.0 | 

#### Which type of pump serves the highest average number of people?

#### Table 3 | Popuation Per Pump Type
| extraction_type | population per pump |
| :----: | :----: |
| windmill | 408.358974 |
| india mark iii | 378.112245 |
| other - play pump | 343.082353 |
| submersible | 324.308564 |
| india mark ii | 298.967083 |
| afridev | 249.866102 |
| other - rope pump | 214.128603 |
| other | 209.885381 |
| swn 80 | 205.832970 |
| gravity | 148.117065 |



## Data Munging

There are a total of 7 feature columns with missing data which have to be dealt with. Luckily, all 7 of these feature columns are categorical of `object` pandas datatypes. None of the numerical columns have missing values. The missing categorical values are dealth with automatically when converting those features into dummy variables. Dummy variable conversion is necessary when working with categorical text data since training any model requires numerical datatypes. This method changes each unique value in the categorical feature column into its own binary column. An example is shown below where all missing values for the `waterpoint_type_group` feature are combined into a single binary feature column `waterpoint_type_group_nan`

#### Table 4 | Sample of Categorical Dummy Variables
| id | date_recorded | funder_0 | ... | waterpoint_type_group_nan |
| :----: | :----: | :----: | :----: |
| 69572 | 2011-03-14 | 0 | ... | 0 |
| 8776 | 2013-03-06 | 0 | ... | 0 |
| 34310 | 2013-02-25 | 0 | ... | 0 |
| 67743 | 2013-01-28 | 0 | ... | 0 |
| 19728 | 2011-07-13 | 0 | ... | 0 |

Some of the feature columns are either duplicates or derived from other feature columns and getting rid of these will help reduce the total number of features. The features that were dropped from the dataset were:

1. `recorded_by`
2. `payment_type`
3. `quality_group`
4. `quantity_group`

## Results

I first created a logistc regression model to compare the performance of a random forest model to. I used a Bayesion optimization algorithm to find the optimal value of the regularization parameter C and obtained the results below.

#### Table 5 | Results of Optimized Logistic Regression Model
| functional | functional needs repair | non functional |
| :----: | :----: | :----: |
| precision | 0.721376 | 0.154448 | 0.638251 |
| recall | 0.561340 | 0.526316 | 0.548136 |
| f1-score | 0.631375 | 0.238815 | 0.589771 |
| classification_rate | 0.553770 | 0.553770 | 0.553770 |

The classification rate is simply defined as the overall accuracy of the model and is what the drivendata competition used to score submissions. 

#### Table 6 | Results of Optimized Random Forest Model
| functional | functional needs repair | non functional |
| :----: | :----: | :----: |
| precision | 0.813756 | 0.576444 | 0.841199 |
| recall | 0.891874 | 0.357193 | 0.786270 |
| f1-score | 0.851026 | 0.441075 | 0.812808 |
| classification_rate | 0.812825 | 0.812825 | 0.812825 |

The precision-recall curves are plotted for both models in Figure 2 below.

#### Figure 2 | 
<script src="https://cdn.plot.ly/plotly-latest.min.js"></script>
<iframe width="100%" height="550" frameborder="0" scrolling="no" src="/images/0005-water-tanzania/precision_recall_combined.html"></iframe>




A spacial map of the three functional classes of pumps is shown in Figure 2 below.

#### Figure 3 | 
<script src="https://cdn.plot.ly/plotly-latest.min.js"></script>
<iframe width="100%" height="550" frameborder="0" scrolling="no" src="/images/0005-water-tanzania/status_group_map.html"></iframe>


## Conclusions
Two common pumps are the Afridev ($1,427) and the India Mark II ($1,585). Installing a new pump incurs additional costs for digging the whole, building a housing for the pump, and labor. It's estimated that an average pump installation costs around $3,800 USD, including the pump<sup>6</sup>.  

I trained both a Logistic Regression and a Random Forest model to classify water pumps as "functional", "function needs repair", and non functional. I used a Bayesion optimization model to find the best parameters for the models. There was a stark difference in the performance of the two models, with the Random Forest model completely outperforming the Logistic Regression model. The Random Forest model achieved a classification rate of 0.813 on the test set while the Logistic Regression model achieved a classfication rate of only 0.554.

While I didn't hypertune the model to specifically focus on improving the recall score for accurately predicting "non functional" pumps, the optimized model had overall better results for both precision and recall since I used the weighted f1_score as the loss function. The Random Forest model would highly benefit the Tanzanian water of ministry and aid the organization in deployed constrained resources and man power to the pumps that need it most, with much higher accuracy than before.

As a motivation for follow up work, I think the model would benefit from minimizing the overfitting indicated by the large difference in the train and test scores. Further exploration and understanding of the most important features of the model is also desirable. Finally, introducing additional engineered features that perhaps underly the top important features such as indicators for when a water source will be dry would likely improve the model vastly.

## References

1. https://www.un.org/development/desa/dpad/wp-content/uploads/sites/45/publication/ldc_list.pdf
2. https://www.un.org/development/desa/dpad/least-developed-country-category.html
3. https://washwatch.org/en/countries/tanzania/summary/statistics/
4. https://www.theguardian.com/global-development-professionals-network/2016/mar/22/how-do-you-solve-a-problem-like-a-broken-water-pump
5. https://en.wikipedia.org/wiki/India_Mark_II
6. http://www.ropepumps.org/uploads/2/9/9/2/29929105/rope_pump_-_piston_pumps._comp._study_tz.pdf