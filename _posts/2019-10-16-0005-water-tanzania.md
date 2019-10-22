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
toc_icon: "opacity"
toc_sticky: "true"
---

# Improving Access to Clean, Safe Drinking Water in Tanzania

## Motivation
Tanzania is recognized by the UN as a least developed country, or LDC<sup>1</sup>. LDCs are, in part, defined by the UN as "low-income countries confronting severe structural impediments to sustainable development"<sup>2</sup>. One critical impediment the country has struggled with for decades has been supplying and maintaining access to clean and safe drinking water for Tanzanians. In 2017, a reported 24.8 million citizens lacked access to 'at least basic' water<sup>3</sup>. For those fortunate enough to have access to this life-essential resource at any given time, it is never gauranteed.

In most of the rural parts of the country, much of the potable water is accessed via water pumps that draw water from local sources. Around 60,000 hand-pumps are installed in sub-Saharan Africa every year, but between 30 to 40% of those do not function at anyone one time<sup>4</sup>! These non-functioning pumps have realized an estimated loss of $1.2 billion USD over the last 2 decades<sup>4</sup>. One of the biggest challenges with maintaining non-functioning pumps is identifying the pumps that are in need of repair or have completely broken down. Once the functional status of a pump has been identified, the appropriate resources and man-power can be deployed to target sites in need of repair or replacement. Unfortunately, there is currently no efficient or accurate system to tackle this monumental problem. Local governments and organizations battling the water crisis simply do not possess enough time, resources, or man-power to check every pump in the country. Identifying the functional status of these water pumps is therefore critical to ensuring access to clean drinking water and is the focus of this work.

The problem statement can be phrased as follows. Data exists for tens of thousands of pumps which are assumed to be functional, but we don't actually know until someone is sent out to physically check the pump's status. Rather than physically checking every pump, can we predict with some accuracy better than random, which pumps will be functional and which will be non functional? Thus, the goal of this project is to be able to predict the functional status of water pumps in Tanzania to improve allocation of resources for pump maintenance, reduce pump downtime, and ensure basic water access for tens of millions of Tanzanians.

***The Data***

Taarifa is an open-source platform for crowd-sourced reporting and triaging of infrastructure related issues. Together with the Tanzanian Ministry of Water, data has been collected for thousands of water pumps throughout Tanzania. The data is hosted as part of a competition by DrivenData.org:

<a href="https://www.drivendata.org/competitions/7/pump-it-up-data-mining-the-water-table/page/23/">https://www.drivendata.org/competitions/7/pump-it-up-data-mining-the-water-table/page/23/</a>

## EDA
59,400 unique water pumps described by 39 features comprise the dataset. Each water pump is represented exactly once in the dataset and thus the shape of the training data is 59,400x39. Figure 1 below illustrates the large class imbalance with the following breakdown: 54.3% - functional, 7.3% - functional but require repair, 38.4% - non functional. This stratification had to be taken into account when creating train/test splits.

#### Figure 1 | Pump Status Classes
<script src="https://cdn.plot.ly/plotly-latest.min.js"></script>
<iframe width="100%" height="500" frameborder="0" scrolling="no" src="/images/0005-water-tanzania/pump-conditions.html"></iframe>

#### What is the most common type of pump?

The top 10 types of pumps are shown in Table 1. The majority of pumps use gravity as the pumping mechanism. Nira and Tanira pumps are the most popular model of handpumps represented in this dataset. However, the india mark ii is the world's most widely used water handpump<sup>5</sup>. The majority of these hand pumps are designed to serve communities of around 300 people.

#### Table 1 | Pump Count by Pump Type
<table style="width:100%">
<tr><th  style="text-align:center">extraction_type</th><th  style="text-align:center">Pump Count</th></tr>
<tr><td  style="text-align:center">gravity</td><td  style="text-align:center">26780</td></tr>
<tr><td  style="text-align:center">nira/tanira</td><td  style="text-align:center">8154</td></tr>
<tr><td  style="text-align:center">other</td><td  style="text-align:center">6430</td></tr>
<tr><td  style="text-align:center">submersible</td><td  style="text-align:center">4764</td></tr>
<tr><td  style="text-align:center">swn 80</td><td  style="text-align:center">3670</td></tr>
<tr><td  style="text-align:center">mono</td><td  style="text-align:center">2865</td></tr>
<tr><td  style="text-align:center">india mark ii</td><td  style="text-align:center">2400</td></tr>
<tr><td  style="text-align:center">afridev</td><td  style="text-align:center">1770</td></tr>
<tr><td  style="text-align:center">ksb</td><td  style="text-align:center">1415</td></tr>
<tr><td  style="text-align:center">other - rope pump</td><td  style="text-align:center">451</td></tr>
</table>

#### Which type of pump has the highest number of non functional pumps?

Table 2 shows that the number of non functional pumps within each pump type does not follow the same trend as the total number of pumps. The pump type with the highest number of non functional pumps is, not surprisingly, `gravity`. The second however is `other` with 5,195 non functional pumps. Of course `other` is likely a catch-all term for any pump that didn't fit into any of the other pump types. Therefore, we can't know exactly what type of pump it is. Interestingly, even though there are 1500+ more `nira/tanira` total pumps than `other`, `other` contains over 3,000 more non functional pumps than `nira/tanira`. The final column in the table shows the % of non functional pumps for that pump type. Although `nira/tanira` has the 3rd highest total of non functional pumps, proportionately it has the least amount of non functional pumps with only 25.7% of all `nira/tanira` pumps being non functinoal. Compare that to the `mono` pump which has 57.7% of all pumps being non functional! It will be interesting to see which pump type the model is best able to classify.

#### Table 2 | Highest Non Functional Pump Counts

<table style="width:100%">
<tr><th  style="text-align:center">extraction_type</th><th>functional</th><th  style="text-align:center">functional needs repair</th><th  style="text-align:center">non functional</th><th  style="text-align:center">% non functional</th></tr>
<tr><td  style="text-align:center">gravity</td><td  style="text-align:center">16048</td><td  style="text-align:center">2701</td><td  style="text-align:center">8031</td><td  style="text-align:center">30.0%</td></tr>
<tr><td  style="text-align:center">other</td><td  style="text-align:center">1029</td><td  style="text-align:center">206</td><td  style="text-align:center">5195</td><td  style="text-align:center">80.8%</td></tr>
<tr><td  style="text-align:center">nira/tanira</td><td  style="text-align:center">421</td><td  style="text-align:center">641</td><td  style="text-align:center">2092</td><td  style="text-align:center">25.7%</td></tr>
<tr><td  style="text-align:center">submersible</td><td  style="text-align:center">2626</td><td  style="text-align:center">227</td><td  style="text-align:center">1911</td><td  style="text-align:center">40.1%</td></tr>
<tr><td  style="text-align:center">mono</td><td  style="text-align:center">1082</td><td  style="text-align:center">129</td><td  style="text-align:center">1654</td><td  style="text-align:center">57.7%</td></tr>
</table>

#### Which type of pump serves the highest average number of people?

The average population for each pump type is summarized in Table 3. This factor should be directly related to how long a pump will remain functional since wear from repated use is usually the cause of a pump breaking down. These questions can help drive new features for the dataset to improve the model further. This is left as an exercise for future improvement of the model.

#### Table 3 | Popuation Per Pump Type
<table style="width:100%">
<tr><th  style="text-align:center">extraction_type</th><th  style="text-align:center">Population per pump</th></tr>
<tr><td  style="text-align:center">windmill</td><td  style="text-align:center">408</td></tr>
<tr><td  style="text-align:center">india mark iii</td><td  style="text-align:center">378</td></tr>
<tr><td  style="text-align:center">other - play pump</td><td  style="text-align:center">343</td></tr>
<tr><td  style="text-align:center">submersible</td><td  style="text-align:center">324</td></tr>
<tr><td  style="text-align:center">india mark ii</td><td  style="text-align:center">298</td></tr>
<tr><td  style="text-align:center">afridev</td><td  style="text-align:center">250</td></tr>
<tr><td  style="text-align:center">other - rope pump</td><td  style="text-align:center">214</td></tr>
<tr><td  style="text-align:center">other</td><td  style="text-align:center">210</td></tr>
<tr><td  style="text-align:center">swn 80</td><td  style="text-align:center">206</td></tr>
<tr><td  style="text-align:center">gravity</td><td  style="text-align:center">148</td></tr>
</table>

## Data Wrangling

There are a total of 7 feature columns with missing data which have to be dealt with. Luckily, all 7 of these feature columns are categorical of `object` pandas datatypes: none of the numerical columns have missing values. The missing categorical values are dealth with automatically when converting those features into dummy variables. Dummy variable conversion is necessary when working with categorical text data since training any model requires numerical datatypes. This method changes each unique value in the categorical feature column into its own binary column. An example is shown below where all missing values for the `waterpoint_type_group` feature are combined into a single binary feature column `waterpoint_type_group_nan`

#### Table 4 | Sample of Categorical Dummy Variables
<table style="width:100%">
<tr><th  style="text-align:center">id</th><th  style="text-align:center">date_recorded</th><th  style="text-align:center">funder_0</th><th  style="text-align:center">...</th><th  style="text-align:center">waterpoint_type_group_nan</th></tr>
<tr><td  style="text-align:center">69572</td><td  style="text-align:center">2011-03-14</td><td  style="text-align:center">0</td><td  style="text-align:center">...</td><td  style="text-align:center">0</td></tr>
<tr><td  style="text-align:center">8776</td><td  style="text-align:center">2013-03-06</td><td  style="text-align:center">0</td><td  style="text-align:center">...</td><td  style="text-align:center">0</td></tr>
<tr><td  style="text-align:center">34310</td><td  style="text-align:center">2013-02-25</td><td  style="text-align:center">0</td><td  style="text-align:center">...</td><td  style="text-align:center">0</td></tr>
<tr><td  style="text-align:center">67743</td><td  style="text-align:center">2013-01-28</td><td  style="text-align:center">0</td><td  style="text-align:center">...</td><td  style="text-align:center">0</td></tr>
<tr><td  style="text-align:center">19728</td><td  style="text-align:center">2011-07-13</td><td  style="text-align:center">0</td><td  style="text-align:center">...</td><td  style="text-align:center">0</td></tr>
</table>

Some of the feature columns are either duplicates or derived from other feature columns and getting rid of these will help reduce the total number of features. The features that were dropped from the dataset were:

1. `recorded_by`
2. `payment_type`
3. `quality_group`
4. `quantity_group`

I introduced one new engineered feature by computing the number of years between the pump's year of installation and the year that the datapoint was recorded. This is another feature I think will have good correlation to the predicted class since the build materials for the pump will degrade with time and weathering. 

## Results

To evaluate the performance of my models, I chose the precision, recall, and f1-score metrics. These are defined as follows:

$$Precision = \frac{True Positive}{(True Positive + False Positive)}$$

$$Recall = \frac{True Positive}{(True Positive + False Negative)}$$

$$F1 = \frac{2 * (Precision * Recall)}{(Precision + Recall)}$$

Precisiona and recall are favored over the true positive and true negative rates when there is a significant class imbalance, as in this case. The F1-score is the harmonic mean of precision and recall and gives us the ability compare a single metric for one model to another. 

Logistic Regression models are popular for simple classification tasks due to their simplicity in both implementation and interpretation. I first created a logistc regression model to compare the performance of a random forest model to. I used a Bayesion optimization algorithm to find the optimal value of the regularization parameter C and obtained the results below. 

#### Table 5 | Results of Optimized Logistic Regression Model
<table style="width:100%">
<tr><th  style="text-align:center">metric</th><th  style="text-align:center">functional</th><th  style="text-align:center">functional needs repair</th><th  style="text-align:center">non functional</th></tr>
<tr><td  style="text-align:center">precision</td><td style="text-align:center">0.721376</td><td style="text-align:center">0.154448</td><td style="text-align:center">0.638251</td></tr>
<tr><td  style="text-align:center">recall</td><td style="text-align:center">0.561340</td><td style="text-align:center">0.526316</td><td  style="text-align:center">0.548136</td></tr>
<tr><td  style="text-align:center">f1-score</td><td style="text-align:center">0.631375</td><td style="text-align:center">0.238815</td><td  style="text-align:center">0.589771</td></tr>
<tr><td  style="text-align:center">classification_rate</td><td  style="text-align:center">0.553770</td></tr>
</table>

In Table 5, precision, recall, and f1-score are calculated for each class in a 1 vs all fashion. The results are drastically different between classes. The precision was highest for the `functional` class (0.72) and lowest for the `functional needs repair` class (0.15). This isn't a surprise since the dataset has 32,000 `functional` samples to train on and only 4,300 samples of the `functional needs repair` class. In addition to the large class imbalance, the `functional needs repair` class may simply be intrinsically harder to predict given the same set of predictor variables.

The recall scores are very similar between all 3 classes. The results imply that the model varies widely in the number of false positives between classes but is relatively equal in the number of false negatives between classes.

The classification rate is simply defined as the overall accuracy of the model and is what the DrivenData competition uses to score submissions. The classification rate for this model is 55.4%, so we have an average of 55.4% accuracy in predicting each class correctly.

#### Table 6 | Results of Optimized Random Forest Model
<table style="width:100%">
<tr><th style="text-align:center">metric</th><th  style="text-align:center">functional</th><th  style="text-align:center">functional needs repair</th><th  style="text-align:center">non functional</th></tr>
<tr><td  style="text-align:center">precision</td><td  style="text-align:center">0.813756</td><td  style="text-align:center">0.576444</td><td  style="text-align:center">0.841199</td></tr>
<tr><td  style="text-align:center">recall</td><td  style="text-align:center">0.891874</td><td  style="text-align:center">0.357193</td><td  style="text-align:center">0.786270</td></tr>
<tr><td  style="text-align:center">f1-score</td><td  style="text-align:center">0.851026</td><td  style="text-align:center">0.441075</td><td  style="text-align:center">0.812808</td></tr>
<tr><td  style="text-align:center">classification_rate</td><td  style="text-align:center">0.812825</td></tr>
</table>

The results for an optimized random forest model are summarized in Table 6. The random forest model clearly outperforms the logistic regression model. The overall classification rate went from 55.4% to 81.3%! Precision and recall improved significantly for the `functional` and `non functional` classes. Precision also improved for the `funtional needs repair` class, but at the expense of a lower recall. The overall f1-scores for each class improved over previous values so we can confidently say this model works better than logistic regression for this specific problem. 

#### Figure 2 | Precision-Recall for Non Functional Class
<script src="https://cdn.plot.ly/plotly-latest.min.js"></script>
<iframe width="100%" height="600" frameborder="0" scrolling="no" src="/images/0005-water-tanzania/precision_recall_combined.html"></iframe>

The precision-recall curves for the `non functional` class are shown in Figure 2. To decide where along the curve we want to operate, we need to consider the business problem we're trying to solve and the consequences of prioritizing precision over recall and vice versa. Having a high precision means we minimize false positives. In the case of the `non functional` class, false positives are misclassifications of `functional` pumps as being `non functional`. The consequence of misclassifying a `functional` pump is that we waste resources in deploying a materials and a repairman to the pump. However, the underlying assumption here is that there is currently no way to know a pump's functional status without actually deploying someone to physically check it, in which case we would have to physically check the status of every pump. So if we can reduce the number of pumps we have to check, we are already better than the baseline. For example, a precision of 50% implies that for every 2 pumps that we check, we can be sure that at least one of them is `non functional`, thus warranting our deployment of resources for a replacement.

In order to maximize recall, false negatives must be minimized. False negatives in the case of the `non functional` class occur when a `non functional` pump is classified as either `functional` or `functional needs repair`. Both of these misclassifications implies the pump is still working and is not in immediate need of replacement.

I would like to argue that the critical metric we want to maximize is the recall of the `non functional` class. If a `non functional` pump is misclassified, an effort will not be made to replace the broken down pump and hundreds of people can go without that water source for weeks or even months. They would likely be forced to find other sources of water which could be less sanitary or much farther away. On the otherhand, the consequences of misclassifying a `functional` or `functional needs repair` are not critical to human life.

### A Case Study

Ultimately, the Tanzanian Water of Ministry and local organizations employing this research will have to decide how they want to balance the precision and recall of this model. Let's assume we want to maintain a recall of 95%. This means that for every 100 `non functional` pumps, we will only misclassify 5 of them. From Figure 2 we can see that a 95% recall corresponds to a 50% precision. Let's use these figures along with the actual number of `functional` and `non functional` pumps in the dataset to come up with some numbers.

In the full training dataset, there are 22,824 `non functional` pumps out of a total of 59,400 pumps. With a recall of 95%, we would correctly classify 21,683 pumps and only misclassify 1,195 pumps. Since the precision will be locked to 50% for our chosen recall, we would have to deploy replacement resources to double the total number of `non functional` pumps in order to achieve a 95% recall, or 45,648 pumps. Compare this to the current state in which the status of a pump can only be known by physically checking each pump. Assuming the chance of encountering a `non functional` pump is equal to the proportion of `non functional` pumps, 56,466 out of the 59,400 pumps would have to be physically checked to match the number of `non functional` pumps that this model correctly identifies. The model would thus reduce the number of pumps that have to be checked by 10,818 in this sample, which corresponds to a 19.2% savings in time, money, and manpower!

### Savings Calculator

The proportion of `non functional` pumps is assumed to be more or less constant across Tanzania and more broadly across sub-saharan Africa, as reported in the literature<sup>4</sup>. I've developed a simple excel sheet calculator for determining the savings this model can offer over the current state of needing to survey every pump's functional status. The `savings_calculator.xls` can be accessed from the home repository <a href='https://github.com/Kennfucius/water_pumps_tanzania'>here</a>.

#### Figure 3 | Savings Calculator
<script src="https://cdn.plot.ly/plotly-latest.min.js"></script>
<iframe width="100%" height="300" frameborder="0" scrolling="no" src="/images/0005-water-tanzania/savings-calculator.png"></iframe>

An example of the calculator is shown in Figure 3. Assuming an organization is responsible for 10,000 pumps and there is an average cost of $50 to survey each pump (employee compensation, fuel, etc.), the organization could realize a savings of $96,000. The average price of a new Afridev or India Mark II pump is between $1,427-$1,585. The average cost of a new pump deployment including labor, digging, etc., is $3,800<sup>6</sup>. At this rate, the savings of $96,000 could be used to deploy 25 brand new pumps. This would be enough to support 7,500 people<sup>3</sup>!

## Conclusions

I designed a random forest classification model to predict the functional status of water pumps in Tanzania. I employed a Bayesion optimization algorithm to tune the hyperparameters. The model achieved a precision of 0.84 and a recall of 0.79 on a test dataset, while the overall classification rate was 0.81. Using the proportion of `non functional` pumps in this sample, I computed an average savings in resources of 19.2% over the current state. This savings could be used to deploy additional pump replacements in places with critical need. I also created a simple calculator to help organization involved in tackling the water crisis to estimate the savings they could realize if employing the model presented in this work.

Finally, I explored several interesting questions in the EDA section that motivate the engineering of new features. For example, I computed that the `mono` pump type is `non functional` 57.7% of the time while the nira/tanira pumps are `non functional` only 25.7% of the time. I also showed that the average population served by each pump type can vary anywhere between 148 and 408 people. These important factors related to each pump type may significantly contribute to the model's predictive power. This is left for future work.

## References

1. https://www.un.org/development/desa/dpad/wp-content/uploads/sites/45/publication/ldc_list.pdf
2. https://www.un.org/development/desa/dpad/least-developed-country-category.html
3. https://washwatch.org/en/countries/tanzania/summary/statistics/
4. https://www.theguardian.com/global-development-professionals-network/2016/mar/22/how-do-you-solve-a-problem-like-a-broken-water-pump
5. https://en.wikipedia.org/wiki/India_Mark_II
6. http://www.ropepumps.org/uploads/2/9/9/2/29929105/rope_pump_-_piston_pumps._comp._study_tz.pdf