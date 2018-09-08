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
In part 1 of this series, I will walk through building the collaborative filtering model in python. Training, optimizing, and analyzing the model will follow in subsequent posts.

## Background

### Data
The data I'll be using comes from boardgamegeek.com. The raw data has userID, gameID, and ratings columns as in the sample below.

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

Figure 1 shows the distribution of all user ratings on a semilog plot. The median rating is 7.0. The ratings are clearly quantized at integer and half integer levels. A closer examination reveals further quantized levels at every quarter rating. Below the quarter interval the ratings distribution becomes more continuous.

### The Model

The specific type of collaborative filtering model I want to build is commonly referred to as model based, as opposed to content based. The goal is for the neural network to find underlying features of the data that make good predictors of user ratings for a given game. Neural networks are often called black box models because we don't actually know what these underlying features are. An intuitive assumption is to think of these features as common properties we would associate with board games such as genre, number of players, recommended age, average length of game, etc. If a user is asked to rate a game they've never played before, it's natural to think these properties would play an important role in the user's rating.

#### Figure 2 | Matrix Implementation
![Matrix Implementation]({{ "/images/0003-recommender-system/model.png" | absolute_url }})

Implementation of the model in python is best optimized using matrix algebra which greatly speeds up calculations that must be performed iteratively. The matrix implementation is illustrated in Figure 2. The data is described by a users matrix and a games matrix. These matrices have a common dimension N_features. These are the features that the model will learn and each individual user or game will be described by a vector of these features. The number of features is a model parameter that we define and tune like the number of neurons in neural network models. To generate a prediction matrix of all users' predictions on all games, we perform a matrix multiplication as illustrated.

#### Table 2 | Sparse Matrix

| userID/gameID | 3 | 5 | 10 | 11 | 12 |
| :---: |
| 83 | NaN | NaN | NaN | NaN | 8.0 |
|	119 | 7.0 | 7.0 | NaN | 8.0 | NaN |
|	144 | NaN | NaN | NaN | 7.0 | NaN |
|	156 | 7.5 | 6.5 | NaN | 7.0 | 8.0 |
|	186 | 7.0 | NaN | NaN | NaN | 8.0 |

We can easily check our prediction matrix against the actual data by pivoting the original data in Table 1 to look like the sparse matrix in Table 2.

## Results

## Summary
