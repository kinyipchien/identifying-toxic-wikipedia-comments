See my [notebook](https://github.com/kchien3/toxic-comments-project/blob/master/notebooks/toxic_comments_project.ipynb) for now.

# Automated Moderation
**Identifying Toxic Comments**

## Andrew Kin-Yip Chien
[Linkedin](https://www.linkedin.com/in/andrew-k-chien/) | [Github](https://github.com/kchien3) | [Slides](https://github.com/kchien3/toxic-comments-project/blob/master/presentation/toxic_comments_project-slides.pdf)

## Table of Contents

* [Background and Motivation](#background-and-motivation)
* [Data](#data)
  * [Description](#description)
* [Feature Engineering](#feature-engineering)
  * [Text Vectorization](#text-vectorization)
* [Exploration](#exploration)
  * [Important Words](#important-words)
  * [Distributions](#distributions)
* [Modeling](#modeling)
  * [Class Imbalance](#class-imbalance)
* [Revenue Maximization Strategies](#revenue-maximization-strategies)
* [Conclusions](#conclusions)
* [Future Directions](#future-directions)
* [References](#references)

## Background and Motivation
Comments sections abound on various websites to facilitate discussion and engagement between users. However, toxic and abusive comments can quickly derail discussions and cause users to permanently refrain from commenting in the future and the scale of the problem is huge.

[11,000 comments](www.nytimes.com/interactive/2016/09/20/insider/approve-or-reject-moderation-quiz.html) are posted to the New York Times website every day. Over 160 million comments are posted to Reddit every month. Websites employ full-time human moderators to enforce policies of civility but the job [negatively affects](https://www.theverge.com/2019/2/25/18229714/cognizant-facebook-content-moderator-interviews-trauma-working-conditions-arizona) their mental health.

Machine learning methods can provide solutions that limit moderators' exposure to damaging content and moderate consistently and efficiently at scale. In this project I will build an auto-moderator, in other words, a model to classify whether comments are toxic or not.

## Data
### Description
160,000 comments from Wikipedia discussion pages were annotated by up to 10 human raters through the crowd-sourcing platform Crowdflower. Comments were rated on whether they were toxic or not (meaning whether they were likely to make people leave a discussion.) Additionally, five toxic subtypes that are not mutually exclusive were also annotated (severe toxic, obscene, threat, insult, identity hate), but the features were not used to build models in this project.  

This project uses a [modified kaggle competition version](https://www.kaggle.com/c/jigsaw-toxic-comment-classification-challenge/data) of the data set in order to benchmark model performance, but the unmodified data is [available](https://figshare.com/articles/Wikipedia_Talk_Labels_Toxicity/4563973) from Wikipedia.

## Feature Engineering
Text must be converted into numeric features for machine learning methods to train on.

41 features were manually created, consisting of counts of sets of characters, proportions of sets of characters, counts and proportions of words, counts of lines, and average lengths of words and lines in each comment.

### Text Vectorization
Another way to convert text into numeric data is by vectorization. Word or character level n-grams can be converted into count vectors or tf-idf vectors.  

#### Term Frequency-Inverse Document Frequency
<div align='center'>
<img src='img/tf_idf_equation.png'>
</div>

Term frequency-inverse document frequency is a statistic that reflects how important a word is to a document in a corpus. The more frequent a word is within a document and the fewer the documents the word appears in, the higher its tf-df value.

After appropriate train-test splitting of the data set, tf-idf vectorization resulted in a vocabulary close to 60,000 -- in other words, 60,000 features for models to train on.

## Exploration
### Important Words
Tf-idf vectorization was used to rank word importances within the toxic and nontoxic comment classes. The top 10 unique words within each class are (not ordered by importance):  
Toxic: ass, bitch, dick, don', f\*\*k, f\*\*\*ing, like, s\*\*t, stop, stupid, suck.  
Nontoxic: article, good, help, new, section, thank, time, use, 've, wp.  

There is already a clear pattern in the language used in toxic versus nontoxic comments.  

### Distributions
I would like to highlight two interesting differences between toxic and nontoxic comments.

One:
<div align='center'>
<img src='img/count_char.png'>
</div>
It can be observed that toxic comments (green) tend to be shorter (have fewer characters) than nontoxic comments (black). But toxic comments are also longer, as can be seen by the small peak at the right edge of the graph. Long comments (5000 characters) tend to be strings repeated until the character limit is reached.

</br>
</br>
Two:
<div align='center'>
<img src='img/prop_uppercase.png'>
</div>
It can be observed that toxic comments (green) tend to contain a higher proportion of uppercase characters than nontoxic comments (black). All capitalized text typically indicates shouting and it makes sense that toxic comments have an associated shouting/angry tone.

<br/>
Only tfidf values were used as features for modeling.

## Modeling
### Train/Test Split
The comments were split into a 75% training fold and 25% testing fold to preserve the class imbalance in the test fold.

### Class Imbalance
Approximately 10% of the comments belong to the toxic class, with the remaining comments belonging to the nontoxic class. Highly imbalanced classes will lead to models that perform poorly at identifying the minority class unless the imbalance is specifically addressed. There are various methods for addressing imbalance, including upsampling, downsampling, and changing sample weights in model fit functions. Here we will downsample the majority class to produce a class-balanced training set.

### Logistic Regression

## References
* Etim, Bassey. “Approve or Reject: Can You Moderate Five New York Times Comments?” *New York Times* 20 Sept. 2016, www.nytimes.com/interactive/2016/09/20/insider/approve-or-reject-moderation-quiz.html
* Newton, Casey. “The Secret Lives of Facebook Moderators in America.” *The Verge* 25 Feb. 2019, www.theverge.com/2019/2/25/18229714/cognizant-facebook-content-moderator-interviews-trauma-working-conditions-arizona.
