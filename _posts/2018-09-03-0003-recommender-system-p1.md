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
In part 1 of this series, I will demonstrate how to build a collaborative filtering model in python. Training, optimizing, and analyzing the model will follow in subsequent posts.

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

The specific type of collaborative filtering model I want to build is commonly referred to as model based, as opposed to content based. The goal is for the model to find underlying features of the data that make good predictors of user ratings for a given game. This is sort of a black box approach because we don't actually know what these underlying features are. An intuitive assumption is to think of these features as common properties we would associate with board games such as genre, number of players, recommended age, average length of game, etc. If a user is asked to rate a game they've never played before, it's natural to think these properties would play an important role in the user's rating.

#### Table 2 | Sparse Matrix

| userID/gameID | 3 | 5 | 10 | 11 | 12 |
| :---: |
| 83 | NaN | NaN | NaN | NaN | 8.0 |
|	119 | 7.0 | 7.0 | NaN | 8.0 | NaN |
|	144 | NaN | NaN | NaN | 7.0 | NaN |
|	156 | 7.5 | 6.5 | NaN | 7.0 | 8.0 |
|	186 | 7.0 | NaN | NaN | NaN | 8.0 |

The first step to building the model is to get the data into the right form. We start by pivoting the original data table so that it looks like the sparse matrix in Table 2. This will make comparing predictions easy since we can conveniently use matrix algebra to compute all rating errors in a single line of code. Once we have the sparse matrix, we need to split the data into training and cross-validation (CV) data sets.

#### Figure 3 | Training Data
![Training Data]({{ "/images/0003-recommender-system/X_train.png" | absolute_url }})

20% of the data is reserved for cross-validation while 80% of the available data will be used for training. In pandas, this is easily done using the .sample(frac=0.2) method where the frac argument indicates we want to sample 20%. The corresponding values in the original dataframe is then blanked to form the training data set (Figure 3).

#### Figure 2 | Matrix Implementation
![Matrix Implementation]({{ "/images/0003-recommender-system/model.png" | absolute_url }})

Implementation of the model in python is best optimized using matrix algebra which greatly speeds up calculations that must be performed iteratively. The matrix implementation is illustrated in Figure 2. The data is described by a users matrix and a games matrix. These matrices have a common dimension N_features. These are the features that the model will learn and each individual user or game will be described by a vector of these features. The number of features is a model parameter that we define and tune like the number of neurons in neural network models. To generate a prediction matrix of all users' predictions on all games, we perform a matrix multiplication as illustrated.

#### Figure 3 | Training Code
![Training Code]({{ "/images/0003-recommender-system/training_code.png" | absolute_url }})

```python
args = cofi.get_args(X_train, N_features=250, Lambda=1)
parameters = cofi.unroll_params(args[0], args[2])

results = fmin_cg(cofi.cost_f, parameters, cofi.grad_f, args=args,
                 full_output=True, maxiter=100)
```

## Results

#### Figure 3 | Feature Dependence
![Feature Dependence]({{ "/images/0003-recommender-system/Nfeature-dependence.png" | absolute_url }})

Figure 3 shows the model's error dependence on the number of features, N. As the number of features increases, the model's error decreases. This is exactly what one should expect. If we attempt to predict user ratings of games given only the recommended age of the games, you can imagine the results would be quit unreliable. If however, we are given the game's recommended age, genre, number of players, game length, etc., the results should be more accurate.

#### Figure 4 | Training Results
![Training Results]({{ "/images/0003-recommender-system/predictions_vs_training.png" | absolute_url }})

Figure 4 shows the results of training the model. The predicted ratings are plotted against the actual ratings in the training data set. This gives a visual indication of how well the model fits the data based on the spread of points from the line with slope unity. If the model predicts the ratings perfectly, the points will all lie exactly on the black line. Of course, such a model would likely be prone to overfitting so including a regularization term can help mitigate overfitting.

## Summary

In this part of the recommender system series, I demonstrated a basic collaborative filtering model for predicting user ratings of board games. The model is able to fit the training data well. The learning curves (Figure 6) suggest the model is overfitting the training data. The predicted vs actual ratings in the CV data set (Figure 5) show that the model tends to be significantly less accurate at ratings above and below the overall mean rating. In the next part of the series, I will explore different ways to optimize this model.
