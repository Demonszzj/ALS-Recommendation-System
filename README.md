# Overview

In the project, we will apply the tools we have learned in this class to solve a realistic, large-scale applied problem.
Specifically, we built and evaluate a collaborative-filter based recommender system. 

## The data set

In this project, we'll use the [ListenBrainz](https://listenbrainz.org/) dataset, which is stored in Dataproc's HDFS at `/user/bm106_nyu_edu/1004-project-2023/`.

This data consists of *implicit feedback* from music listening behavior, spanning several thousand users and tens of millions of songs.
Each observation consists of a single interaction between a user and a song.
**Note**: this is real data.  It may contain offensive language (e.g. in song titles or artist names).  It is entirely possible to complete the assignment using only the interaction data and ID fields without investigating metadata.


## Basic recommender system

1.  As a first step, we partitioned the interaction data into training and validation samples.
    This will reduce the complexity of our experiment code down the line, and make it easier to generate alternative splits if we want to measure the stability of your implementation.
    We will also need to aggregate the individually observed interaction events into count data.

2.  Before implementing a sophisticated model, we began with a popularity baseline model.
    This should be simple enough to implement with some basic dataframe computations, and should be optimized to perform as well as possible on our validation set.
    Evaluate the popularity baseline (see below) before moving on to the next step.

3.  Our recommendation model should use Spark's alternating least squares (ALS) method to learn latent factor representations for users and items.
    The documentation on the [pyspark.ml.recommendation module](https://spark.apache.org/docs/3.0.1/ml-collaborative-filtering.html) before getting started.
    This model has some hyper-parameters that we tuned to optimize performance on the validation set, notably:
      - the *rank* (dimension) of the latent factors,
      - implicit feedback parameter (alpha),
      - the regularization parameter.

### Evaluation

Once we made predictions—either from the popularity baseline or the latent factor model—we evaluated accuracy on the validation and test data.
Evaluations is based on predictions of the top 100 items for each user, and report the ranking metrics provided by spark.
Refer to the [ranking metrics](https://spark.apache.org/docs/3.0.1/mllib-evaluation-metrics.html#ranking-systems) section of the Spark documentation for more details.

As a general rule, we explored ranges of each hyper-parameter that are sufficiently large to produce observable differences in our evaluation score on the validation data.


### Using the cluster

In this project, we used a Dataproc cluster, which is a limited, shared resource. Too many people run inefficient code simultaneously, it can slow down the entire cluster for everyone. Thus, some of our parameters are constrained: Rank <= 100.

## Extensions

We did another two extensions to the basic ALS recommender system.:

  - *Comparison to single-machine implementations*: compare Spark's parallel ALS model to a single-machine implementation, e.g. [lightfm](https://github.com/lyst/lightfm) or [lenskit](https://github.com/lenskit/lkpy).  Your comparison should measure both efficiency (model fitting time as a function of data set size) and resulting accuracy.
  - *Fast search*: use a spatial data structure (e.g., LSH or partition trees) to implement accelerated search at query time.  For this, it is best to use an existing library such as [annoy](https://github.com/spotify/annoy), [nmslib](https://github.com/nmslib/nmslib), or [scann](https://github.com/google-research/google-research/tree/master/scann) and you will need to export the model parameters from Spark to work in your chosen environment.  For full credit, you should provide a thorough evaluation of the efficiency gains provided by your spatial data structure over a brute-force search method, as well as any changes in accuracy induced by the approximate search.

## Timeline

- [ ] 2023/04/21: data pre-processing, train/validation partitioning, popularity baseline model.
- [ ] 2023/05/05: Working latent factor model implementation on subsample of training data.
- [ ] 2023/05/12: Scale up to the full dataset and develop extensions.
- [ ] 2023/05/16: final project completed.
