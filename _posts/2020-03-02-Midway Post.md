---
layout: post
title: Midway blog post
subtitle: Midway. 
bigimg: /img/saiyan_bk.jpeg
gh-repo: tongxinw/bengali.ai
gh-badge: [star, fork, follow]
tags: EDA, Data Augumentation
comments: true
---


## Updated Exploratory Data Analysis

Further exploring into the actual handwriting of Bangali words, we found that many grapheme images did not fit into the center, or even got cropped, as shown in sample images below. More specifically, their backgrounds are not clear, indicating that there is noise on images and we need to preprocess the dataset using ZCA whitening technique in order to eliminate this noise. Belows are visualizations of the top 10 and last 10 classes of grapheme roots as well as the visualizations of the vowels and the consonants. One challenge we might encounter in the future model training process is that the dataset does not have sufficient training points for the last few classes, and classification might not be as accurate as top classes.

<div style="text-align:center;">
   <img src="https://tongxinw.github.io/bengali.ai/img/Sample EDA_Grapheme Root.PNG" alt="Test">
</div>
<br/>

<div style="text-align:center;">
  <img src="https://tongxinw.github.io/bengali.ai/img/Sample EDA_last 10_root.PNG" alt="Test">
</div>
<br/>

<div style="text-align:center;">
  <img src="https://tongxinw.github.io/bengali.ai/img/Sample EDA_vowel.PNG" alt="Test">
</div>
<br/>

<div style="text-align:center;">
  <img src="https://tongxinw.github.io/bengali.ai/img/Sample EDA_consonant.PNG" alt="Test">
</div>
<br/>

## Data Augmentation 

A good data augmentation can boost the performance of CNN model drastically. Thus, we tried to build a Multi-output generator in order to randomly rotate, zoom, and shift images for creating more training data.

## Model Improvements:

**Activation Function:**

Tuning activation function has major effects on the accuracy scores. The baseline model used ReLU as the activation function, and we switched it to ELU in the final model. ReLU did not perform so well in our model since it suffers from a problem known as dying ReLU, meaning neurons will stop outputting anything but 0. Instead, we adopted ELU that outforms ReLU by alleviating vanishing gradients problem.

**Learning Rate Scheduler:**

Also, we changed the learning rate from constant value to learning rate scheduler which decreases the learning rate during the training process. We performed time-based decay, step decay and exponential decay, and the result turned out that exponential decay gave the best score.

**Runtime Boosting:**

Furthermore, we applied a boosting algorithm into the training process. It turns a weak model to a stronger one by fixing its weaknesses. The algorithm works by initially training a model on a small dataset and then taking all of the training data to test the model. When we trained the first model, we addressed its deficiencies, which allows us to train the subsequent model from baseline instead from scratch. During the baseline model training process, we saved the model weights for the final training. For the subsequent model training, we tried to freeze the convolutional layers, and the training time was even shorter but the accuracy is lower than the score from unfreezing the layers. 


## References
[Kaggle notebook](https://www.kaggle.com/kaushal2896/bengali-graphemes-starter-eda-multi-output-cnn)

[Kaggle notebook](https://www.kaggle.com/gpreda/bengali-ai-handwritten-grapheme-getting-started)

[Article](https://towardsdatascience.com/image-augmentation-for-deep-learning-histogram-equalization-a71387f609b2) on data augmentation

Shout out for Sayan Samanta providing this beautiful Bengali handwritten image for us!
