# PySparNN
Sparse (approximate) nearest neighbor search for python! This library is well suited to finding nearest neighbors in sparse, high dimensional spaces (like a text doccuments). 

Out of the box, PySparNN supports Cosine Similarity.

PySparNN can be easily extended with abritrary similarity metrics (Manhattan, Eculidian, Jaccard, etc).

If your data is NOT SPARSE & you don't require a custom distance function - please consider [annoy](https://github.com/spotify/annoy). 
It uses a similar-ish method and I am a big fan of it. As of this writing, annoy performs ~8x faster on their introductory example. 

The most comparable library to PySparNN is scikit-learn's LSHForrest module. As of this writing, PySparNN is ~25% faster on the 20newsgroups dataset. [Here is the comparison.](https://github.com/facebookresearch/pysparnn/blob/master/sparse_search_comparison.ipynb)

Notes:
* A future update may allow incremental insertions.

## Example Usage
```
import pysparnn as snn

data = [
    'hello world',
    'oh hello there',
    'Play it',
    'Play it again Sam',
]    

features = [dict([(x, 1) for x in f.split()]) for f in data]

cp = snn.ClusterIndex(features, data)

cp.search(features, threshold=0.50, k=1, return_similarity=False)
>> [[u'hello world'], [u'oh hello there'], [u'Play it'], [u'Play it again Sam']]

cp.search(features, threshold=0.50, k_clusters=2, k=2, return_similarity=False)
>> [[u'hello world'],
>> [u'oh hello there'],
>> [u'Play it', u'Play it again Sam'],
>> [u'Play it again Sam', u'Play it']]

```

## Requirements
PySparNN requires numpy and scipy. Tested with numpy 1.10.4 and scipy 0.17.0.

## How PySparNN works
Searching for a document in an collection of K documents is naievely O(K) (assuming documents are constant sized). 

However! we can create a tree structure where the first level is O(sqrt(K)) and each of the leaves are also O(sqrt(K)).

We randomly pick sqrt(K) items to be in the top level. Then for each of the K doccuments - assign it to the closest neighbor in the top
level.

This breaks up one O(K) search into two O(sqrt(K)) searches which is much much faster when K is big!

PySparNN takes advantage of matrix multiplication to do comparisons. Thus searching a point against the index becomes a series of sparse matrix operations.

## Further Information
http://nlp.stanford.edu/IR-book/html/htmledition/cluster-pruning-1.html

See the CONTRIBUTING file for how to help out.

## License
PySparNN is BSD-licensed. We also provide an additional patent grant.
