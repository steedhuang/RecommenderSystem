# RecommenderSystem
Cross Product Modulo Summation Recommender System

# Recommender System based on Sampling-Embedding-Recommender Framework
# Modified with Cross Product Modulo Summation (CPMS) Algorithm*

The code was reconstructed by Dabu Zhang in 2019, the algorithm has been explained in patent by Steed Huang between 2020-2021, this version code is translated and tested by Tommy Zhou in 2022.

## Work Flow
 - Sampling
   - User behavior item sequence
   - User-item heterogeneous graphs
   - Learning networks
   - ...
 - Embedding
   - Word2vec
   - Deepwalk
   - Node2vec
   - ...
 - Recommender
   - User-item cosine similarity ranking
   - Item-based collaborative filtering
   - ...


## built-in Datasets
 - ml100k
 - ml1m
 - mooc
 - goodbooks

## Configurations
Use `config.py` for configurations. Modify the `PROJECT_ROOT_PATH` based on the project file location, and `VIRTUALENV_PYTHON_PATH` based on the location of python descriptor.

## Setup
0. Use virtualenv with python 2.7 and install pip dependencies.
```
# 1. Activate virtualenv
# 2. Install pip dependencies
pip install -r requirements.txt
```  
1. Unzip datasets to the root folder of the project.
2. Build local dataset `origin_data.db` using Sqlite
```
cd utils
python db.py
```
3. Run main program
```
python main.py
```

## Build your own algorithm
Sampling: partially inherit the `Sampling` class in `sampling/__init__.py`
Embedding: partially inherit the `SkipGramModel` class in `embedding/__init__.py`
Recommender: partially inherit the `Recommender` class in `recommender/__init__.py`
Use `Workflow` class in `workflow/__init__.py`to select the strategy to build your own algorithm. Workflow is based on `Strategy pattern` in OOP.

## top-k evaluate protocol
1. candidate items as items not appear in user i's train-set history.  
2. For each positive item in the test set, we randomly sample 50 negative samples that have no interaction records with the target user. Then, we rank the list consisting of the positive item and 50 negative items
3. (TODO)the leave-one-out evaluation  

## grid search for best hyperparameters (For dataset using k-fold train-test split)
```
cd hyperparameter/
python grid_search.py
```

## grid search for best hyperparameters (For dataset using time based train-test split)
item heterogeneous graphs
```
cd scripts/item_graph/
python generate_edgelist.py
python generate_embedding_files.py
python evaluate_embedding_results.py # grid search
```
user-item heterogeneous graphs
```
cd scripts/user-item-graph
python generate_edgelist.py
python generate_embedding_files.py
python evaluate_embedding_results.py # grid search
```

## Issues
No penalties for negative samples

## A fix to OpenBLAS process crash
```
export OPENBLAS_NUM_THREADS=1
```

*Note on CPMS:

The cosine-based approach defines the cosine-similarity between two users x and y as cos(x,y)=x.y/(|x|.|y|)), when two vectors are apart from each other they may still have very big cosine value, if they are on the same direction (or small angle apart). The distance-based approach defines distance-similarity between two users x and y as dist(x,y)=|x-y|, when two vectors are apart from each other they may still have very small distance value, if they are on the opposite direction. To count both distance and angle information here we use Cross Product Modulo Summation, which is essentially the area between two vectors. Distance and angle are both reflected in the aera: 		 	
       CPMS=|x|.|y|sin(x,y)/Sqrt(|x|.|y|+x.y)=sqrt(|x|.|y|-x.y))  
Bear in mind that sin = sqrt(1-cos^2). The cross product (occasionally directed area product, to emphasize its geometric significance) is a binary operation on two vectors in a three-dimensional oriented Euclidean vector space. Note that more than two vectors can be constructed the similar way for CPMS with cubic or higher root!

Here are the benchmark samples:

Precision@5
CMPS         23.03%
SVD          6.58%
NMF          4.93%
KNN          4.76%
CoClustering 4.29%
Random       1.63%

Recall@10
CMPS         84.67%
SVD          31.28%
NMF          30.37%
KNN          30.22%
CoClustering 28.66%
Random       15.26%






