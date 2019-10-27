# Social Network Analysis

## Representation of network

### **Adjacency Matrix**  

In the matrix, if there is weight, fill in the weight; if not, boolean.The matrix is symmetric if the network has no direction and all connected \(\*\).   
Drawback: waste of space, not an efficient format if large size. Use $$O(V^{2})$$.   
Solution: Sparse matrix format, or the next option: adjacency list.  
Good news: checking takes only O\(1\) \(\*hash table?\)

### Adjacency List 

eg.1 with weight, but no direction, no order  
eg.2 without weight, with direction, no order  
O\(V+E\) memory, harder \(compared to 👆\) to check whether 2 nodes are connected; quick retrieval of a node's neighbors. 

### Graph **Laplacian \(L\)**



### Nodes' connectedness vs graphs' connectedness

Nodes: path between nodes

Graph: path between every pair of nodes. 



giant component 

cliques: all connected 

6; AEGFD is not bc dg not connected. 

paths

shortest path

diameter 

hubs and bridges 

clusters/communities 

subnetworks

egocentric network: 

degree 1: d, d's direct friend, and edges between them 

degree 1.5: plus the connections between friends. and it's also ok to exclude d in it, bc it's obvious by definition. 

node degree: in or out, number of edges on a node. 

degree distribution: statistical summary of the degrees in the network; absolute frequency \(histogram\)

density: edges/total\_possible\_edges; percentage of the edges in the graph. total\_possible\_edges = N\*\(N-1\)/2, if it's non-directed. 

clustering coefficient \(transitivity\): 

* Local transitivity: density of 1.5 degree, with ego excluded. eg 6 total, itself excluded, N=5. meaning: the possibility that ego's friends are connected. 
* Global transitivity: calculate the local transitivity for every node, and then take average. 



Probabilistic Counting Algorithms for Data Base 

&lt;&gt;

Pain Point: Find roughly how many unique nodes there are. 

Idea:   
First, 1/2 of integers are even \(divisible for 2\). 

1/4 of the integers are divisible for 4, 

...

Second, Replicate the idea, translate the number to binary. The 2s, 4s, and 8s will be 10, 100, 1000.

Now the problem becomes a 'finding the max', get the number of zeros in the representation, then we can get the approximation with O\(n\).



&lt;Private traits and attributes are predictable from digital records of human behavior&gt;

Pain point: what do 'likes' on facebook reveal? 

Idea: Convert the 'like matrix' to 'component matrix' through SVD, then run logistic regression. 



&lt;Predicting Individual Behavior with Social Networks&gt;

Pain Point: Can marketers predict individual behavior with social networks? Is it worth it? 

Idea: Pilot study, sort the probability in a descending order. 



{% embed url="https://www.sci.unich.it/~francesc/teaching/network/" %}



