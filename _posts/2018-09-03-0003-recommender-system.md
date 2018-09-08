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
In part 1 of this series, I will walk through building the collaborative filtering model in python.

## Background

### Data
The data we'll be using comes from boardgamegeek.com. The raw data has userID, gameID, and ratings columns as in the sample below.

#### Table 1 | Raw data
| userID | gameID | rating |
| :---: |
| 83 | 39938 | 7.0 |
|	83 | 34219 | 7.0 |
|	83 | 24068 | 7.0 |
|	83 | 70149 | 8.0 |
|	83 | 43015 | 9.0 |

#### Figure 1 | Ratings Distribution
![Ratings Distribution]({{ "/images/0003-recommender-system/ratings-distribution.png" | absolute_url }})

Figure 1 shows the distribution of all user ratings on a semilog plot. The median rating is 7.0. The ratings are very clearly quantized at integer and half integer levels. A closer examination reveals further quantized levels at every quarter rating. Below the quarter interval the ratings becomes more continuous.

### Data Munging

#### Table 2 | Sparse Matrix
| userID/gameID | 3 | 5 | 10 | 11 | 12 |
| :---: |
| 83 | NaN | 7.0 | NaN | NaN | NaN |
|	119 | NaN | 7.0 | NaN | NaN | NaN |
|	144 | NaN | 7.0 | NaN | NaN | NaN |
|	156 | NaN | 7.0 | NaN | NaN | NaN |
|	186 | NaN | 7.0 | NaN | NaN | NaN |

## Results

## Summary
