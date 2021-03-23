---
layout: post
title: Identifying Wildlife in Camera Trap Images
---

As someone with a lifelong passion for ecology and wildlife conservation working with data focused on wildlife populations for my final project at Metis was an obvious choice. Early on in exploring what data science is and what it can do I became interested in computer vision, much of animal science in my experience has been about visual observations from identifying an animal and its species to assessing body condition and behavioral cues, which is why I wanted to use this project at a starting point to explore data science applications in the realm of wildlife conservation. I noticed that much of the labeling of image data for the creation of usable datasets is done manually, using deep learning image classification I want to at least semi-automate this process. The more quickly and efficently these types of images can be curated into usable datasets the more quickly they can be used in further modeling towards actionable insights. I also thought this would be a great project to continue to build on, if I can build a model that succesfully identify species purhaps in the future I could add to this model or a new one that does a more complex identification such as behavior based off body positioning.

A summary of my project work as well as some future goals can be found in [this presentation](placeholder). 

## Data Used

This project used data from seasons 1-4 of Snapshot Serengeti, these datasets and ones from later seasons can be found [here](http://lila.science/datasets/snapshot-serengeti). Due to the sheer size of these files and the time constraints of the this project I used resized versions of the seasons which can be found [here](https://community.drivendata.org/t/resized-dataset-is-now-available/3874) this still amounted to over 100 GB of images, but was significantly more managable than terabytes. 

## Tools Used
- Google Cloud Platform
- fast.ai's vision library
- pytorch
- PIL.Image
- pandas, numpy, json, os
- random, math, glob
- matplotlib.pyplot

## Exploratory Data Analysis and Data Cleaning

Each season of images has an accompanying json file that image annotations, while some of this data such as image datetime information may be interesting to explore inthe future I mostly focused on 'image_id' which is essentially the image file and 'category_id' which is the relevant labeling information. The seasons I worked with had 49 distinct labels or classes, looking at the distribution of these classes class imbalance was very apparent. Ideally I would augment the existing data or add new images for the smallest classes, however I was concerned with potential data bleed between the training and validation sets using data augementation and hesitant to add even more data to an already very large dataset. Images that were classified as 'empty' however were significantly more prominent than all other classes so I chose to remove 90% of those images to help rebalance the classes, this may seem like a drastic amount to decrease this class by but even with this reduction it remained as one of the most prominent classes. This was not only to prevent the model from over-predicting hte 'empty' class but also had the added benefit of reducing training times.  
## Transfer Learning and Initial Training

I standardized the image sizes for training, and while google cloud platform did provide access to more RAM and more powerful gpus than available with my local machine these helpful resources are not infinite and I still had to train in batches to avoid running out a memory. To create my model I implemented transfer learning techniques using Resnet50 starting point that was pretrained using the ImageNet database of over a million images, some of which were animals. Due to the noises of the images used; varying lighting and weather conditions, only partial images of animals, motion blurring, and so on, I thought a deeper neural network may be more effective than one with fewer layers. However deeper neural networks can encounter vanishing gradient problem, as the gradient becomes smaller and smaller the wights and biases from the intial layers are not effectively updated with each training session. This is why I chose Resnet50 as it is a residual network using residual connections between layers preventing the vanishging gradient problem.  To evaluate performance  I focused on F1 Macro score, due to imbalance noticed earlier I wanted to show perfomance on those smaller classes, and validation loss, this model uses the categorical cross entropy loss fuction which penalizes the model the more confident it is in incorrect labeling. Fast.ai's inbuilt learning rate finding function was particularly helpful during the training of my model as it showed at what learning rates the los was at its smallest. To avoid potentially losing work as a saftey precaution I saved a copy of the model after every epoch during training. I first trained with frozen layers and as preformance began to plateau I continued training with unfrozen layers stopping model training when perfomance plateaued again.

![Initial validation sample](/Images/stage_3_validation_sample.png)

![inital top losses](/Images/stage_3_top_losses.png)

## Evaluating Perfomance and Additional Training

At this point the model has a validation loss score of 0.29 and an F1 Score of 0.70, while not bad for my first time creating an image classifier I was curious to see where the model was under-performing. Some of the results I saw were what I expected, the model mislabeling species that are very similar such as confusing a Grant's gazelle with a Thomson's gazelle, however others were more surpising. It appeared the model was mislabeling zebras and wildebeests and vice versa, which initially was confusing as they are relatively visually distinct species. However the more I looked at the dataset the cause for the model's confusion became clear; every image in the dataset has a single label but some images have multiple species. Since zebras and wildebeests are gregarious grazing species they are often in images together, but the images only have one label thus making it more difficult for the model to differentiate. This also lead the to issue of the model correctly identifying one species in an image but if it didn't match the dataset's labeling marking it as a mislabel. Another area the model was experiencing the most loss from was correctly identifying 'empty' images that the dataset had mislabled as something else, this is due to many of the images being taken as part of a sequence of images which all share the same label but the species they are labeled for may no longer be present in some of these images. 

Given the short time period this project was initially due in I did not have a particularly time efficent way to address the issue of single labels on multi-species images so I instead focused on the mislabeling related to image sequences. I created a new version of the test dataset that only used the first frame from each image sequence set under the assumption the first image was most likely the one labeled correctly. I then went on to fine tune my model using this version of the dataset, I used the same seed for the train validation split as I had previously in the hopes of reducing data bleed as much as possible. This halved the validation loss and increased the F1 Score to 0.79, while many of the issues that were present with the original dataset are still present in this version they appear to be less frequent. Overall it appears that the model is more confident in its correct labeling and less confident in its incorrect ones while overall performances is still somewhat stiffled by problems with the dataset itself.

![Fine tuned Results](/Images/fined_tuned_validation_sample.png)

![Fine Tuned Losses](/Images/fine_tune_top_losses.png)

![Validation sample confusion Matrix](/Images/giant_confusion_matrix.png)

This confusion matrix of a subsection of the validation set further reinforces the idea that the model fails the most when labeling images as empty when the dataset says they are not empty and mislabeling species that congregate together. 


## Classification App and Future Plans

Instead of just showing the model identify species in images it hasn't seen before correctly I wanted to make testing my model more interactive so I created the app linked to earlier. This has the added benefit of being able to test model performance on non-camera-trap images. I did notice in the process of testing this app that it does have better more consistent performance on images from camera traps than it does with images from google. For example I tested it with a google image of a leopard, one of the classes it had seen fewer examples of, and it identified it as a cheetah; however inputing a camera trap image of a leopard from snapshot serengeti it was able to correctly identify it as a leopard. The app also has an educational section which provides information about a species as well as information about some of the difficulties faced in trying to conserve that species population. [Here](https://github.com/Jilliane1993/classifier_app) is my repository for my current app if you are interested in the code behind it. 

I have many ideas on how to improve this model and different directions I could take this project in the future. The mislabeling issues are the most important to be addressed, concatenating image sequences into a single image or manual relabeling images of high loss with the correct label are potential ways to address this issue. I also am considering implementing bounding boxes and labeling the individuals in images instead of the image as a whole; there are likely some image relabeling steps involved with this as well but this would also allow for the creation and usage of count data from the images. The app as it currently is can be a bit slow to start so I want to look at other deployment methods to increase perfomance in the app itself. 

As I develop my skills as a data scientist eventually I would like to turn this modeling workflow into a mobile app. Many people are familiar with the issue of animals disappearing from their natural habitats but less often spoken of is the issue of new animal populations appear in non-native environments. I want to create an app that can be used to identify animal species in an image a user takes, based off locational data determine if that species is native or invasive, and if it is an invasive species link the user to local resources like animal control. If it is a native species perhaps it coule provide the user with interesting information similar to my current app. This would not only educate users but get them involved in wildlife conservation.

Here's a link to app I made for my Wildlife Image Classifier. I apologize if it is a bit slow to load. For the best most consistent performance from my image classifier I recommend using images from Snapshot Serengeti. 

[![Binder](https://mybinder.org/badge_logo.svg)](https://mybinder.org/v2/gh/Jilliane1993/classifier_app/HEAD?urlpath=%2Fvoila%2Frender%2Fdeployment.ipynb)
