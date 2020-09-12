---
layout: post
comments: true
title: Diverse Decision Boundaries
---

Diverse Decision Boundaries a.k.a. Diverse Hypotheses is one of the projects I worked on this past summer wth Prof. Pulkit Agrawal
and PhD student Jacob Huh at the Improbable AI group at CSAIL MIT.

The overarching goal of the project is to investigate and solve the problem of neural networks learning 
shortcuts/biases in a dataset through having diverse hypotheses.

In this entry I will introduce the problem and give an overview of our work while preserving the confidential
nature of the preliminary results.

### Motivating example
Consider the image below, where at the top we are showing some training data of two images with labels A and B
 and at the bottom we
have a test example that we want to classify after training on the train data.
The question is whether the label for the test sample is A or B?
Recognizing the dataset as MNIST, or just having common sense, you might be tempted to say the test label is A, because
it is associated with the digit 2 in the train set. However, what if we truly wanted to classify the images by background color, making
the desired prediction label to be B? 


<img src="/files/motivating_example.png" width="403px"/>

In the synthetic example above we dealt with two MNIST digits
 with colored backgrounds, but this problem can be encountered in real life images too, for example having grass and yellow sand 
 with a dog and camel respectively as train set, but having a dog in sand or camel in grass during test time.

### When in doubt, DNNs tend to capture the "easier" solutions
The idea is that neural networks don't really "know" which feature we want to be captured (color, shape or something else)
and unless the dataset is sufficiently free of biases (which are often irrelevant cues that can be  exploited for
successful prediction on the training set, that fail to generalize on test data) the DNNs resort to using the bias as 
a prediction signal as they have no reason to try and capture the
full intricacies of the data despite the full capacity to do so. 
Many experimental results verify this phenomena, such as the simple example below, where a DNN 
trained on biased data fails to generalize when the bias shifts to a different class.

<img src="/files/example_mnist.png" width="403px"/>

### Proposed Solutions
One seemingly straightforward solution to this bias problem is to simply de-bias your dataset :). However,
this is often not easy, especially with real world datasets where data collection and even quantifying bias 
may be impossible. Many proposed approaches rely on somehow changing the training data itself, by augmentation or other
methods for de-biasing. Other rely on quantifying bias and encouraging the network to stay away from the bias.
Another method that we propose is to have diverse decision boundaries that capture different features.
The idea is that if we have many hypotheses we might be able to find one which captures the correct predictive features.
There are many ways to realize the idea of having diverse decision boundaries, but the simplest approaches involve training with
a regularization term that encourages diversity, or using evolutionary training strategies.
### Related research questions
We conduct series of experiments for related research questions that provide promising experimental results
that can guide further research towards the overarching goal:
1. Is there a subnetwork in a larger network that can capture the correct features in a biased dataset?
2. Is a subnetwork encoding bias different from a subnetwork encoding the true predictive feature or do they share parameters?
3. Does a subnetwork encoding bias only learn the bias and not the true predictive feature?
4. Does a fortuitous initialization of a subnetwork encoding the true predictive feature encode it by itself, without any training? 


### Conclusion
While a great work has already been done to address this issue, dealing with learning bias in datasets during DNN training remains an active research area as one of the most pressing problems
in CV and DL today.

