---
description: Entitled 'The Winning Recipes to an Oscar Award'
---

# Draft for Social Network Project Report

## Executive Summary 

In this study, with IMDB API and the two datasets 'Best Picture Nominations' as well as the 'Best Actor Nominations generated' with it, I explored several social network metrics within both Bi-partite \(actor-director\) and Mono-partite\(director-director, director-actor, company-company\) network. These include: clustering coefficients and centrality measurements, Gini Index within each network, as well as the shortest path for a node to reach Kevin Bacon \(also known as Bacon Number\).  

All of the analysis fall within the categories of: \(1\) Discuss whether there is winning recipes for the nominees to eventually receiving an Oscar Award. \(2\) Seek for the best centrality measurement\(s\) for a more precise winner-prediction. \(3\) Explore the social network metrics of a network that is selected from the heavy tail of a power-law distribution, or in other words, the centralities of the nodes that are selected based on a conditional probability P\(winner\|nominated\).\(4\) Analyze 'stardom' effect. 

## Introduction

The ‘cumulative advantage’ model has many names: preferential attachment, Matthew effect, Pareto principle, or ‘lock-in’ effect. Studies on cumulative advantage are generally focused on two topics: either from statisticians and physicists who are more concerned about the outcomes of a set of objects, namely how the small differences are magnified in the long run; or from the economists who focus on the outcomes of the specific objects, in other word, how unpredictable the outcome is.

Successful cultural products—such as hit songs, best-selling books, and blockbuster movies—also show a ‘winner take all’ market, where superstars earn orders of magnitude more successful than the average. Related statistics include: only about 8 percent of all movies made gross more than $40 million at the domestic box office, and most of the movies do not make a profit at all. Similarly, 90 percent of musicians lose money for their parent companies [\(Vogel 2004\)](https://paperpile.com/c/aNMfIL/3eJr).

Therefore, it is hard to make predictions on factors in the film industry, and those researchers who have tried \([De Vany](https://paperpile.com/c/aNMfIL/sQoR) 1999\) admitted that the revenues from movie box office follows a Lévy distribution. Forecasts of expected revenues are meaningless because the possibilities do not converge on a mean; they diverge over the entire outcome space with an infinite variance. 

However, a Risk and Survival Analysis model can be applied for such study: instead of predicting the infinite variance, a survivor analysis considers the probabilities that are attached to certain outcomes in the upper tail. For instance, if one wants to predict whether a film will earn money, they can study the conditional probability that a movie will continue to earn more, given that it already has earned a certain amount \(box office revenue of fifty million or more\).

Following the same idea, in our case, it would be to analyze the distribution of centralities under the condition that the network is already considered as elite-network, where all the nominees are superstars already. In this project, I took the actors, directors, IMDB vote, Oscar Nomination results, collaborating countries and producing companies from a list of nominees in this decade.

The reason for choosing this specific time range, instead of using a complete database from 1964, is that each award nominates five individuals only. Even if I include the coworkers that the nominees were working with in those films, the size of such network cannot bear much of noise. Introducing a longer time range will in general add more noises because some practitioners who were nominated that long ago might not be involved in this business right now. If the analysis is used to predict whether the nominee would return with an award, a network of more recent time range can serve a better purpose. 

To validate that a conditional probability P\(winner\|nominated\) is more predictable than P\(winner\), the two graphs below are the IMDB score and mean budget, averaged for each year. From these two factors, we can observe that this subgroup of nominated films do not follow a Lévy-distribution anymore. It looks promising that we might get good prediction on the result when including network-related statistics \(centrality measurements, clustering coefficients, etc.\) 

> 2 graphs from slide \#5

The proposed steps to carry out this project are described as follows:

* Crawl information of Actor/Actress/Director/IMDB scores, etc. using IMDB API
* Construct collaborating companies network
* Construct two separate Actor/Actress-director collaboration networks \(one for Best Actor/Actress/Supporting Actor/Supporting Actress Network and the other one for Best Picture Network\) 
* Use Gephi and Palladio for visualization and exploratory purpose
* Calculate the network-related statistics within different networks

## Data Preparation

### Construct Full Datasets

The Oscar website [www.oscar.org](www.oscar.org) does not provide a downloadable form of list, and they only support search & query on their own webpage. Thanks to a GitHub repository contributor, who has crawled the name of nominees as well as award title for each year since 1964 from Wikipedia, the first step for this project is simplified to: construct a full database based on this provided name list, and the database should include information of nodes and edges within the Oscar network. 

In the analysis for Best Picture, it is intuitive to make a director-actor network where each clique is of size 5 or 6 and attribute the award to this whole team, given 1 or 2 of them are the director\(s\), and the rest of four nodes are the four leading actors and actresses. This is because Best Picture Nominations are partly directors' credits, and the cast also share the same honor. 

There are a few options to proceed in order to generate the database for use:

* Use MongoDB and construct a database myself
* Download the complete dataset of all films from https://datasets.imdbws.com
* Use IMDB API
* Use Third-Party API \(eg. OMDB\)

A third-party API has limits on the free accounts and only allows unpaid users to call its API 500 times so the last option comes as the first to pass. Because there are only 130 films which are nominated as 'Best Picture', and 374 persons who are nominated as 'Best Actors' all together since 2000, either downloading the whole database from IMDB \(which has 5 separate files that needed to conduct 'left join' with, and the total data size is over 2GB\) or writing lengthy programs for data retrieval would not worth the effort. 

The IMDB API was used here to gather all the data. The API itself has two main methods: search and get. As their literal meanings, 'search' allows users to give names that are close enough to the actual keys in their database, and return the ID as well as exact key values. For instance, a vague call of ia.search\_movie\('matrix'\), it would return the IMDB MovieID and the actual name of this movie that is stored in their database, 'The Matrix'. This process is relatively fast. Similarly, ia.search\_people\('Julia'\) will return 'Julia Roberts'. Each search query takes about 0.3 second. 

The method 'get', on the other hand, takes longer time, but it also can give back more values. If call a 'get\_movie', one can further specify information including 'plot','quotes','reviews', 'cast', 'directors', 'rating', as well as 'budget'. Although there supposed to be a return value of 'awards', listing all the awards that a movie \(or a person\) receives, as I tried this function, it was already deprecated and returned None to all the function calls. 

Two complete datasets were then generated with IMDB API with the following headings:

> Graphs for the two headings

### Pre-Processing 

The constructed datasets needs to be cleaned as well because when parsing directors/cast/companies attributes, the function calls return a list within a Pandas DataFrame cell. Also, there are characters \($ signs, etc.\) in the budgets column. 

After proper data cleaning, the datasets now have the headings described in xx and xx.

> Graph for the two headings

### Create EdgeList and Node Attributes

Although the dataset contains many information, the networks I constructed in this project only needs edge-list and node attributes. To be more specific, node attributes are: Name, Role \(Actor/Director/DualRole\), Result\(Y/N\). Name is the name of actor or director, and role is the role of this person. Edge attributes cannot accept an actor who is a director in some other film, or that otherwise. Therefore, when this happens, the role is changed to DualRole. 

A network of companies that collaborated in the same film is constructed as a mono-partite graph \(company-company\). General movies have more than one actor or actress filming, so this network is cleaned up to create tri-partite nodes \(actor-movie-director\). A node/vertex is the fundamental unit of a graph and the edges are the lines connecting a set of nodes \(edges are also referred to as arcs when the edges are undirected\). The tripartite nodes are later converted to bi-partite nodes \(one of the three nodes is made as an edge\) and mono-partite nodes \(two of the tri-partite nodes are made as edges\). For illustration, a comparison is shown in graph \* below. 

> Graph describe tri-bi-mono- partite graphs

This is essentially done to determine a detailed level of insights between the nodes, while the nodes which gets converted to an edge contributes to the strength / influence between the remaining nodes. \(i.e.\) when one converts movies and directors into edges and retain actors, they can establish unique and clear insights between actors, with the influence of the associated movies & directors as edge weights. 

As for the network for Best Actor/Actress/Supporting Actor/Supporting Actress, the node attributes are still comprised of Name, Role \(Actor/Director/DualRole\), Result\(Y/N\). The difference is, this time, instead of taking four members from cast, three main actor/actress were taken. Also, the boolean column 'Result' was assigned to positives only to the actors who indeed won the award, because unlike Best Picture Nomination, these awards are not rewarded to the whole cast, but to some specific person in the team. 

For a better visualization and analysis, it is also important to assign to each node a color and size. This will be further discussed in the following sessions. 

### Exploratory Data Analysis

[Gephi](http://gephi.org/), a popular open source network visualization tool is used to apply the various graph algorithms and generate insights at this stage, while further analysis are conducted with R and python. This is because Gephi has a more interactive UI in displaying network graphs and results of the graph algorithms. The downside of the Gephi tool is its inability to handle large datasets. However, with the current datasets, this shouldn't cause a problem.



## Conclusion

 strategic nodes in the networks of scientific communication that provide ready access to information at the frontiers of research. 

As William Goldman, a well-respected screenwriter puts it, the recipe in the entire movie industry is ‘nobody knows anything’.

## Reference

https://imdbpy.readthedocs.io/en/latest/

  


###   

