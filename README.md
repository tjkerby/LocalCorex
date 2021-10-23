# LocalCorex

# Todo List
* Explain that it isn't published and as such source code is not available yet
* Explain motivation
  * Why do we care about variable interactions
  * Why is this difficult to do when interactions change depending on where data is
* Explain main concept
  * Partitioning data
  * Creating representations with functions to map between representations
  * Perturbing one of the representations and transform into the other representation
* Show some results
  * MNIST
  * FMNIST
  * Synthetic Tree Data
* Discuss what I'm currently working on and what additional applications it has
  * Testing more kinds of relationships
  * Data Augmentation borderline cases
  * EDA tool for high-dimensional data

## Disclaimer
Local Corex is an algorithm that I designed in collaboration with Dr. Kevin Moon (USU), Dr. Greg Steeg (USC), and Myrl Marmarelis (USC). Because this work is not yet published I have not yet included all of the source code used to generate the plots that you will see below. This will change as soon as it is published. 

## Motivation behind Local Corex
Local Corex is a machine learning algorithm that learns variable interactions. Variable interactions are important for a number of reasons. When trying to predict the effect of a variable if there are interactions between this variable and other variables its effect is dependent on the other variables and not just its own value. In the realm of high dimensional data it is rare for a variable to be independent from all other variables. This is one of many reasons why making inference on high-dimensional data is difficult to do with much certainty. Variable interactions can also be used to try to understand systems and processes. For example it is well understood that genes often have an effect on the expression of other genes. Finding these related variables can help us determine whether that relationship is due to enhancers, repressors, or other transcription factors.

As difficult as it is to identify variable interactions it is made even more difficult when that interaction changes depending on where in the data you are looking at. For example, if we think of the pixels that light up for a given digit in a 28 by 28 pixeled image we expect that different digits will have different related variables or pixels. Despite this, most methods used to find variable interactions in high dimensional data try to find them without doing any kind of separation between groups (ie: latent factor analysis, corex, etc). Local Corex makes use of this knowledge by first separating the data into similar groups and then looking at the related variables.

## Main Idea of Localized Variable Interactions
The first step of Local Corex is to divide your data into like groups. Most clustering methods depend on euclidean distance to separate data into groups. This can be a problem since as the dimension of your data increases your ability to discriminate distances between points decreases. This is known as the curse of dimensionality. To get around this, we use a dimensionality reduction method called PHATE to create a lower dimensional representation of our data while still preserving as much information as possible. We can then run clustering methods on this embedding to separate the data into groups.

Once your data has been separated into sufficiently localized groups you shouldn't have the problem that we discussed earlier where your interactions change. Now you can apply other methods to find the variable interactions. 

## Main Concept of Local Corex
Local Corex separates data into localized groups as explained in the previous section. The way it finds related variables is by leveraging a lower dimensional representation of your data, a function that maps from your original representation space to the reduced representation space, and a function that maps from the reduced representation space to the original representation space. This can be achieved by training an Autoencoder that preserves the geometry of the manifold in the bottleneck layer. We use the GRAE algorithm to achieve this ***FIXME Need to cite***. 

With the previously described architexture we can train an autoencoder on a local partition. Then we can take the average representation in the reduced representation space (bottleneck) and perturb just one dimension at a time and transform back into the original representation space. As we do this for several perturbations we will get a sense of what original variables are associated with the single bottleneck variable. Because of the way this is calculated we can get a sense of the nature of the relationship whether it is positive or negative as well as the magnitude. To limit the number of variables that are associated with a given bottleneck variable we can add a threshold value. 

## MNIST Results
To give you a taste of what you get back here are some of the results when run on the MNIST dataset. The data was subdivided into 30 different groups using KMeans clustering and then had a GRAE model trained on each cluster we were interested in looking at. Below is an image of the average data point in the 24th partition.

![group_24_ave](https://github.com/tjkerby/LocalCorex/blob/main/MNIST/group_24_average.JPG)

As can be clearly seen, group 24 consists almost entirely of 2's.
