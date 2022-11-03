# Extraterrestrial Index: The measure of Extraterrestriality

Authors: IndexError, Polytetral

## Introduction

### Background and Importance

As a generalized variant of the Vertical Scrolling Rhythm Game (VSRG) gamemode, osu!mania is widely played by players from diverse VSRG backgrounds. The osu!mania tournament scene in particular, is constantly changing and rapidly increasing in terms of difficulty; as we start to see a larger influx of players from different VSRG variants, the average skill levels of players start to increase exponentially. This can be attributed to increased accessibility to charts ported from other VSRGs that lead to more focused training and better physicality overall.

This creates a larger disparity between top players who are veterans to the annual osu!mania World Cup (MWC), and average players who may be looking to break the ranks but lack the physicality or resources to do so. 
As such, rank restricted tournaments are initiatives to make tournament play more accessible to players who may not have a chance to participate in official or larger community tournaments. They of course use rank as a metric to determine who gets to play in such tournaments. In the case of 4 Digit MWC, a registrant must not exceed the global rank of #250 and the 4K rank of #1000 to be cleared for tournament play. 

However, rank as a metric implies the complete reliance on the Performance Points (PP) system, which is inherently flawed for osu!mania. Specifically in osu!mania, the gamemode encompasses game mechanics that exist from other VSRGs that already have a loyal playerbase, such as O2Jam and Etterna. The performance ability of players from such games may not necessarily be well tracked by the Performance Points system, which requires players to play specific maps that are ranked in osu!mania.

On the same line of argument, most players practicing for tournament play go to maps that originate from other games (such as FFR charts), which in relation are normally unranked in osu!mania. This makes it impossible to account for the improvement in performances of players especially if they take place underground. This can also sometimes be done in deliberation, by opting to play offline, though this is of another issue on moral integrity that should not be covered in the span of this article.

Hence, there exists a “deranking” phenomenon in osu!mania where a player (un)intentionally deranks which ensures their rank in osu!mania will strictly be lower than the rank restriction. Such derankers may deny participation for other players who are better suited for intermediate-level gameplay, especially if the derankers are significantly more experienced and perform well in tournaments. This is also especially more prevalent in a country-based competition, since some countries are larger than others in population and have more well-developed players than others.

With concerns for “derankers” depriving opportunities for other players to participate in 4 Digit MWC, a large wave of skillbans were applied to the iteration for 2022, consisting of the following:
- Bans to the top 3 of the 2021 edition;
- Bans to the champions of every edition;
- Bans to all members in the top 12 teams of MWC 2021 and 2020;
- Other handpicked bans that are considered warranted by the organizational team.

Of course, skillbans that rely on blanket rules are also inherently flawed, since they are also known to deny entry to less skilled players that belonged to a podium team with a “deranker”. The latter includes:
- Some players from podium teams may not have improved in physical ability relative to other newer players who may have never shown up to a tournament ever before; 
- One-trick players who excel in a single skillset who exist in these podium/top 12 teams may also not be representative of the skill level of their teams. This is especially apparent in SV specialists being banned from 4DM for simply belonging to a podium team;
- Personal bias in handpicks, where the outcome of bans and the tournament all rely on the limited knowledge that the organizational team possesses about all 500 signees of the tournament.

In an effort to counteract false positives generated by the blanket ban and handpicking bias for and against specific players, a mathematical model will be implemented across all signees for 4DM2023. This model will process every single registration for 4DM2023 and determines if a player should be skillbanned based on data collected from various tournaments and ranked/loved leaderboards. Consequently, a custom metric for 4DM will be introduced to do so.

### Defining "Outliers"

In order to perform a skillban, we first need to define what "outlier" means in our context. The "outlier" in this context means the players who outperform the other players in the same rank range in a rank-restricted Rhythm Game tournament. In this case study, we will perform the Outlier Detection on a country-based team 3v3 tournament which has the rank restriction between 1000-9999, targeting to the individual players.

### Challenges

The dataset is the most challenging part due to the scores are seperated between various maps. Namely there is no clear intersection between players who play one map and players who play other maps. The data provided for us were the score records of **4 Digit MWC 4**, **4 Digit MWC 3**, **SOFT6**, **MWC2021** and **MWC2022** and dataset of **Ranked and Loved maps**. There are tournaments that are in the same level and some in the different level, but all they have in common is the diversity of players in a tournament, including 4 digits. The target is to connect and find the relationship of these datasets together and detect the outliers of 4 digit via these relationships.

## Important Definitions

**Score**: means the score of a player, normally they are in the range of 0 to 10^6. We will instead denote a score as a logit transformation of score, defined by:

$$
\text{score} = \ln \left(\frac{x}{10^6 - x}\right)
$$

Where $x$ is a raw score of a player

## Exploratory Analysis

The Exploratory Analysis of Historical 4 Digit MWC 4 can be found in the [Github Repository](https://github.com/4digitmwc/4dmanalysis). There are the analysis and algorithm attempts there, and the major analysis are as follows:

- Linear Regression and Confidence intervals of 4 Digit MWC 4 Scores depending on Round: [here](https://github.com/4digitmwc/4dmanalysis/tree/main/journal)
- Survival Analysis of teams according to Qualfiers Scores: [here](https://github.com/4digitmwc/4dmanalysis/blob/main/4dm4analysis/survival.ipynb)
- Analyzing the chance of Survival in each round according to Qualifiers Scores using Logistic Regression Approach: [here](https://github.com/4digitmwc/4dmanalysis/blob/main/4dm4analysis/survival_log.ipynb)

## Extraterrestrial Index for Tournament Outlier Detection

**Extraterrestrial Index** (ETI for short) is a metric defined specially for the study of outlier detection of the overall tournament(s) performances. The goal of ETI metric is to connect each tournament performance to each other. For example, if a player performed well in SOFT6, they might also perform (too) well in 4 Digit MWC 4 or vice versa. The ETI of each tournament can be connected to each other via a model which indicates the scaling of how a player will perform in another tournament given a performance in one tournament.

### Extraterrestrial Index in 4 Digit MWC 4

We then first study the ETI in our baseline tournament, **4 Digit MWC 4**. It is a rank-restricted tournament that has the quality mappool with fair distribution of the qualified participants. The goal is to provide the overview to the performance distribution of the players and overview of how to detect the outliers with this specified feature.

We then first consider using the **Average Standardized Score** for all maps with the assumption of where all scores of all players in a tournament is provided:

$$
\sum_{i \in \text{maps}} \frac{x_i - \bar{x}_i}{\hat{\sigma}_i n}
$$

This will, of course according to the assumption, provide the enough information for us whether a given player is outperforming in a tournament. However, due to the elimination, banning and the unpicked maps, there are censored scores appeared in a tournament, which can cause us a trouble to calculate the performance of a given player.

We then use the assumption that if a player didn't play a map, they got the minimum score in a map compared to other players. This will also be the indirect weightage for early game and late game scores, i.e. eliminated players will get less score compared to other players in a round, and calculated the minimum possible performance in a map in case a map is banned in a match played by a given player. We then get the following procedure for ETI:

- **Imputation**: We first impute the censored scores with the minimum scores of all players in a map.

- **Standardization**: We then scale the scores into the distribution with mean 0 and variance 1.

- **Evaluation**: We then average the standardized scores of each player, then multiply by the square root of the numbers of beatmaps in order to get the variance of 1 (check!)

We seperate the calculation according to the categories **RC**, **HB** and **LN**. Then we compute the ETI of each category which will denote the performance of a player in each category (assume a player who didn't play a category has the minimum ETI). After that we will get the 3 crucial information for each player in terms of performance.

We then calculate the average of ETI then observe the distribution:

![](https://cdn.discordapp.com/attachments/546525809440194560/1035205419934679060/unknown.png)

We can see explicitly that the distribution of ETI is skewed to the right due to the imputation of the scores according to the assumption. 

![](https://cdn.discordapp.com/attachments/546525809440194560/1035211631354990692/unknown.png)

We then obtained the top 15 players of 4 digit MWC 4 according to ETI in order to get an overview and manually verify if the certain feature is valid.

Let us observe further in the correlation of ETI between categories. In 4 Digit MWC 4 (4dm4 for short), we observe the correlation between ETI of each category and obtained the result:

![](https://cdn.discordapp.com/attachments/546525809440194560/1035207055071510528/unknown.png)

Where Row and Column 1,2,3 denote the category RC, HB and LN. We then can see that there is a strong correlation between HB with other categories, meanwhile there is a low-moderate correlation between RC and LN. We then observe further how each category is correlated to each other.

![](https://cdn.discordapp.com/attachments/546525809440194560/1035208984157761628/unknown.png)

This is the scatter plot between the ETI of RC and HB. We can see that there is a correlation between two categories and a leverage which also is an outlier from the linear regression of two categories.

![](https://cdn.discordapp.com/attachments/546525809440194560/1035209044241166376/unknown.png)

The same for the scatter plot between the ETI of LN and HB. We can also see a clear correlation with one leverage which is also considered to be an outlier in linear regression model of two categories.

![](https://cdn.discordapp.com/attachments/546525809440194560/1035208856952905738/unknown.png)

This is the scatter plot of the relation between ETI of RC and LN, we can see the increase of variances when the ETI of either beatmap category increases. This is called **Heteroscedasticity** in Linear Regression Model. However, a linear relationship is still be explicitly shown.

However we still cannot draw the boundary where a player should be skillbanned yet, which will be in the next procedure.

### Expansion to Major osu!mania Tournaments

Since we already know how to obtain the Extraterrestrial Index for a tournament in each category. The next task is to connect them together and determine the players who are the "Outliers". Of course that the outliers we mentioned about will not be the typical outliers in statistical definition but rather the outliers with some specific features which will be shown soon in this article.

### Idea Overview

From the resources we have, we got the data of major tournaments and some rank restricted tournaments with enough evidence to connect them together using 4dm4 players who participate each of these tournaments. We then use the Linear Model with Regularization in order to predict the ETI of 4dm4 tournament from other tournaments (connection). Then we distinguish the outlier candidates by performing the Logistic Regression over some rule-based classification to obtain the probability. Next, we use the data of those candidates to do the Local Outlier Factor with Adjusted Metric specifically for this task.

### Connection between Tournaments: Elastic Net Linear Regression

From the previous section that we can calculate the ETIs or the average performance compared to other players in a tournament. Next we will try to connect the dots together between tournaments. We chose the simple method called the "Linear Regression" to transform the ETI of each beatmap category in one tournament into the average performance in another tournament. In this case, we will convert the ETIs from other tournaments into ETI or average performance in 4dm4 tournament.

We can calculate the average performances for 4 Digit MWC 4 by:

$$
\text{ETI}_{\text{4dm4}} = \beta_0 + \beta_{1}\text{ETI}_{\text{RC}} + \beta_{2}\text{ETI}_{\text{HB}} + \beta_{3}\text{ETI}_{\text{LN}}
$$

In the 4dm4 case, the coefficients are as follows:

$$
\text{ETI}_{\text{4dm4}} = \frac{1}{3}\text{ETI}_{\text{RC}} + \frac{1}{3}\text{ETI}_{\text{HB}} + \frac{1}{3}\text{ETI}_{\text{LN}}
$$

In the general case, the coefficients $\beta_i$ can be calculated by minimizing the squared error in regression, namely **Ordinary Least Squares**:

$$
\min \sum \|y - X\beta\|^2
$$

However, in multiple linear regression, we also want to address the multicollinearity and remove unnecessary coefficients. Which can be done by using the Regularization or Elastic Net. This will transform our minimization problem which we are used for calculating coefficients into

$$
\min \left(\sum \|y - X\beta\|^2 + \lambda_1 \beta^T\beta + \lambda_2 \sum |\beta|\right)
$$

Which $\lambda_1$ and $\lambda_2$ are user-defined parameters. We use different parameters to address the data points between two tournaments listed below.

- 4 Digit MWC 3: $\lambda_1 = 0$, $\lambda_2 = 1$
- SOFT6: $\lambda_1 = 1$, $\lambda_2 = 0$
- MWC2021: $\lambda_1 = 0$, $\lambda_2 = 1$
- MWC2022: $\lambda_1 = 0$, $\lambda_2 = 0.5$

This will give us the reduced values of the coefficients $\beta$, which has the less size than the coefficients we obtained from Ordinary Least Squares method. After the prediction, we will get the ETI for all players (including players with higher rank than 4 digits) from each tournament. The next process is to select the candidates for the outliers, which will be specified in the next part.

### Outlier Candidates selection

After we compute the approximate ETI for every player who participates any tournament. We then select the 4 digit players whose fit the criteria of being an outlier. We propose the Rule-Based features then calculate the Lower Bound ETI that fits the criteria. Then we use the candidates list in the next process.

The candidates list will be beneficial for the skillbans process due to the severality in the sample size. We try to find the outperformers among the 4 digit players in the tournament. This process is conducted to reduce the misclassification between the outliers and high-level players.

We use the Logistic Regression to convert ETI into the probability that a player is a candidate for the skillbans. The formula for deciding can be written as follows:

$$
\ln\left(\frac{p_{\text{candidate}}}{1 - p_{\text{candidate}}}\right) = \alpha_0 + \alpha_1 x
$$

Where $x$ is the ETI of a given player. It can be dualized into the function:

$$
\text{candidate}(x) = \begin{cases}
0 & \alpha_0 + \alpha_1 x \leq 0 \\
1 & \alpha_0 + \alpha_1 x > 0 \\
\end{cases}
$$

We then define the Rule-Based criteria to classify between the skillbans candidates and other players, which are

- Has the current rank higher than 4 Digits
- Participated in **SOFT6**
- Advanced into at least **Quarterfinals** of MWC2021 and 2022

This criteria is for the labelling purposes, namely not all the players who fits these criteria will be considered in the skillbans candidates. But the players with the higher ETI than the players with these criterias will also be considered, including ones who participated in 4dm3 and/or 4dm4.

We then perform the simple logistic regression and obtained the candidates list, next we will use these candidates list to detect the outliers.

### Adjusted Local Outlier Factor

The algorithm we decided to use in the skillbans procedure is **Local Outlier Factor** it is the Outlier Detection Algorithm based on the comparison of density of a given data point and the neighborhoods. However, in the skillbans process we decided to add the metric to obtain the specific features from the outliers, namely the outliers who has the outstanding performance.

### Actual Definition of Outliers

We then consider the previous definition of outliers. Which is "the players who outperform the other players in the same rank range in a rank-restricted Rhythm Game tournament". It is necessary to translate the subjective definition into the mathematical notation. Which can be easily seen as detecting outlier with the constraint of $f(x) > t$. Where $f(x)$ is the transformation function, which necessarily is the increasing function of average performance (ETI) and $t$ is the unknown value which will be auto considered in the process. 

### Local Outlier Factor

We then consider the **Local Outlier Factor** algorithm. In order to get the Local Outlier Factor of a given data point $A$. We started by setting the number of nearest neighbors $k$ and compute the **Reachability Distance** 

$$
\text{rd}_k(A,B)=\max\{\text{k-distance}(B), d(A,B)\}
$$

Where $\text{k-distance}(B)$ is the distance from the point $B$ to its $k$-th nearest neighbors.

We then define the **Local Reachability Density** as the following formula

$$
\text{lrd}_k(A) = \frac{|N_k(A)|}{\sum_{B \in N_k(A)} \text{rd}_k (A, B)}
$$

Where $N_k(A)$ are the nearest neighbors of $A$. We can see that the local reachability density tend to decrease if $A$ is far away from the clusters (less density).

We then define the **Local Outlier Factor** of the point $A$ as the formula

$$
\text{LOF}_k(A) = \frac{\sum_{B \in N_k(A)} \text{lrd}_k (B)}{|N_k(A)| \cdot \text{lrd}_k (A)}
$$

Namely the Local Outlier Factor can be computed by comparting the density of the neighborhood with the density of the given data point. In other words, if there is less density in the data point, the Local Outlier Factor is going to be higher.

### Adjustments

Normally, the outlier detection algorithm we are using, including the Local Outlier Factor, use the metric of **Minkowski Metric**:

$$
d_p(x,y) = \left(\sum |x - y|^p \right)^{\frac{1}{p}}
$$

Where $p$ is the dimension we consider. Then if we choose $p=2$, it becomes the **Euclidean Distance**

$$
d(x,y) = \sqrt{\sum (x - y)^2}
$$

However, this metric will also consider the lower and the middle point as the outliers. As we want the specific features of the outliers being high-performed players, we then consider scale the ETIs down in range $[0, 1]$. Then we adding the terms to specify the features. Hence we have the following metric:

$$
d(x,y) = \sqrt{\sum (x - y)^2} + \lambda_1\|x\|_2 + \lambda_2 \|x\|_1
$$

Note that this adjusted metric is not commutative due to the addition of the 2nd and 1st Degree Norms. However, we use this metric to detect the outliers in the Local Outlier Factor procedure according to the ETI features from each tournament.

This metric will also redefine the "density" in Local Outlier Factor as not only how close the point is to the neighborhood, but also how close the point is to the origin. To compare, assuming that there is a valley at the origin and there is a village at the valley, it is difficult to climb up the hill to build a living place. As the slope of the hill increase (infinitely), those who lives at the hill are mostly extraordinary and isolated from the others.

We calculated the local outlier factor of the candidates, divided by each category, **RC**, **HB** and **LN** and tournaments **4dm4**, **4dm3**, **SOFT6**, **MWC2021** and **MWC2022**. Then predict the result according to each category. The outliers will be the players whose at least of their model prediction result is an outlier.

### In Layman Terms

To summarize all the procedures in Layman Terms, we start the skillbanning process by considering the custom metric called ETI (Extraterrestrial Index) which will measure the average performance of a given players in a tournament. Then we convert the ETI from each tournaments into one tournament. Then we select the candidates using rule-based criterias, after that, we use these candidates list to perform the outlier detection by comparing the "density" which is the performance and how outstanding the performance of the players compared to others.

## Algorithm for Detecting Outliers outside Tournaments

We have the ability to detect the outliers from the major free-for-all and rank-restricted tournaments. The next step is to expand our algorithm to the more general scenario, which is the ranked and loved section. The ranked and loved section is the place where the scores are publicly shown and there are high-level tournament and physical maps participated in the section. The score calculation algorithm might not be the same as the tournaments but to simplify, we will assume they are the same.

This model will address the case of players who doesn't have any history in major tournaments. Of course that in order to obtain a rank of 4 digits, a player needs to pursue the performance points in order to gain ranks, which can generate some insightful historical data to investigate and process in our model.

In this study, we used the data of players with global rank 1-10000, including scores and player statistics and recalculate into the approximate 4k rank. Which the calculation procedure can be found in [osu!wiki](https://osu.ppy.sh/wiki/en/Performance_points). Then we will use this information in our procedure. We selected the specific beatmaps to perform the outlier detection procedure (note: the selected beatmaps in this study are omitted). Then perform the calculation

### Candidates Selection

Because of the large amount of data points and it is difficult to determine just the outliers among all ten thousand players, we decide to select the candidates to perform the outlier detection by putting all the players into the classification model which will classify between 4 digits and higher than 4 digits. Which has the parameters of ETI from the ETI model. However the missing data will be validated by the algorithm of **K-Nearest Neighbors**. Next, we will classify the players using **Logistic Regression** and gather the players who are 4 digits, but classified as higher than 4 digits according to the model. The gathered players are considered as the candidates for the skillbans process which will be elaborated next.

### Outlier Detection using One-Class Support Vector Machine

### Support Vector Machine

**Support Vector Machine** is an algorithm for Machine Learning which is used for mainly in classification tasks. The main idea is to draw the region that will distinguish between two classes. In this study, we decide to use the linear plane

$$
f(\textbf{x}) = w^T\textbf{x} + b
$$

The decision function works like Logistic Regression. It depends on the sign of the decision function. But the fitting method works different than the Logistic Regression. Its procedure relies on using the knowledge of **geometry** and **similarity** among the data points in one class meanwhile the Logistic Regression relies on the Likelihood of **Binomial Distribution**.

Disclaimer that the author (IndexError) still has no prior knowledge about this and how to interpret and derive them, so this is a brief idea on how the SVM works.

### One-Class SVM

**One-Class SVM** is an unsupervised category of Support Vector Machines to determine whether a data point is an outlier by assigning all data into one class and find the decision function to distinguish between the normal data point and the outlier. In this study, the function to determine the outlier is the linear plane. As we have classified the candidates for the skillbans, it is reasonable set the algorithm to detect half of the data points to be the outliers and the rest as the normal data points. The region that has the higher score will be considered as outliers. Or in mathematical words, $f(\textbf{x}) > 0$. This will determine us the outliers for the candidates we got from ranked and loved mapsets.

## Model Performance

After the model is constructed, the next thing is to test the Model with user input in samples to determine whether the model will yield the low false predictions rate. Namely model should at least agree (partially) with the user inputs.

This testing method is similar to the meme *apes together strong*, namely both the user and model are apes and they are working together to make a precise definition of where should a player be considered as an outliers. Users know which player should be skillbanned according to performance but the Model will provide the precise calculation with more evidence to support the decision of the users.

### User Input

In the testing procedure, we let the volunteers determine the samples from the ranked section and tournaments history whether they are the outliers subjected on some score history, namely the historical scores with the highest standardized value compared to other players. The model output will be hidden from the testers in order to avoid the confirmation bias. Testers will be provided three following options: **True** (skillbanned), **False** (not skillbanned) and **Unsure** (not enough evidence). Then we obtain the results from the tester to calculate the **Confusion Matrix** compared to the model.

### Confusion Matrix

**Confusion Matrix** is the matrix which is used to find the agreements between the labelled data by testers and the predicted data by the model. The row **i** and column **j** of the matrix indicates the frequency that how frequent that the model predicts that the data is in the class **j** if the label indicates that it is in the class **i**.

In this case, the first row, first column of the matrix will indicate how frequent the model predicts the skillbans correctly (True Positive), and the second column will indicate how frequent the model predicts that the player should not be skillbanned given the actual label is skillbanned (False Negative). In the second row, first column will indicate how frequent the model predicts that a player should be skillbanned given the actual label is not skillbanned (False Positive) and the second column will indicate how frequent both the model and the actual label is False (not skillbanned) (True Negative). The table below is the brief diagram of the confusion matrix in skillbans classification (and binary classification in general)

|                | True (Predicted) | False (Predicted) |
|:--------------:|:----------------:|:-----------------:|
|  True (Actual) |   True Positive  |   False Negative  |
| False (Actual) |  False Positive  |   True Negative   |

Our approach in usage of confusion matrix is discarding the "Unsure" label from the actual label and consider only sample where the testers are labelled as True or False as the actual label. Then compute the confusion matrix, after that what we would obtain is the accuracy of the model relative to the actual label.

### Accuracy Metric

From the Confusion Matrix, we can also define the following metric(s) to determine how accurate the model is:

- **Precision**: Determine how much relatively the positive predictions that are agreeing with user input. Can be found by dividing True Positives by total number of positive model predictions

$$
\text{Precision} = \frac{TP}{TP + FP}
$$

- **Recall**: Determine how much relatively the model captures all of the positive list from the user input. Can be found by dividing True Positives by total number of positive user predictions

$$
\text{Recall} = \frac{TP}{TP + FN}
$$

From two metrics above, we can define another metric called **F1-Score**, which is

$$
\text{F1} = \frac{2 \cdot \text{Precision} \cdot \text{Recall}}{\text{Precision} + \text{Recall}}
$$

**F1-Score** will determine how much the model agrees with the user predictions, this will be really useful for the imbalanced classification or outlier detection task like this one specifically.

## Related Works

- Clustering using sum-of-norms regularization: With application to particle filter output computation - [link](https://ieeexplore.ieee.org/document/5967659)
- sklearn.neighbors.LocalOutlierFactor - [link](https://scikit-learn.org/stable/modules/generated/sklearn.neighbors.LocalOutlierFactor.html)
- sklearn.linear_model.LogisticRegression - [link](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LogisticRegression.html)
- sklearn.svm.OneClassSVM - [link](https://scikit-learn.org/stable/modules/generated/sklearn.svm.OneClassSVM.html)
- The Elements of Statistical Learning - [link](https://hastie.su.domains/ElemStatLearn/)
