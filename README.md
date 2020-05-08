
# Product Quantization

## Requirements
 * **Scipy 1.4.1**
 * **Numpy 1.18.2**
 * **Python 3.6**

## Part1: PQ for L1 Distance 

1. **data** is an array with shape (N,M) and dtype='float32', where N is the number of vectors and M is the dimensionality.
2. **P** is the number of partitions/blocks the vector will be split into. Note that in the examples from the inverted multi index paper, P is set to 2. But in this project, you are required to implement a more general case where P can be any integer >= 2. You can assume that P is always divides M in this project. 
3. **init_centroids** is an array with shape (P,K,M/P) and dtype='float32', which corresponds to the initial centroids for P blocks. For each block, K M/P-dim vectors are used as the initial centroids. **Note** that in this project, K is fixed to be 256.
4. **max_iter** is the maximum number of iterations of the K-means* clustering algorithm. **Note** that in this project, the stopping condition of K-means* clustering is that the algorithm has run for ```max_iter``` iterations.

## Return Format (Part 1)

The `pq()` method returns a codebook and codes for the data vectors, where
* **codebooks** is an array with shape (P, K, M/P) and dtype='float32', which corresponds to the PQ codebooks for the inverted multi-index. E.g., there are P codebooks and each one has K M/P-dimensional codewords.
* **codes** is an array with shape (N, P) and dtype=='uint8', which corresponds to the codes for the data vectors. The dtype='uint8' is because K is fixed to be 256 thus the codes should integers between 0 and 255. 

# Part2: Query using Inverted Multi-index with $L_1$ Distance (45 Points)


1. **queries** is an array with shape (Q, M) and dtype='float32', where Q is the number of query vectors and M is the dimensionality.
2. **codebooks** is an array with shape (P, K, M/P) and dtype='float32', which corresponds to the `codebooks` returned by `pq()` in part 1.
3. **codes** is an array with shape (N, P) and dtype=='uint8', which corresponds to the `codes` returned by `pq()` in part 1.
4. **T** is an integer which indicates the minimum number of returned candidates for each query. 

## Return Format (Part 2)

The `query()` method returns an array contains the candidates for each query. Specifically, it returns
* **candidates** is a list with Q elements, where the i-th element is a **set** that contains at least T integers, corresponds to the id of the candidates of the i-th query. For example, assume $T=10$, for some query we have already obtained $9$ candidate points. Since $9 < T$, the algorithm continues. Assume the next retrieved cell contains $3$ points, then the returned set will contain $12$ points in total.

## Implementation Hints

The implementation of `query()` should be efficiency. You should work out at least
1. How to efficiently extend Algorithm 3.1 in the paper to a general case with P > 2.
2. How to efficiently make use of `codes` returned by Part 1. For example, it may not be wise to enumerate all the possible combinations of codewords to build the inverted index. 

We will test the efficiency by setting a running time limit (more details later).

# Evaluation

Your implementation will be tested using 3 testcases (**30 points each, and another 10 points from the report**), your result will be compared with the result of the correct implementation. Part 1 and part 2 will be test **seperately** (e.g., you may still get 45 points from part 2 even if you do not attempt part 1), and you will get full mark for each part if the output of your implementation matches the expected output and 0 mark otherwise. 

**Note:** One of these 3 testcases is the same as the one used in the **submssion system**.

## How to run your implementation (Example)


```python
import submission
import pickle
import time

# How to run your implementation for Part 1
with open('./toy_example/Data_File', 'rb') as f:
    Data_File = pickle.load(f, encoding = 'bytes')
with open('./toy_example/Centroids_File', 'rb') as f:
    Centroids_File = pickle.load(f, encoding = 'bytes')
start = time.time()
codebooks, codes = submission.pq(data, P=2, init_centroids=centroids, max_iter = 20)
end = time.time()
time_cost_1 = end - start


# How to run your implementation for Part 2
with open('./toy_example/Query_File', 'rb') as f:
    queries = pickle.load(f, encoding = 'bytes')
start = time.time()
candidates = submission.query(queries, codebooks, codes, T=10)
end = time.time()
time_cost_2 = end - start

# output for part 2.
print(candidates)
```




    [{3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14}]



## Running Time Limits

As shown in the above snippet, we will be recording the running time of both part 1 and part 2. Your implementation is expected to finish with Allowed time Limit. If your code takes longer than Allowed Time Limit, your program will be terminated and you will recieve 0 mark.

For example, on CSE machine, e.g., **wagner**, your code is supposed to finish with in 3 seconds (for part 1) and 1 second (for part 2) for the toy example illustrated above.

## Project Submission and Feedback

For project submission, you are required to submit the following files:

1. Your implementation in a python file `submission.py`.

2. A report `Project.pdf` (**10 points**). You need to write a concise and simple report illustrating
    - Implementation details of part 1, especially what changes you made to accomodate $L_1$ distance.
    - Implementation details of part 2, including the details on how you extended the algorithm 3.1 to a more general case with P>2, and how you efficiently retrieve the candidates. 


**Note:** Every team will be entitled to **15 Feedback Attempts** (use them wisely), we will use the last submission for final evaluation.
