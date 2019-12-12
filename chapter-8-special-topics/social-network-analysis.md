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

degree 1: d, d's direct friend, and edges between them 

degree 1.5: plus the connections between friends. and it's also ok to exclude d in it, bc it's obvious by definition. 

node degree: in or out, number of edges on a node. 

degree distribution: statistical summary of the degrees in the network; absolute frequency \(histogram\)

density: edges/total\_possible\_edges; percentage of the edges in the graph. total\_possible\_edges = N\*\(N-1\)/2, if it's non-directed. 

clustering coefficient \(transitivity\): 

* Local transitivity: density of 1.5 degree, with ego excluded. eg 6 total, itself excluded, N=5. meaning: the possibility that ego's friends are connected. 
* Global transitivity: calculate the local transitivity for every node, and then take average. 



Probabilistic Counting Algorithms for Data Base 

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

## New approaches

### Node Embedding 

#### Method 1: Graph Laplacian 

L=A-D \(adjacency-degree\)

Pros: predict friendship \(link connection\)  
Cons: sparse matrix size   
  
potential way: laplacian embedding, select the top k eignen vectors from the laplacian matrix. However, still need to store the original matrix in memory, still costly.

#### Method 2: Analogy to text classify 

Create a document-term-matrix\(DTM\), potential problem is \(1\) there are way more columns than the entries. \(2\) because words are related, we cannot assume an IID of the columns   
  
A potential solution is to map them to topics, and instead of finding the frequency of word, we find the percentage of each topic from each doc. 

#### Method 3: Word Embedding 

\(1\) way: Use a taxonomy like WordNet\(that tells the synonym of word\) that has hypernyms relationship  
\(2\) way: use domain knowledge to create synonym set  
\(3\) a vector form, turn each word into a vector, and look at its neighbors: the meaning of words can be known through the company it keeps. Summarize the cooccurrence relationship, use a matrix here, and check the cosine similarities.   
problem is the cooccurrence matrix size will increase, we can surely run a SVD or PCA on that matrix and reduce the dimensionality.    
Once with a low-dimensional vector, can run similarity test.   
  
another way is to do a gradient descent, 'how likely that this word will cooccur with some other words'. input: word, output: neighboring words, multi-class classification, and the number of class is the number of vocabulary   


In a network setting, the idea is similar. The neighboring nodes of a neighbor is very similar to the neighboring word. Use word2vec on graph. input: node in network, output: network

Treat the node sequence as sentences. biased second order random walk, 'random walk with memory'.The nodes that are most similar to the original point will have more weights in a random walk.  

Node to work paper: BFS: more incliend to explore the local neighborhood; DFS: more inclined to explore the neighbors 

return parameter p: the likelihood to return where i was  
in=class parameter q:



Step 1: Generate random walks   
Step 2: Treat nodes as words, sequences as sentences, use word2vec to reduce dimensions   
Step 3: Use node vectors for other tasks \(eg. prediction or link prediction\) 

