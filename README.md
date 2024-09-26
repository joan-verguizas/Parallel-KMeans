# Parallel-KMeans

### Course: Management and Analysis of Physics Datasets (2022-2023)

### Master's Degree: Physics of Data, University of Padova (IT)

## Overview

This project was developed as part of the final exam for the Management and Analysis of Physics Datasets course during the 2022-2023 academic year. The work was conducted in pairs during my first year in the Master's degree program in Physics of Data at the University of Padova.

The goal of the project was to develop an algorithm that implements a parallel version of K-means clustering. To achieve this, we utilized the PySpark library, which supports distributed computing algorithms, and employed the CloudVeneto resources provided by the University of Padova to implement the cloud-based architecture.

## Data

The dataset we have worked with is the [kdd99 dataset](https://kdd.ics.uci.edu/databases/kddcup99/kddcup99.html) which contains network traffic data of different types of malicius activity attacks. Therefore, our goal is to group the points of our dataset in k different clusters based on their similarity. 

## Methods

### Kmeans

As we said, our goal is to partition the given dataset into distinct groups, known as clusters. In order to do that, Kmeans algorithm is typically used. The fundamental principle behind it is to assign data points to clusters in a way that minimizes the sum of squared distances between each point and the centroid of its assigned cluster. The algorithm iteratively refines these assignments and updates cluster centroids until convergence is achieved.

While traditional k-means has proven effective, it is sensitive to the initial placement of cluster centroids. Poor initializations can lead to suboptimal solutions, where the algorithm converges to local minima rather than the global minimum of the objective function. This limitation gave rise to an improved initialization technique known as k-means++.

### Kmeans++

Kmeans++ is used to improve the initialization of cluster centroids which is performed randomly. It operates as follows:

- First centroid selection: The first centroid is chosen randomly from the data points.
- Subsequent centroids selection: For each remaining centroid, the probability of selecting a data point is proportional to the squared distance between that data point and the nearest already chosen centroid. This encourages points farther away from existing centroids to have a higher chance of being selected.
- Repeat until k centroids are chosen: The process continues until all k centroids are selected.

Then we proceed with the standard Kmeans algorithm.

K-means++ initialization leads to faster convergence and more accurate clustering results compared to random initialization. However, even with improved initialization, the standard k-means algorithm can still be slow and inefficient for large datasets due to its sequential nature. This drawback gave rise to a parallelized version of k-means, known as "|| k-means."


### || Kmeans

|| k-means is a distributed version of the k-means algorithm designed to leverage the power of parallel computing and efficiently handle large-scale datasets. Here's a brief explanation of how KMeans|| works:

- Sample data points in parallel: Instead of selecting one centroid at a time (like in KMeans++), KMeans|| selects several candidate centroids in parallel by sampling multiple points from the dataset. The probability of selecting a point is proportional to its squared distance from the closest centroid, similar to KMeans++.

- Repeat sampling process: This sampling process is repeated for several rounds, accumulating a large pool of candidate centroids (more than k centroids) to ensure good coverage of the dataset.

- Reduce candidate centroids: From the large pool of candidate centroids, a weighted selection is performed to reduce the number of centroids down to k.

- Parallel Lloyd's Algorithm: After the initial k centroids are chosen, the algorithm proceeds with the Lloyd's Algorithm using multiple cores at the same time.

KMeans|| offers a trade-off between accuracy and speed. While it sacrifices some of the precision of KMeans++ in selecting centroids, it gains scalability and can work efficiently with very large datasets across multiple machines.

## Results & Experiments

We implemented the parallel Lloyds algorithm with both random and parallel initialization and evaluate in both cases how the computational time and the performance change (cost) while changing the number of cores per worker and the number of partitions in our dataset. We do this for [1, 2, 3, 4] cores per worker and [8, 16, 32, 64] partitions. We will construct a heatmap with the results and perform 5 iterations for each distinct number of partitions and distributions.

Finally, we will plot the average cost for both random initialization and parallel initialization of the kmeans parallel implementation for the two best configurations. The average cost is lower when we apply a parallel initialization at the expenses of a bigger execution time.
