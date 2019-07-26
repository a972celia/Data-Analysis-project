<h2>K means clustering</h2>
<p>Clustering, however, is an example of unsupervised learn‐ ing, in which we work with completely unlabeled data (or in which our data has labels but we ignore them).</p>
<b>The idea</b>
<p>One of the simplest clustering methods is k-means, in which the number of clusters k is chosen in advance, after which the goal is to partition the inputs into sets S1, ..., Sk in a way that minimizes the total sum of squared distances from each point to the mean of its assigned cluster.</p>
<p>There are a lot of ways to assign n points to k clusters, which means that finding an optimal clustering is a very hard problem. We’ll settle for an iterative algorithm that usually finds a good clustering:</p>
<p>1. Start with a set of k-means, which are points in d-dimensional space.</p>
<p>2. Assign each point to the mean to which it is closest.</p>
<p>3. If no point’s assignment has changed, stop and keep the clusters.</p>
<p>4. If some point’s assignment has changed, recompute the means and return to step 2.</p>
<b>Implementation</b>
<p>In this folder, we put two applications of Kmeans clustering with python, one is built from scratch, another is SK-Learn Implementation.  </p>
