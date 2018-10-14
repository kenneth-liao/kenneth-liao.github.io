---
title: "Recommender System: Part 1 - Building The Model"
date: 2018-09-03
tags: [Recommender System, Collaborative Filter, Prediction]
header:
  overlay_image: "/images/0003-recommender-system/0003-banner-medium.jpg"
  overlay_filter: 0.5
  teaser: "/images/0003-recommender-system/0003-banner-medium.jpg"
excerpt: "Building a recommender system using a collaborative filtering model"
mathjax: "true"
# This sets the table of contents on the right of the page
toc: true
toc_label: "Table of Contents"
toc_icon: "thumb_up"
toc_sticky: "true"
---

## Objective
In part 2 of this series,

## Background



### The Model


## Results


#### Figure 5 | Cross Validation Results
![CV Results]({{ "/images/0003-recommender-system/predictions_vs_cv.png" | absolute_url }})

Figure 5 shows a similar plot where this time we're comparing predicted ratings to actual ratings in the CV data.

#### Figure 6 | Learning Curves
![Learning Curves]({{ "/images/0003-recommender-system/learning curves.png" | absolute_url }})

It is often useful to plot learning curves as a function of data sample size. This helps determine if more or less training data affects the model's performance. The learning curves above show the error of the model on the training and CV data sets as a function of sample size. As the data set approaches 100% of the available training data, the CV data continues to decrease. It does appear to flatten out towards the end, indicating that providing the model with more data may not significantly improve performance. Another striking result of this plot is the gap between the training error and the CV error. This large gaps suggest the model does not generalize well to the unseen CV data.

## Summary
