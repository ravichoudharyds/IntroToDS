Warning: This is based on my understanding of Causal Inference methods based on a project I did recently. Please do correct me if I am wrong somewhere. 

Usually Causality is confused with correlation. People assume that if 2 variables are very highly correlated, then one is the cause and the other is the effect. But this is a mistaken relationship since causality is an entirely different concept. Correlation can exist among two things which have no cause and effect relationship. For example there is a positive correlation between the average age and average wealth of people, but age is not a cause of person's wealth, it's rather the experience and the inflation of the economy.   

Causal Inference is an important topic in the field of medicine. In the field of medicine, it is usually done through randomized experiment i.e., patients are randomly assigned to one of the 2 groups 
1. group getting the actual medicine
2. group getting a placebo

<p align="center"><img alt="Placebo and Real Medicine" src="medicine.jpg"></p>


The random assignment helps in eliminating any form of bias that the people conducting the experiments or the patients themselves might introduce. In order to properly study the effect of medicine a double blind study is done. The double blind study is a study in which neither the patients and nor the doctors know about the group that the patient is assigned to. This way the effect of the medicine can be studied in comparison to the placebo effect. Based on the size of the group receiving the medicine, the study can also help confirm if the medicine is the sole reason for the change in patient's health and that change is statistically significant. 

But in most fields, there is a limitation on conducting experiments due to the expenses or non-feasibility of the experiment. As such a lot of research is going on in the development of causal inference on observational data. Observational data is data where no random assignment was followed. In observational data we see the data of patients with the group they were assigned to, and the result of the assignment to that group but we have no data on the process of assignment to groups.

The problem with observational data is that because of the bias introduced during treatment assignment, comparison of the results of 2 groups can yield wrong results. 

Since the study is not randomized, observational study requires further investigation into the different possible reasons for the assignemnt to a particular group to eliminate the bias. These reasons are called confounders. And causal inference on observational data assumes that conditioning on the confounders, the treatment was randomly assigned. In other words, once we control for all the possible reasons that might have led to a patient recieiving or not receiving the medicine, the assignment will be random. 

[Prof. Andrew Gelman's book](http://www.stat.columbia.edu/~gelman/arm/chap9.pdf) gives a better understanding of causal inference and its difficulties. 

To get my hands dirty and gain some practical experience, I worked on a project to study if there is a cause and effect relationship between movies reviews and the revenue of the movie. 

<p align="center"><img alt="Pixar Movies Revenue Vs Ratings" src="pixar.png"></p>

For the project, I divided the data into 3 groups and separately assumed a treatment and control in each. The groups are mentioned below 
1. Treatment: Movies with positive reviews, Control: Movies with negative reviews.
2. Treatment: Movies with positive reviews, Control: Movies with neutral reviews.
3. Treatment: Movies with neutral reviews, Control: Movies with negative reviews.

As I mentioned earlier, in order to do causal inference on observational data, we need to account for all the confounders. The confounders I considered for the project were 4 lead actors/actresses, writers, directors, budget, production house and genre.

There are several methods that rely on the use of supervised learning in causal inference study on observational data, some of them are described in this [tutorial](https://cs.nyu.edu/~shalit/slides.pdf) by Prof. Uri Shalit and Prof. David Sontag. All of these methods try to eliminate the treatment assignment bias.

Among the several methods described in the tutorial, I chose to start with Propensity Score Matching as it is one of the simplest and easy to understand methods. In this method, a propensity score model with confounders as predictors is developed to predict the probability of treatment assignment. Any classification model can be used for this purpose. I chose to go with Logistic Regression since it has been the go to method for the propensity score modeling for many reasearchers. Once we have the propensity score model, we can use matching techniques like kNN to match the observations in treatment group to the closest observations in control group based on the propensity score. The results of the closest match are considered as a "what if" scenario. For example in my project where treatment group was movies with positive reviews, and control group was movies with negative reviews, propensity score represented the probability that a movie will get positive reviews. And  matching helped me get the expected revenue of a movie with positive reviews for the case that it received negative reviews. 

You can read up more about Propensity Score Matching on [Wikipedia](https://en.wikipedia.org/wiki/Propensity_score_matching) and [this paper](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3144483/) by Prof. Peter Austin.

[Paired T-test](http://www.statstutor.ac.uk/resources/uploaded/paired-t-test.pdf) was used to get statistical significance of the difference in mean revenue between the treatment and control groups. Other tests like Wilcoxon Signed Rank Test can be used for the same. While Propensity Score matching is simple and easy to implement, it suffers from a weakness. The weakness is that the technique relies the propensity score to be aligned correctly to the actual probability of treatment assignment. Since the actual probability distribution is unknow, this alignment is difficult to assess.

To take care of this I chose to use a [Doubly Robust Estimator](https://www4.stat.ncsu.edu/~davidian/double.pdf) to get an estimate of the average treatment effect i.e., difference in revenue of a movie had it received negative reviews instead of positive reviews. The doubly robust estimator relies on the propensity score models discussed earlier and another model which estimates the results conditioned on the treatment group. For my project this meant, a classification model for the propensity score and 3 regression models given below. 
1. To predict the revenue of movies with positive reviews
2. To predict the revenue of movies with negative reviews
3. To predict the revenue of movies with mixed reviews

Both of these models are then used to generate an estimator. This is called doubly robust because it uses 2 estimators and even if we can get only one estimator correct, we will get correct results.

This was a summary of methods I had used for Average Treatment Effect. You can read more about it in the [Project Report](Project%20Report.pdf).

There are much more advanced methods out there. And some of them can be used for estimating Individual Treatment Effect i.e., the effect of treatment on a particular individual. Some of these advanced methods are mentioned here - BART (Bayesian Additive Regression Trees), [Causal Forest/ Generalized Random Forest](https://github.com/grf-labs/grf) based on the [paper](https://arxiv.org/pdf/1610.01271.pdf) by,Susan Athey Julie Tibshirani and Stefan Wager, [Deconfounder](https://github.com/blei-lab/deconfounder_tutorial) by Yixin Wang based on her [paper](https://arxiv.org/pdf/1805.06826.pdf).  There are some other techniques based on a competition held on Causal inference discussed in this [paper](https://arxiv.org/pdf/1707.02641.pdf).

PS: I don't hold any copyrights on the images used.
