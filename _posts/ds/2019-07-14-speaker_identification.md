---
layout: post
title:  "Speaker identification with ML"
date:   2019-07-14 11:38:45 +0800
categories: datascience ml
---
> Building a ml pipeline that adds labels to audio files for speaker identification

## General Idea
We will build a tool that classifies speakers within the audio clip based on characteristics of the speaker, i.e. The model will be trained and tested on 130 hours of Hindi Audio. 

### Ideas for model
> DNNs (due to effective feature learning for Automatic Speech Recognition)
- Gaussian Mixture Model-Universal Background Model (traditionally succeful, but unsupervised. So, it is not customized for speaker identification)
- Siamese neural network using Mel Frequency Cepstral Coefficient (instead of raw features -> higher level representation of speaker-related characteristics)

### SNN Approach
1. Generate a model based on averaging speaker representation
2. Finetune the training model based on a Siamese framework (generate discrimintative feature space to avoid mis-identification regarding within-speaker variations)
3. Build a few baseline models to compare

### Reasoning for SNN
- Employ DNN for speaker feature extractor
- SNN for one shot feature extraction
- The main goal is to create a common feature subspace for discrimination between match and non- match pairs based on a distance metric.

## Overview
1. Problem definition (identifying speaker transitions-> open set text independent)
2. Single Numeric Metric (f1-score: precision (false positive) and recall (false negative), recall is most important -> look for how often it doesn't recognize a transition ))
3. Model (CNN with Siamese Framework -> one-shot learning + discriminative feature space)
4. Train test split, train model
5. Compare with baseline models (random + knn)


# Metrics
The metrics that we have decided on using to evaluate the performance of our models are:
- Accuracy
- Precision
- Recall
- AUC 
- F1Score

Among the models condsidered for this project, we choose the model that gets the best metric.
Metric-based techniques can compute the distances of adjacent intervals of the considered audio signal. Maxima of the distance measure indicate possible speaker changes and a threshold detection leads to the acceptance or rejection of the assumed speaker turns.
If an Unsupervised model like Gaussian Mixture Model is used then the metrics used would be:
- Rand Index
- Mutual information

# How I built it

## Padding
The first step for preparing an audio track for a model is to make sure that they have equal length. A mistake I realized late in the project is how I padded the features instead of the audio track itself, causing me to cut-off / add extraneous zeroes onto the n-dim feature array.

Hence, we should pad the audio in the time domain first.

## Vocal separation
In hopes for better accuracy of the model, we will separate vocals from the background noise with a common librosa technique...

```python
#build spec (y is time series, sr is sampling rate=22050 hz)
  y, sr = librosa.load(wav_file)

  #padding
  y = librosa.util.fix_length(y, int(max_audio_len))
  
  #compute spectrogram magnitude (mag of D raised to power) and phase
  S_full, phase = librosa.magphase(librosa.stft(y))

  S_filter = librosa.decompose.nn_filter(S_full,
                                        aggregate=np.median,
                                        metric='cosine',
                                        width=int(librosa.time_to_frames(0.5, sr=sr)))
  S_filter = np.minimum(S_full, S_filter)
  margin_i, margin_v = 2, 10
  power = 2

  mask_i = librosa.util.softmask(S_filter,
                                margin_i * (S_full - S_filter),
                                power=power)

  mask_v = librosa.util.softmask(S_full - S_filter,
                               margin_v * S_filter,
                               power=power)
  S_foreground = mask_v * S_full
  S_background = mask_i * S_full
  
```

## Feature extraction
As previously mentioned, we will focus on the following features:
- MFCCs
- Zero-crossing rate
- RMS
- Spectral roll off rate

To do so, we would require the Librosa library.

```python
#convert clean spectrogram back to time series
vocals = librosa.istft(clean_y)
#prepare features
mfccs = librosa.feature.mfcc(y=vocals, n_mfcc=40)
zcr = librosa.feature.zero_crossing_rate(y=vocals)
rms = librosa.feature.rms(S=clean_y)
spro = librosa.feature.spectral_rolloff(S=clean_y)
if proc == True:
  features = [np.array(mfccs), np.array(zcr), np.array(rms), np.array(spro)]
  return features
```

## Testing with a simple neural net first
Due to the limited time span, I had to first build a baseline model in order to demonstrate that the idea works.

Here's a simple neural network that has around 68-84% accuracy based on different random seeds.

We first get the features and classes, then split it into a train-test split and use the features as an input vector for the model, training it with the class labels (number of speakers). 

```python
# get raw features
feats = bldr.get_feats()
X = prep_feats(feats)
# get classes
y = (np.array(bldr.get_classes()))
print("Num X:", (X.shape), "Num Y:", len(y))
# Train test split
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.25, random_state=42)
```

> prep feats simply unstacks and normalizes the data due to the nature of the feature vectors (mfcc has 40 frames whilst the others are 1-d)

```python
def prep_feats(feats):
  feats_array = []
  for i in feats:
    res = []
    for item in i.values():
      item_list = list(item)
      stacked = (np.hstack(item_list))
      res.append(stacked)
    res = np.hstack(res)
    feats_array.append(res)
  normalizer = StandardScaler()
  normalized_feats = normalizer.fit_transform(feats_array)
  return normalized_feats
```

Since there are only 1-2 speakers in the audio given, I decided to encode the classes and use binary-crossentropy as the loss function. In the neural net, I also found it was best to use relu and sigmoid fx as the final activation function.

```python
lb = LabelEncoder()
y_train = lb.fit_transform(y_train)
y_test = lb.transform(y_test)
print("Number of speakers: ",lb.classes_)

output_size = (y_train[0])
input_size = X_train.shape[1]
# Neural network
model = Sequential([
  Dense(256, input_shape=(input_size,)),
  Activation('relu'),
  Dense(128),
  Activation('relu'),
  Dense(64),
  Activation('relu'),
  Dense(1),
  Activation('sigmoid')
])
model.compile(optimizer=optimizers.RMSprop(lr=1e-4),
            loss='binary_crossentropy',
            metrics=['accuracy'])
model.fit(X_train,y_train,epochs=75, batch_size=16, validation_data = (X_test, y_test))
score = model.evaluate(X_test,y_test,batch_size=16)
```

> These are notes used to aid the building of a DNN

### Research Notes

#### Deep Neural Networks broken down
- Assumes correlation and maps input to output through approximation
- Simply a neural network with more than 3 hidden layers
- Like traditional neural networks, it is classified into 2 types: Clustering (unsupervised) or Classification (supervised)
> Assuming the audiolabel project already has a labeling system, we will only look at classification algorithms
- Each layer of nodes trains on a distinct set of features (increased complexity, increased layers)
- Automatic feature extraction (can learn from huge quantities of unlabeled data after training)
- Converts logit layer (last neuron layer) to an output layer (thru logistic/ softmax classifier)
> We will probably use a softmax function due to multiclass classification (more than 1 speaker)
- In the most basic sense, deep learning is just stacks of linear regressions with increasing complexity. In the end of each, there is an activation function (usually ReLU ) which determines the weight of the neuron. The output is judged by a loss function (i.e. gradient descent). If the error is not minimized, the neural network begins to re-learn with the goal of minimizing the cost function. (backpropagtion)
> RELU - Rectified linear unit - a non-linear function - if input is negative, it will be converted to 0 and the neuron is not activated. Error is backpropagated and corrects the weight of the neuron

#### What's different in DL
- Instead of time consuming error propagation mechanisms (bp), DL uses layer greedy-wise training (learns from previous layer) to reduce training time
- Each layer is trained w/ stochastic gradient descent and fine-tuned with BP

#### Types of DL
- *CNN*: Extracts a feature per layer and learns from the previous using greedy-layer-wise training mechanisms to only extract useful layers
- *Deep Belief Network*: Interconnected hidden layers where each layer acts as input only visible to next layer. Uses activation function due to probabilistic logic nodes. Each layer is simply an isolated neural network.


#### Feedforward vs. Feedback Networks
- A feedforward network is a network that contains inputs, outputs, and hidden layers. The signals can only travel in one direction (forward). Input data passes into a layer where calculations are performed. Each processing element computes based upon the weighted sum of its inputs. The new values become the new input values that feed the next layer (feed-forward). This continues through all the layers and determines the output.
- A feedback network (for example, a recurrent neural network) has feedback paths. This means that they can have signals traveling in both directions using loops. All possible connections between neurons are allowed. Since loops are present in this type of network, it becomes a non-linear dynamic system which changes continuously until it reaches a state of equilibrium.

#### Siamese Neural Network
> Traditional requires huge amounts of data for learning. SNN allows one-shot learning (learning with 1 training data)
In basic terms, it is a network with twin copies of CNNs and calculates the absolute difference between the layers for the ouput.


### Good resources
G. Heigold, I. Moreno, S. Bengio, and N. Shazeer, “End-to- end text-dependent speaker verification,” in Acoustics, Speech and Signal Processing (ICASSP), 2016 IEEE International Conference on, pp. 5115–5119, IEEE, 2016.

C. Zhang and K. Koishida, “End-to-end text-independent speaker verification with triplet loss on short utterances,” in Proc. of Interspeech, 2017.


### Notes

## Librosa notes

- *y* is the time series, a 1-d np array, *y[t]* gives the amplitude of the wave at sample *t*
- *sr* is the number of samples per second of a time series
- *n_ftt* is frame length, the number of samples in a frame (length of fft window)
- *hop_length* is number of samples between each frame -> overlapping
- *spectrum* signal's mass / amplitude distributed across frequencies 
- *spectral_centroid* is center of mass (average loudness/ amplitude)
- *mfcc* how human's perceive frequencies, capped by mel filter bank
- *zero-crossing-rate* is how many times (rate) the audio goes to zero


