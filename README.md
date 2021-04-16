# Rapid Fire Market Panel Response Summarization

### Table of Contents
* [Overview](#general-info)
* [Dependencies](#dependencies)
* [Proposed Solution Architecture](#proposed-solution-architecture)
* [Technicalities](#technicalities)
* [Initial Results](#initial-results)

### Overview
The presented problem concerns that of a moderator panelling an audience regarding the favorite aspects of social media. When the audience is large in number and participants are likely "on the clock" it would be unfeasible for the moderator to review and address every participant's comment. The ability of the moderator to quickly understand the mood in the room allows the moderator to carry on the conversation in a more organic fashion and gain better insights into their product preferences. 

By harnessing the power of natural language processing our goal is to quickly condense a large volume of comments into a easily digestable format from which the moderator may carry on the conversation. 

The available survey data includes:
1. The participants' repsonses
2. The participants' preference of one random participant's responses to another 
3. The participants' agreement or disagreement with another participant's response 

### Libraries
* Python 3.9
* Transformers 4.4.2
* Sent2vec 0.2.0
* Pytorch 1.8.1
* Scikit-learn 0.24.1
* Matplotlib 3.4.1
* Pandas 1.2.4

### Proposed Solution Architecture
The problem could be approached in many ways. The following method has shown to be capable of providing insights to the operator in around ten seconds. 

The first step is to use the participant response meta-data in order to understand the popularity of each response. A popularity score is assigned to each comment based on the opinions of other participants in the audience. A small number of comments are going to be vastly unpopular and those will be filtered from the comment set as they do not represent the majority and contribute unnecessary noise. 

Next, the filtered comments will be clustered together based on their contextual similarity. Once our thoughts have been clustered into broader "ideas" we are in a better position to summarize all thoughts to the moderator. The moderator will be provided with a) the most representative comment from each cluster, and b) the most popular comment from each cluster. Results are be sorted in descending order based on popularity. 

### Technicalities
The comments are assigned a popularity score using simple arithmetic weighting. For the agreements dataset an agreements popularity score between zero and one is calculated by dividing the total number of times the comment was presented by the total number of agreements. For the binary choice dataset a binary choice popularity score between zero and one is calculated by dividing the total number of times the comment was presented as a choice by the total number of times the choice is chosen. The final popularity score is determined as the weighted sum of the two sub-scores. 

Sentences are vectorized using a pre-trained distilBERT language representation model. This model is a lightweight (40% smaller and 60& faster) version of the famous BERT model which retains 97% of it's language understanding capabilities. This is critical in a situation such as this where models are being deployed on the fly. Given more time on this project I'd also be interested in exploring fine-tuning the distilBERT model as well as exploring Google's Universal Sentence Encoder which have been specifically trained for sentence similarity tasks. In hindsight simple, non-contextual sentence representations based embeddings such as word2vec or gloVe should have been used as benchmarks prior. 

Given the thought vectors we then cluster them using DBSCAN. DBSCAN is selected as we are able to create clusters without needing to specify the specific number of clusters. This is important as we do not know the number of various "ideas" within our audience prior. We selected epsilon in the final model in order to maximize the number of clusters so that we can pool a diverse set of ideas. Finally, given our clusters we may use cosine similarity to determine which thoughts are closest to the center of the cluster. These thoughts along with the most popular thought from each cluster will be presented to the moderator. 


### Initial Results
The following table shows what the output of this project could potentially look like. Here we've condensed 40 thoughts down to nine in order from most to least popular all in around ten seconds. 

![Results](result.png)

These comments capture the core ideas I gathered upon reading all forty comments which include:
* Connection with friends and family
* Easy and convenient
* User control (filtering / blocking)
* A segment of users who do not seem to like anything about social media 
