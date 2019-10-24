# Subreddit Classification
#### Using classification algorithms as a Subreddit spam detector
_Author: Ritchie Kwan_

---

## Table of Contents

0. [Problem Statement, Assumptions, Executive Summary](code/01-Gathering-Data.ipynb)
1. [Data Collection](code/01-Gathering-Data.ipynb)
2. [Data Cleaning & EDA](code/02-Data-Cleaning-and-EDA.ipynb)
3. [Benchmark Model](code/03-Benchmark-Model.ipynb)
4. [Model Tuning](code/04-Model-Tuning.ipynb)
5. [Evaluation and Conceptual Understanding](code/05-Evaluation-and-Conceptual-Understanding.ipynb)

## Presentation
[Subreddit Classification for Spam Detection](https://docs.google.com/presentation/d/1B9ZIjbuSkLH4oXGXfjwnv_1GK3AoNaqXi2Shfxvjjqw/edit?usp=sharing)

## Problem Statement

Spam and ham are typically used to describe junk and relevant emails, respectively. In the context of Reddit, any post unrelated to the subreddit can be considered "spam", while posts related to the subreddit are "ham". The integrity of a subreddit relies on the quality and relevance of its posts. The content of a spam post could be as bad as a hyperlink to a phishing website or as benign as a high-quality post accidentally submitted to the wrong subreddit.

A spam detection bot could be trained to automatically detect and remove irrelevant posts before they are released to the public. **The objective is to determine how the frequency of certain two-word phrases (bi-grams) in a post affect the odds that the post belongs to a specific subreddit by using Natural Language Processing (NLP) techniques.**

## Assumptions

The default page of each subreddit is sorted by `hot`, typically posts that have already acquired a sufficient amount of upvotes by users and possibly passed a spam detection bot. Since the posts collected using the Reddit API appear to be sorted the same way, I assume that **all posts collected using the Reddit API are ham**.

## Executive Summary

**Data Collection**  
I collected as many posts from `/r/leanfire` and `/r/fatfire` as Reddit would allow. In addition to `title` and `selftext`, I also collected `comments` to train my models. Since I collected more posts from `/r/leanfire`, it will be my target class `y=1` (ham). Posts from `/r/fatfire` will be used to imitate spam for the sake of training classification models.

**Data Cleaning and EDA**  
I extracted text from the `title`, `selftext`, and `comments` columns to use as my predictors. I used `RegExpTokenizer` to split the text into individual words and `Lemmatizer` to reduce words to their lemma.

**Benchmark Model**  
I train test split my data to check for overfitting. I used `CountVectorizer` and `TfidfVectorizer` to map bi-grams to their frequency or weighted frequency in each post. For my benchmark model, I used `LogisticRegression`. For the training data, I used **accuracy to measure success** of the model. Not only did my model perform better using the `TfidfVectorizer` transformed data, the highest ranked bi-grams were also more meaningful.

**Model Tuning**  
I used the following classification models with and without tuning hyperparameters:
- LogisticRegression
- KNN
- NaiveBayes
    - MultinomialNB
- DecisionTreeClassifier
- BaggingClassifier
- RandomForestClassifier
- ExtraTreesClassifier
- AdaBoostClassifier
- GradientBoostingClassifer
- SVC
- VotingClassifer

With or without hyperparametering tuning, Naive Bayes' `MultinomialNB` performed the best in terms of variance.

**Evaluation and Conceptual Understanding**  
Unseen data will be newly submitted posts on `/r/leanfire`, meaning all new data belongs to the positive class (`y=1`). If the model classifies a post as negative (`y=0`), then it is likely to be spam.  

True Positives = Ham  
False Negative = Spam  

To maintain the integrity of the subreddit, I prefer the model to have a **sufficiently high False Negative rate** (spam detection rate). The consequence of this decision is that **some quality posts may be blocked**. This is preferred over **letting actual spam bypass the detection algorithm**.
