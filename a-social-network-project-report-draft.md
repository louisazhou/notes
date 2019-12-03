---
description: Entitled 'The Winning Recipes to an Oscar Award'
---

# Draft for Social Network Project Report

## Executive Summary 

In this study, with IMDB API and the two datasets 'Best Picture Nominations' as well as the 'Best Actor Nominations' generated with it, I explored several social network metrics within both Bi-partite \(actor-director\) and Mono-partite\(director-director, director-actor, company-company\) network. These include: clustering coefficients and centrality measurements, Gini Index within each network, as well as the shortest path for a node to reach Kevin Bacon \(also known as Bacon Number\).  

All of the analysis fall within the categories of: \(1\) Discuss whether there is winning recipes for the nominees to eventually receiving an Oscar Award. \(2\) Seek for the best centrality measurement\(s\) for a more precise winner-prediction. \(3\) Explore the social network metrics of a network that is selected from the heavy tail of a power-law distribution, or in other words, the centralities of the nodes that are selected based on a conditional probability P\(winner\|nominated\).\(4\) Analyze 'stardom' effect. 

## Introduction

The ‘cumulative advantage’ model has many names: preferential attachment, Matthew effect, Pareto principle, or ‘lock-in’ effect. Studies on cumulative advantage are generally focused on two topics: either from statisticians and physicists who are more concerned about the outcomes of a set of objects, namely how the small differences are magnified in the long run; or from the economists who focus on the outcomes of the specific objects, in other words, how unpredictable the outcome is.

Successful cultural products—such as hit songs, best-selling books, and blockbuster movies—also show a ‘winner take all’ market, where superstars earn orders of magnitude more success than the average. Related statistics include: only about 8 percent of all movies made gross more than $40 million at the domestic box office, and most of the movies do not make a profit at all. Similarly, 90 percent of musicians lose money for their parent companies [\(Vogel 2004\)](https://paperpile.com/c/aNMfIL/3eJr).

Therefore, it is hard to make predictions on factors in the film industry, and those researchers who have tried \([De Vany](https://paperpile.com/c/aNMfIL/sQoR) 1999\) admitted that the revenues from movie box office follows a Lévy distribution. Forecasts of expected revenues are meaningless because the possibilities do not converge on a mean; they diverge over the entire outcome space with an infinite variance. 

However, a Risk and Survival Analysis model can be applied for such study: instead of predicting the infinite variance, a survivor analysis considers the probabilities that are attached to certain outcomes in the upper tail. For instance, if one wants to predict whether a film will earn money, they can study the conditional probability that a movie will continue to earn more, given that it already has earned a certain amount \(box office revenue of fifty million or more\).

Following the same idea, in our case, it would be to analyze the distribution of centralities under the condition that the network is already considered as elite-network, where all the nominees are superstars already. In this project, I took the actors, directors, IMDB vote, Oscar Nomination results, collaborating countries and producing companies from a list of nominees since 2000.

The reason for choosing this specific time range, instead of using a complete database from 1964, is that each award nominates five individuals only. Even if I include the coworkers that the nominees were working with in those films, the size of such network cannot bear much of noise. Introducing a longer time range will in general add more noises because some practitioners who were nominated that long ago might not be involved in this business right now. If the analysis is used to predict whether the nominee would return with an award, a network of more recent time range can serve a better purpose. 

To validate that a conditional probability P\(winner\|nominated\) is more predictable than P\(winner\), the two graphs below are the IMDB score and mean budget, averaged for each year. From these two factors, we can observe that this subgroup of nominated films do not follow a Lévy-distribution anymore. It looks promising that we might get good predictions on the winners when including network-related statistics \(centrality measurements, clustering coefficients, etc.\) 

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

Although the dataset contains many information, the networks I constructed in this project only needs edge-list and node attributes. To be more specific, node attributes are: Name, Role \(Actor/Director/DualRole\), Result\(Y/N\). Name is the name of the actor or director, and role is the role of this person. Edge attributes cannot accept an actor who is a director in some other film, or that otherwise. Therefore, when this happens, the role is changed to DualRole. 

A network of companies that collaborated in the same film is constructed as a mono-partite graph \(company-company\). General movies have more than one actor or actress filming, so this network is cleaned up to create tri-partite nodes \(actor-movie-director\). A node/vertex is the fundamental unit of a graph and the edges are the lines connecting a set of nodes \(edges are also referred to as arcs when the edges are undirected\). The tripartite nodes are later converted to bi-partite nodes \(one of the three nodes is made as an edge\) and mono-partite nodes \(two of the tri-partite nodes are made as edges\). For illustration, a comparison is shown in graph \* below. 

> Graph describe tri-bi-mono- partite graphs

This is essentially done to determine a detailed level of insights between the nodes, while the nodes which gets converted to an edge contributes to the strength / influence between the remaining nodes. \(i.e.\) when one converts movies and directors into edges and retain actors, they can establish unique and clear insights between actors, with the influence of the associated movies & directors as edge weights. 

As for the network for Best Actor/Actress/Supporting Actor/Supporting Actress, the node attributes are still comprised of Name, Role \(Actor/Director/DualRole\), Result\(Y/N\). The difference is, this time, instead of taking four members from cast, three main actor/actress were taken. Also, the boolean column 'Result' was assigned to positives only to the actors who indeed won the award, because unlike Best Picture Nomination, these awards are not rewarded to the whole cast, but to some specific person in the team. 

For a better visualization and analysis, it is also important to assign to each node a color and size. This will be further discussed in the following sessions. 

## Data Analysis

[Gephi](http://gephi.org/), a popular open source network visualization tool is used to apply the various graph algorithms and generate insights at this stage, while further analysis are conducted with R and python. This is because Gephi has a more interactive UI in displaying network graphs and results of the graph algorithms. The downside of the Gephi tool is its inability to handle large datasets. However, with the current datasets, this shouldn't cause a problem.

Another visualization tool is [Palladio](http://hdlab.stanford.edu/palladio/), developed by Humanities+Design Lab of Stanford. Palladio is easy to use and do not need any installation as it is an online visualization tool. The reason for choosing two visualization tools is that it is harder to show all the labels \(names of the nominees\) with Gephi's layouts, whereas Palladio can generate a graph with labels in just one click, without filtering or adjustments. It also provides simple panels and this is suffice for initial visualizations. 

Lastly, both Networkx with Python and iGraph with R were used to calculate the exact statistics and for more targeted visualizations.

### Bacon Number in the winner network

The Bacon number of an actor or actress is the number of degrees of separation they have from actor Kevin Bacon, as defined by the game known as Six Degrees of Kevin Bacon. It applies the Erdős number concept to the movie industry. Simply put, a Bacon Number is how further away a person is from Kevin Bacon in a network. 

For example, Kevin Bacon's Bacon number is 0. If an actor works in a [movie](https://simple.wikipedia.org/wiki/Movie) with [Kevin Bacon](https://simple.wikipedia.org/wiki/Kevin_Bacon), the actor's Bacon number is 1. If an actor works with an actor who worked with Kevin Bacon in a movie, the first actor's Bacon number is 2, and so forth.

It is a game of finding the shortest path to a person and it is said that the whole Hollywood cast are all only steps away from Bacon. Each year, some movie sites would summarize the relation of the winners from that year with Bacon and draw a shortest-path graph. An example of such is given in xx. However, up till now, I do not see anyone tried to calculate the Bacon Numbers amongst the Oscar Awards nominees’ network.

> slide \#4

Using Palladio and selecting the winners from this decade, it is clear that Kevin Bacon is a bridge \(has a high betweenness centrality\) among all the Oscar winners. There are only four clusters floating around at the edge, but the winners in general are well-connected with Bacon. 

The node that is furthest away from him has a Bacon Number of 15. 

### Other Bridges like Kevin Bacon. 

Kevin Bacon is just one of the brokers in an Oscar Best Picture network. If take all the nominees, regardless of the results, then two other bridges, Ridley Scott, and Julianne Moore are noticed among the others.

> Slide \#15, exclude the three stickers

Ridley Scott is a 

> copy & paste something from wikipedia

While it is easier for a director/producer to be at relatively more strategic nodes in the networks, it is generally harder for actors or actresses to bridge the two communities. Julianne Moore, like Kevin Bacon, is one of such rare examples. Julianne is most known for her acting in _Still Alice_ and __this was also the film that won her an Academy Award. From the network graph, Julianne is connecting the community of younger, new stars, and those of the old ones. This shows that Juliane has gained much fame even before her nomination throughout her acting career. 

### Network of Collaborating Companies

The colors in Graph x are coded with respect to the clusters that a modularity algorithm assigns. For a company collaboration, it is easy to tell the four communities apart. This algorithm, when running on company network, has a result that looks similar to running a KNN. The size of each node represents the number of Oscar Awards that this company helps to win in the end. Larger companies with their reputation in the whole filming industry are the most distinguishable. Warner Bros., DreamWorks, and Plan B Entertainment make the largest nodes in the giant component subset network, taken from the whole network of collaborating companies. 

> slide \#5 \(I'll change this with a white background later after all the formatting. Just put the black here for place-holding\)

### Comparison of Best Picture Network with Best Actor Network

Before digging into the networks, it's worth a mention that the IMDB averaged scores of the nominated films from 2000 till now has the opposite trend. The years where Best Picture films averaged scores are low \(such as in 2005 and 2011\), Best Actor films are at relatively higher average scores.

> Slide \#16

The two graphs follow \(Picture x and y\) illustrates the actor-director network of nominees from Best Picture and from the Best Actor/tress/ Supporting Actor/Supporting Actress. Both networks are coded with the same rule: color is the weighted degree within the network, and larger size are winners. 

> The two node pics from slide \#12 and slide \#17, put side-by-side \(I'll change this with a white background later after all the formatting. Just put the black here for place-holding\)

The most noticeable difference is that of the number of nodes in the graph. As compared, since the right graph includes more nominees, there are more nodes included. Moreover, due to the node attribute 'result' of the two networks were originally labelled differently, a Best Picture network looks more clustered and dense. The graph density of Best Picture is 0.01, while that of Best Actor is 0.005. On a scale of 0 to 1, neither of them is a dense network, which comports with what is shown in the visualization. A density of 0 would mean that there are no connections at all, and a 1 would indicate that all possible edges are present \(a perfectly connected network\).

Modularity was used as a measure of relative density in the network: a community \(or a modularity class\) has high density relative to other nodes within its module but low density with those outside. Further, Fast Greedy Algorithm was used for community detection. The algorithm is a bottom up hierarchical decomposition process and it merges two current communities iteratively, with the goal to achieve the maximum modularity gain at local optimal.

This algorithm runs pretty fast, and can merge a sparse graph in linear time. However, if the network is large enough, small communities tend to be combined even if they are well-shaped. In our case for Best Picture Network, the community is easier to tell for such small network size. However, it did not work that will in the analysis for Best Actor Network. The two pictures below \(Picture x and y\) demonstrates this clearly.

> Slide \#14, two graphs

The most prominent communities that are highly likely to win an Oscar are also found amongst the communities mentioned above \(Picture x and y\). As mentioned, there are more winners in the Best Picture Network than in the Best Actor Network due to the nature of how the 'result' is labelled in the two network graphs. 

> Slide \#20, the two graphs

Further, the giant components that lie in the middle of the two graphs are put together for another comparison. In Picture x and y, the yellow nodes are actual winners of the corresponding awards, and node sizes represent the degree centralities of each node.

> The two pics from slide \#18

A coincidence here is both networks has a largest component of the same size, diameter of 11, which means there is a path length of 11 between the two farthest-apart nodes in the subset giant network. 

The giant yellow node of the left side is Leonardo Dicaprio, and that on the right side is Meryl Streep and Leonardo is the second largest node \(overlaid on top of the node of Meryl Streep\). This shows that Leonardo has the most connections within the whole Oscar Nomination network. 

It is worth mentioning that Leonardo also has high values in other centrality measurements. A table for the top five rankings is given below in Table \*\*. 

> make a table based on slide \#25 and \#26. The details of those numbers are:

```text
# Best Picture: 

Top 5 nodes by degree:
('Leonardo DiCaprio', 24)
('Tom Hanks', 20)
('Steven Spielberg', 20)
('Russell Crowe', 19)
('Brad Pitt', 19)

Top 5 nodes by betweenness centrality:
('Leonardo DiCaprio', 0.10317801164472765)
('Alejandro G. Iñárritu', 0.08164871216064475)
('Matt Damon', 0.06750369527954317)
('Emma Stone', 0.06442715700141442)
('Steven Spielberg', 0.06179198690382382)

# Best Actor/Actress:

Top 5 nodes by degree:
('Meryl Streep', 29)
('Leonardo DiCaprio', 24)
('Denzel Washington', 19)
('Cate Blanchett', 19)
('Julianne Moore', 19)

Top 5 nodes by betweenness centrality:
('Leonardo DiCaprio', 0.140951477535268)
('Matt Damon', 0.0869423493709777)
('Russell Crowe', 0.06402294657060532)
('Jennifer Connelly', 0.058856066842957744)
('Meryl Streep', 0.055020858669292634)
```

Eigenvector centrality cares if a node is a hub, but it also cares how many hubs this node is connected to. It’s calculated as a value from 0 to 1: the closer to one, the greater the centrality. In this regard, eigenvector centrality is useful for understanding which nodes can get information to other nodes quickly. If one knows a lot of well-connected people, they could spread a message very efficiently. For our case, Leo is again ranked on top, which indicates that he has all the necessary connections within this network. 

Betweenness centrality is a bit different from the other two measures in that it doesn’t care about the number of edges any one node or set of nodes has. Betweenness centrality looks at all the shortest paths that pass through a particular node. To do this, it must first calculate every possible shortest path in your network, so it took a little bit longer to calculate than other centrality measures.

If a node is the only thing connecting two clusters, every communication between those clusters has to pass through you. In contrast to a hub, this sort of node is often referred to as a broker. It not only gives information as to which nodes are important, but also gives the network connectivity and cohesion. Leonardo's betweenness centrality is even higher than all the film directors in this network, which is very impressive.

It also can be seen that some people, like Alejandro G. Iñárritu \(film director\), Matt Damon \(actor\) and Emma Stone \(Actress\), have high betweenness centrality but low degree. They probably are important brokers, connecting otherwise disparate parts of the graph, but since these nodes only show information and connections of Oscar Nomination movies, it doesn't necessarily mean that other nodes do not have other access to the people on the other side of the bridge. Still, a simple idea here is, knowing more people sometimes isn’t everything.

## Conclusion

Centrality measurements are good supplements when one needs to predict winners from the Oscar nominees. The nodes that bear higher centralities are regarded as more strategically-advantaged nodes in the networks of filming that provide ready access to information as well as access to the best filming opportunities. 

However, even with such statistics included, to predict winners accurately is still a challenge. As put by Leonardo Dipario during an interview after he failed to win a nominated Best Actor for 10 times, 'One thing I learned a long time ago is that you have absolutely no control of that.' Leonardo finally won his Gold Medal after trying for over 20 years from 1994 to 2016. Him failing each year became the longest internet meme. Given that the person with the most Oscar-related connections and resources came to such a conclusion, it is not hard to understand the famous quote from William Goldman, a well-respected screenwriter, that the recipe in the entire movie industry is ‘nobody knows anything’.

## Reference

https://imdbpy.readthedocs.io/en/latest/  
[Vogel, Harold L. 2004. Entertainment Industry Economics: A Guide for Financial Analysis. Cambridge University Press.](http://paperpile.com/b/aNMfIL/3eJr)  
[De Vany, Arthur. 1999. Uncertainty in the Movies: Does Star Power Reduce the Terror of the Box Office?](http://paperpile.com/b/aNMfIL/sQoR)  
  


  


###   

