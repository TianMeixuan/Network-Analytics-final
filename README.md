# File Specification
# Executive Summary
In order to build some insight about “ Why do users participate in certain threads ”, we tried to approach the question from two aspects - namely the number of shared connections that 2 users have and the communities as well as the attributes of the communities that they belong to. 
# Network Analytics Pipeline
## Scope of Data
SNAP dump of StackOverflow temporal network is used for the network analysis. There are only 3 columns in the SNAP data, namely:
SRC: id of the source node (a user)
TGT: id of the target node (a user)
UNIXTS: Unix timestamp (seconds since the epoch)

In order to conduct an analysis about the most fundamental elements of the network, we only considered answer to questions (A2Q) data as we believe A2Q successfully compressed a two-mode network between user and questions to an one-mode network. The A2Q data can also be used to extract community information, which is essential for us to conduct analyses about the research question. The comments to questions and comments to answers dataset track the activity within the interpersonal networks in StackOverflow. Thus, the two datasets are not included in this project. 

We assumed there is an information exchange tie between two users if a record could be found in the dataset - if A answered B’s question in time 0, regardless of who posted the question and who answered the question, we consider an undirected tie between A and B. The assumption is built upon that questions posted on StackOverflow are mostly legit questions and the activity of posting questions and the activity of answering questions form a valid information exchange tie. Unlike the email network, whereas the information exchange tie may be invalid due to spam email for example. We also assumed the network is not weighted as information exchange in a network is hard to measure. 

In terms of time frame, due to the large size of the network, we only considered first 4 weeks data since the first record. 

## Kossinets & Watts Model
We applied the Kossinets & Watts Model to the A2Q data to understand the probability for two users to build connections through the activity of answering questions based on the shared nodes they have. We studied the network over a four-weeks period to understand the differences among the probabilities of connecting in each week. Based on the fact that A answers B’s question evidenced that A’s interest in B’s question and the interest is not instant but lasting, we assume A and B remain connected up to week 4 since the time the first connect. To illustrate, if C and D are connected in week 2, we assume the tie between C and D remain in week 3 and week 4. 

## Community Detection and Analyses
We want to use the community detection to understand the community formation in the first 4 weeks and get an insight of how different users from different communities with different sizes and edge densities build connections in the information exchange network. In order to gain a big picture about the information detection community in the 4 week, we deem an undirected tie between the two nodes if one answered question posted by another. Again, as the information exchange network is hard to measure,  we assume that only one tie can emerge between two users no matter how many times they exchange information. To illustrate, if A answered B’s question in week 1 and B answered A’s question 3 times in week 3, we assume there is only one tie between A and B. Duplications are removed from the original dataset and only unique ties are considered for community detection and further analyses. We also used function(.to_undirected()) in Networkx to make sure the network is undirected before use the network for community detection and further analyses.

Louvain algorithm is chosen for detecting communities, which are non-overlapping. Louvain method is an algorithm of community detection to optimize modularity, which is a value that measures the density of edges inside communities to edges outside communities. Networks with higher modularity would have denser connections between the nodes within the community than that in different communities.
Reasons underlying the algorithm choice:
1. The Louvain method is a simple method for detecting communities and we can implement it easily in Networkx and CDLIB packages in python. For detecting the StackOverflow communities in the first 4 weeks, we use Louvain algorithm in CDLIB packages, and its code and call is available at Github (Rossetti, 2019).
2. It is suitable for large networks.  Many large networks were detected by this algorithm, such as Twitter social network (Josep, Vijay and Pablo, 2009), Linkedin social network (Jonathan and Igor, 2009) and mobile phone social network (Greene, Doyle and Cunningham, 2010).
3. Comparing to the other modularity optimization community detection algorithms, the Louvain algorithm is more efficient and has higher speed run codes for a typical network of 2 million nodes only takes 2 minutes on a standard PC. (Blondel et al., 2008)
 
Applying the Louvain algorithm in the StackOverflow dataset, it could be found that there are 11 communities in the first 4 weeks, where the largest community has 310 nodes but the smallest communities only has 2 nodes, which may be a bias in the calculation of the algorithm. Therefore, we only considered the largest 9 communities for further analyses. 

Analysis of community detection:

After community detection, we then analysed whether and how much the ties formed between nodes are affected by the communities they are in. We propose an algorithm to study the relationship between edge density of the community and the ratio of in-community ties, and the relationship between the size of community and the ratio of in-community ties, where,

Edge density = ties in community/potential ties in community

In-community ties: we deem a tie as in-community tie if both users (source node and target node) are from the same community. 

Out-community ties: we deem a tie as out-community tie if users (source node and target node) are not from the same community

In-community ties ratio of a community = in-community ties of a community / in-community ties of a community + out-community ties of a community

<img src="/images/IMG_0220.jpg" alt="IMG_0220"
 title="IMG_0220" width="450" />
