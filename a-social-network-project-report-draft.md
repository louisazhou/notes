---
description: Entitled 'The Winning Recipes to an Oscar Award'
---

# A Social Network Project Report Draft

## Executive Summary 

In this study, with IMDB API and the two datasets 'Best Picture Nominations' as well as the 'Best Actor Nominations generated' with it, I explored several social network metrics within both Bi-partite \(actor-director\) and Mono-partite\(director-director, director-actor, company-company\) network. These include: clustering coefficients and centrality measurements, Gini Index within each network, as well as the shortest path for a node to reach Kevin Bacon \(also known as Bacon Number\).  

All of the analysis fall within the categories of: \(1\) Discuss whether there is winning recipes for the nominees to eventually receiving an Oscar Award. \(2\) Seek for the best centrality measurement\(s\) for a more precise winner-prediction. \(3\) Explore the social network metrics of a network that is selected from the heavy tail of a power-law distribution, or in other words, the centralities of the nodes that are selected based on a conditional probability P\(winner\|nominated\).

## Introduction

The ‘cumulative advantage’ model has many names: preferential attachment, Matthew effect, Pareto principle, or ‘lock-in’ effect. Studies on cumulative advantage are generally focused on two topics: either from statisticians and physicists who are more concerned about the outcomes of a set of objects, namely how the small differences are magnified in the long run; or from the economists who focus on the outcomes of the specific objects, in other word, how unpredictable the outcome is.

Successful cultural products—such as hit songs, best-selling books, and blockbuster movies—also show a ‘winner take all’ market, where superstars earn orders of magnitude more successful than the average. Related statistics include: only about 8 percent of all movies made gross more than $40 million at the domestic box office, and most of the movies do not make profit at all. Similarly, 90 percent of musicians lose money for their parent companies [\(Vogel 2004\)](https://paperpile.com/c/aNMfIL/3eJr).

Therefore, it is hard to make predictions on factors in the film industry. Many have tried \([De Vany](https://paperpile.com/c/aNMfIL/sQoR) 1999\)

  
  
   
  


  




 strategic nodes in the networks of scientific communication that provide ready access to information at the frontiers of research.   
  


### Proposed Steps

- Crawl information of Actor/Actress/Director/Film using IMDB API

- Construct from scratch an Actor/Actress - director collaboration network in the Oscar nominated films in this century

- Calculate the network-related metrics within the network

- Use Gephi for visualization  
  


### Data to Crawl 

Since I have the list of Oscar nominated movies and actors/actresses available from a GitHub repository contributor, the first step is to construct a full database, which includes information of nodes and edges within the Oscar network.   


The available list contains data following this schema:   
  


In the analysis for Best Picture, it is intuitive to make a director-actor network, where each clique is of size 5 or 6, given 1 or 2 of them are the director\(s\), and the rest of four nodes are the leading actors and actresses. For a better visualization and analysis, it is also important to assign to each node a color and size. Color can distinguish both the role each node \(being director or cast\), and the result of Oscar nomination \(winner/loser\) as well. The value of sizes can be assigned through network metrics including results from different centrality measurements.   


It would also be interesting to see if the rating of a film and its budget can help to predict whether a movie will be selected as the winner, or to analyze 'stardom' effect through plotting the distribution of ratings. Because, as discussed by many researchers in the literature, film industry follows a 'power law' distribution, where the standard deviation of centrality within the whole film industry is infinite and with no point of measuring. However, some researchers plotted the histogram of centralities of the nodes that are selected based on conditional probability. In our case, it would be to analyze the distribution of centralities under the condition that the network is already considered as elite-network, where all the nominees are superstars already'.  


Since none of the desired information listed above are present in the list available, I now have a few options to proceed:  


* Use MongoDB and construct a database myself
* Download the information from https://datasets.imdbws.com
* Use IMDB API
* Use Third-Party API 

A third-party API has limits on the free accounts and only allows unpaid users to call its API 500 times so I will pass the last option. Because there are only 130 films which are nominated as 'Best Picture' altogether since 2000, either downloading the whole database from IMDB \(which has 5 separate files that needed to conduct 'left join' with, and the total data size is over 2GB\) or writing lengthy programs for data retrieval would not be worth the effort.   


To spare more time for actual data-analysis and visualization, I adopted the third way, and used the IMDB API to gather all the data.  


The API itself has two main methods: search and get. As their literal meanings, 'search' allows users to give names that are close enough to the actual keys in their database, and return the ID as well as exact key values. For instance, a vague call of ia.search\_movie\('matrix'\), it would return the IMDB MovieID and the actual name of this movie that is stored in their database, 'The Matrix'. This process is relatively fast. Similarly, ia.search\_people\('Julia'\) will return 'Julia Roberts'. Each search query takes about 0.3 second.   


The method 'get', on the other hand, takes longer time, but it also can give back more values. If call a 'get\_movie', one can further specify information including 'plot','quotes','reviews', 'cast', 'directors', 'rating', as well as 'budget'. Although there supposed to be a return value of 'awards', listing all the awards that a movie \(or a person\) receives, as I tried this function, it was already deprecated and returned None to all the function calls.   


With IMDB API https://imdbpy.readthedocs.io/en/latest/, I created a complete dataset with the following headings:

## Research Goal

## Conclusion

As William Goldman, a well-respected screenwriter puts it, the recipe in the entire movie industry is ‘nobody knows anything’.

## Reference



Understand the Oscar Nomination network  


###   

