---
layout: post
title: Final blog post
subtitle: ...final, literally. 
bigimg: /img/saiyan.jpg
gh-repo: tongxinw/bengali.ai
gh-badge: [star, fork, follow]
tags: final
comments: true
---

## Background & Motivation

Bengali (bāṅlā / বাংলা or bāṅālī / বাঙালী) is the official language of Bangladesh and the second most spoken language in India. Considering the number of its users, there are significant benefits in developing models that can optically recognize images of the handwritten graphemes. Bengali has 49 letters (to be more specific 11 vowels and 38 consonants) in its alphabet, there are also 18 potential diacritics, or accents. This means that there are many more graphemes that added complexity results in ~13,000 different grapheme variations (compared to English’s 250 graphemic units). 

This Kaggle project hopes to improve on approaches to Bengali recognition and to classify the components of handwritten Bengali. We obtained the dataset from Kaggle Research Code Competition (need competatition link) and we are given the images of a Bengali handwritten grapheme. We separately classified three elements in the image: grapheme root, vowel diacritics, and consonant diacritics. This blog post serves as a documentation of our learning experience. All secourses we used are listed in the reference list. 

## Overview

For the initial blog post, we preprocessed the given Bengali.AI Handwritten images, performed EDA and built a baseline model using a Convolutional Neural Network to classify the three parts (root, vowel and consonant) of each grapheme. Also, image augmentation was applied to boost performance. For the final blog post, we further improved the baseline model to optimize accuracy scores and speed up the learning process through model reorganization, hyperparameters tuning, and transfer learning. The final accuracy scores we achieved are 97.12% for root, 97.75% for vowel and 98.59% for consonant.

## Exploratory Data Analysis

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

Further exploring into the actual handwriting of Bangali words, we found that many grapheme images did not fit into the center, or even got cropped, as shown in sample images below. More specifically, their backgrounds are not clear, indicating that there is noise on images and we need to preprocess the dataset using ZCA whitening technique in order to eliminate this noise.

<div style="text-align:center;">
  <a href="https://tongxinw.github.io/bengali.ai/img/Sample EDA_Grapheme Root.PNG">
    <img src="https://tongxinw.github.io/bengali.ai/img/Sample EDA_Grapheme Root.PNG" alt="Test">
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

## Building Baseline model

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

## Improving Baseline model

### Model and Hyperparameter Selection

For hyperparameter selection, we found tuning activation function and learning rate had major effects on the accuracy scores. The baseline model used ReLU as the activation function, and we switched it to ELU in the final model. ReLU did not perform so well in our model since it suffers from a problem known as dying ReLU, meaning neurons will stop outputting anything but 0. Instead, we adopted ELU that outforms ReLU by alleviating vanishing gradients problem.

Also, for the final model, we changed the learning rate from constant value to learning rate schedules which decrease the learning rate during the training process. We performed time-based decay, step decay and exponential decay, and the result turned out that exponential decay gave the best score.

Furthermore, we applied transfer learning into the training process. Transfer learning helps to build accurate models in a timesaving way. Instead of training from scratch, we started from a pre-trained model from similar input datasets. (Instead of training from scratch, we employed a similar data set to pretrain the model to the data set used in the current analysis) During the baseline model training process, we saved the model and the weights for the final training. The figure below is the summary of the saved model. We tried to freeze the convolution layers, and the training time was shorter but the accuracy is lower than from training the entire model. 

<div style="text-align:center;">
  <a href="https://tongxinw.github.io/bengali.ai/img/summary.png">
    <img src="https://tongxinw.github.io/bengali.ai/img/summary.png" alt="Test">
  </a>
</div>
<br/>

### Short-comings

The shortcoming of ELU activation function is that it is slower to compute than the ReLU function, thus it is inefficient to train large dataset on the model.

### Results

| Accuracy score | Baseline Model | Improved Model |
| :-------------- |:------------- | :------------- |
| Root | 0.8852 | 0.9712 |
| Vowel | 0.9444 | 0.9775 |
| Consonant | 0.9602 | 0.9859 |

## Possible Next Step

- Perform confusion matrix to evaluate predictions
- Modify DL model layers to improve accuracy

## References
[Kaggle notebook](https://www.kaggle.com/kaushal2896/bengali-graphemes-starter-eda-multi-output-cnn)

[Kaggle notebook](https://www.kaggle.com/gpreda/bengali-ai-handwritten-grapheme-getting-started)

[Article](https://towardsdatascience.com/image-augmentation-for-deep-learning-histogram-equalization-a71387f609b2) on data augmentation

Shout out for Sayan providing this beautiful Bengali handwritten image for us!
