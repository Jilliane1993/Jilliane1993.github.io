---
layout: post
title: Predicting Horror Movie IMDB Scores for a Female Audience
---

In my Metis linear regression project I created models for predicting the IMDB score for horror movies with a female audience. As a life-long fan of horror films I knew I wanted horror to be the genre of focus for my predictive modeling, but it wasn't until I began looking closely at IMDB's demographic breakdown that I realized the majority of users voting identified as male. From the over 600 movies I scraped from IMDB, each with a minmum of 10,000 votes, only about 18% of the total votes were from users that identified as women. With this target demographic in mind, I focused on creating models for 3 potential use cases for the models; predicting the score of a film already released, a film that has not yet been released, and a film that is still in the pitching or pre-production stages. My final models used GridSearchCV with Ridge Regression, however I also used GridSearchCV with Lasso Regression to view my most important features for each model. A summary of my modeling results can be found [here](https://github.com/Jilliane1993/regression_final/blob/main/RegressionPresentationJNE.pdf).

## Tools Used
- BeautifulSoup
- Seaborn
- Pandas
- GridSearchCV
- Lasso Regression
- Ridge Regression
- Robust Scaler
- Standard Scaler

## Data Collection

My data was collected from scraping using requests python library with BeautifulSoup to parse the collected html. The inital scraping collected the general data from a list of horror films on IMDB with 10,000 or more votes, I chose to put a minimum vote limit on the films to avoid outliers such as films with artifically high scores due to lack of user input. From this inital scraping some of the data collected includes release year, runtime in minutes, budget, metascore, and subgenre. I also scraped for films that fit this search parameters but also contained a specific plot tag of interest, I chose cult films, female protagonist, monsters, and murder as plot tags of interest. These tags were used to create a dummified column of 0 not a plot tag for that film, or 1 a plot tag used for that film. Each movie on IMDB has a page that shows the demographic breakdown for its score, so I also scraped the tables from these pages to get the scores based off gender and the vote counts per gender. The initial, demographic information, and plot tags scrapping resulted in multiple pandas dataframes. I merged the dataframes together and then performed some exploratory data analysis, this consisted of correcting formatting issues from the data collection process, removing duplicates, dummifying categorical variables and converting data types.

## Modeling
I first created a model for predicting the score of a previously released film, this kind of predictor may be useful for deciding to re-release a film to theaters to decide if a film would be a good addition to a streaming lineup. This was my most predictive model with a R-squared value of 0.926 using Ridge Regression. This model included both the Metascore (an indicator of critic reception) and Male Score (an indicator of male audience reception). By far my most predictive feature was Male Score, indicating that while precise scoring may differ the overall general response to these films are similar among male and female audiences.  

However 'female audiences like what male audiences like' didn't feel like a satisfying answer to me, so I thought about taking existing audience scores out of the equation entirely. My second model keeps Metascore as a feature but does not include male score, in this scenario the film may have been seen by critics but has not yet been released to general audiences. This model could be useful for deciding theater screening or online streaming for new films or films from a forgein country not yet introduced to an American audience. This model had an R-squared score of 0.516, with Director James Wan as the most predictive feature (closely followed by Metascore). This model is where we start to see the impact a big-name director can have on a film's reception.

Here's where gender bias appears again, I realized that the majority of online film reviews were done by male critics. Keeping Metascore as a feature felt like a light version of keeping male audience scoring in, so my final version of this model removed both. This model looks at predicting the female audience scoring of a film that is still in the pre-production phase, it could be a useful tool in deciding if or how much to fund a film. It is also my least predctive model, with a R-squared value of 0.305. James Wan remains a key feature as well as runtime. 

## Key Takeaways and Future Work

When I started this projected I initially expected a greated importance of plot tags than I found, and less importance with directors. Stepping back and thinking about this it does make sense, when I think of iconic horror films I often first think of the director behind them and not that it was a monster movie. If I was to try to improve the models as is I'd add in the stars for the films, I underestimated the importance of a big-name director I shouldn't also forget about star-power. 

What I think might be more interesting though is to perform sentiment analysis and topic modeling on reviews for horror films based on these simplistic gender lines. What this analysis has shown me is that if a horror film is well-recieved by a male audience it most likely will also be similarly recieved by a female audience; but now I wonder if its for the same reasons? 

### My Repository
[Here's a link to my GitHub repository](https://github.com/Jilliane1993/regression_final) for this project for a closer look at the code used.
