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

The dataset contains 200,840 training handwritten grapheme images. Optical character recognition is particularly challenging for Bengali since there are in total 168 classes of Grapheme Root, 11 classes of vowel diacritics, and 7 classes of consonant diacritics in grapheme language. 

Below are three plots that show the distributions of class values in the dataset. And we plotted the most frequent 20 values of grapheme root and all values of vowel as well as consonants diacritics. Grapheme root and vowel diacritics are distributed in a smooth trend, but we can see a big gap in the use of consonants diacritics after class_0. 

<div style="text-align:center;">
  <a href="https://tongxinw.github.io/bengali.ai/img/grapheme_root.png">
    <img src="https://tongxinw.github.io/bengali.ai/img/grapheme_root.png" alt="Test">
  </a>
</div>
<br/>

<div style="text-align:center;">
  <a href="https://tongxinw.github.io/bengali.ai/img/vowel.png">
    <img src="https://tongxinw.github.io/bengali.ai/img/vowel.png" alt="Test">
  </a>
</div>
<br/>

<div style="text-align:center;">
  <a href="https://tongxinw.github.io/bengali.ai/img/consonant.png">
    <img src="https://tongxinw.github.io/bengali.ai/img/consonant.png" alt="Test">
  </a>
</div>
<br/>

Further exploring into the actual handwriting of Bangali words, we found that many grapheme images did not fit into the center, or even got cropped, as shown in sample images below. More specifically, their backgrounds are not clear, indicating that there is noise on images and we need to preprocess the dataset using ZCA whitening technique in order to eliminate this noise. Belows are visualizations of the top 10 and last 10 classes of grapheme root as well as the visualization of the vowel and the consonant. One challenge we might encounter in the future model training process is that the dataset does not have sufficient training points for the last few classes, and classification might not be as accurate as top classes.

<div style="text-align:center;">
  <a href="https://tongxinw.github.io/bengali.ai/img/Sample EDA_Grapheme Root.PNG">
    <img src="https://tongxinw.github.io/bengali.ai/img/Sample EDA_Grapheme Root.PNG" alt="Test">
  </a>
</div>
<br/>

<div style="text-align:center;">
  <a href="https://tongxinw.github.io/bengali.ai/img/Sample EDA_last 10_root.PNG">
    <img src="https://tongxinw.github.io/bengali.ai/img/Sample EDA_last 10_root.PNG" alt="Test">
  </a>
</div>
<br/>

<div style="text-align:center;">
  <a href="https://tongxinw.github.io/bengali.ai/img/Sample EDA_vowel.PNG">
    <img src="https://tongxinw.github.io/bengali.ai/img/Sample EDA_vowel.PNG" alt="Test">
  </a>
</div>
<br/>

<div style="text-align:center;">
  <a href="https://tongxinw.github.io/bengali.ai/img/Sample EDA_consonant.PNG">
    <img src="https://tongxinw.github.io/bengali.ai/img/Sample EDA_consonant.PNG" alt="Test">
  </a>
</div>
<br/>

## Building Baseline model (updated)

The baseline model gives the initial accuracy scores and weights for further training process. We adapted [Kaushal Shah's Kaggle notebook](https://www.kaggle.com/kaushal2896/bengali-graphemes-starter-eda-multi-output-cnn) for building the baseline model. We loaded and saved the data into the working space, and resized the images by center cropping the region of interest. The model includes 2D convolution layers, batch normalization, max pooling and dropouts to eliminate the risks of vanishing/exploding gradients problems and overfitting. 

These 200,840 training handwritten grapheme images are stored separately in four parquets. For baseline model, due to the inefficiency of the model as well as the large size of training sets, we only trained the first parquet. The baseline accuracy scores are 88.5% for grapheme root, 94.3% for vowel and 96.0% for consonant.

### Tensorboard

Tensorboard visualizes how the model performed and checks underfitting/overfitting condition. For each component of the character, we got a tensorboard that traces the accuracy and the loss. The figure below is one of the tensorboard visualizations. 

<div style="text-align:center;">
  <a href="https://tongxinw.github.io/bengali.ai/img/e4accuracy.png">
    <img src="https://tongxinw.github.io/bengali.ai/img/e4accuracy.png" alt="Test">
  </a>
</div>
<br/>

<div style="text-align:center;">
  <a href="https://tongxinw.github.io/bengali.ai/img/e4loss.png">
    <img src="https://tongxinw.github.io/bengali.ai/img/e4loss.png" alt="Test">
  </a>
</div>
<br/>

As we can see, the accuracy for epoch_dense_4, here represents consonant training is 96%, and is 97.5% for the validation. There is no overfitting or underfitting during the training process.

### Data Augmentation

A good data augmentation can boost the performance of CNN model drastically. Thus, we tried to build a Multi-output generator in order to randomly rotate, zoom, and shift images for creating more training data. 

## References
[Kaggle notebook](https://www.kaggle.com/kaushal2896/bengali-graphemes-starter-eda-multi-output-cnn)

[Kaggle notebook](https://www.kaggle.com/gpreda/bengali-ai-handwritten-grapheme-getting-started)

[Article](https://towardsdatascience.com/image-augmentation-for-deep-learning-histogram-equalization-a71387f609b2) on data augmentation

