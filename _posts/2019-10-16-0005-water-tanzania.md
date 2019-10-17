---
title: "Improving Access to Clean, Safe Drinking Water in Tanzania"
date: 2019-10-16
tags: [Social Impact, Multinomial Classification, Logistic Regression, Random Forest]
header:
  overlay_image: "/images/0002-keras-ETF/0002-banner1-small.jpg"
  overlay_filter: 0.5
  teaser: "/images/0002-keras-ETF/0002-banner3.jpg"
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
59,400 unique water pumps described by 39 features comprise the dataset. Each water pump is represented exactly once in the dataset and thus the shape of the training data is 59,400x39. Figure 1 below illustrates the large class imbalance with the following breakdown: 54.3% - functional, 7.3% - functional but require repair, 38.4% - non functional.

#### Figure 1 | Pump Status Classes
<script src="https://cdn.plot.ly/plotly-latest.min.js"></script>
<iframe width="100%" height="550" frameborder="0" scrolling="no" src="/images/0005-water-tanzania/pump-conditions.html"></iframe>

The majority of pumps use gravity as the pumping mechanism. 

We have no description of one columns, num_private. There are a total of 7 feature columns with missing data which will either need to be imputed or rows will need to be dropped for a dense training matrix. Some of the feature columns are either duplicates or derived from other feature columns and getting rid of these will help reduce the total number of features. The majority of columns are categorical with string datatypes and will need to be converted to numeric or dummy variables. I think we have a good understanding of the data to start munging it and preparing it for the Random Forest model. So let the munging begin!

## Conclusions
Two very common pumps are the Afridev ($1,427) and the India Mark II ($1,585). Installing a new pump incurs additional costs for digging the whole, building a housing for the pump, and labor. It's estimated that an average pump installation costs around $3,800 USD, including the pump. 



## References

1. https://www.un.org/development/desa/dpad/wp-content/uploads/sites/45/publication/ldc_list.pdf
2. https://www.un.org/development/desa/dpad/least-developed-country-category.html
3. https://washwatch.org/en/countries/tanzania/summary/statistics/
4. https://www.theguardian.com/global-development-professionals-network/2016/mar/22/how-do-you-solve-a-problem-like-a-broken-water-pump
5. http://www.ropepumps.org/uploads/2/9/9/2/29929105/rope_pump_-_piston_pumps._comp._study_tz.pdf