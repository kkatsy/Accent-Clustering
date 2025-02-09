# Assessing English accent similarity using various audio-to-embedding schemes and dimensionality reduction methods

### CSE256 SP23
### Authors: Katya Katsy, Mason Kellogg, Morgan Lafferty, Sean Lindstrom

*Project report*: [Assessing English accent similarity using various audio-to-embedding
schemes and dimensionality reduction methods](https://github.com/kkatsy/Accent-Clustering/blob/main/finalreport_2023.pdf)

Welcome to our project page! If you would like to run our embedding/clustering code yourself please follow the instructions below.

### Results

![](https://github.com/kkatsy/Accent-Clustering/blob/main/baselines_all.png)

From left to right: 2a. Baseline features colored by native language of speaker emphasizing similarity in mean and standard deviation of MFCC embedding between classes. 2b. Baseline features colored by native language of speaker emphasizing variation in length of MFCC embeddings between classes. 2c. Baseline features colored by native language of speaker emphasizing linear relationship between length of MFCC embedding and 'silence' time (number of entries in MFCC embedding less than 5 in absolute magnitude) in recording.

![](https://github.com/kkatsy/Accent-Clustering/blob/main/clusters.png)

3D cluster plots for each pair of embedding scheme and dimensionality reduction technique. Each audio clip is represented as a point that is colored according the native language of the speaker.

### Dataset

We used the "Speech Accent Archive" dataset collected by researchers at George Mason University and made available for download on Kaggle. This dataset contains 2,140 .mp3 audio samples, each from a distinct speaker reading the same passage in English.

The speakers in the dataset are from diverse backgrounds, representing 214 different native languages across 177 countries. There is an about even split between males (1123) and females (1049) with a median speaker age of 28 (IQR 22-41).

For our project, we reduced this dataset to include all speakers from the 6 most represented native languages: English, Spanish, Arabic, Mandarin, French, and Korean. We do so because (1) clustering results would be very difficult to interpret had we included all 214 different native language classes and (2) some languages have only a few samples in our dataset which could result in erroneous and noisy clusters. After excluding all other samples, we have 579 English, 162 Spanish, 102 Arabic, 65 Mandarin, 63 French, and 62 Korean speakers.

### Embedding Schemes

**Phonemes**: Phonemes were derived from the audio files using Allosaurus - a pretrained universal phone recognizer that converts waveforms into strings of IPA phonemes. The Allosaurus python library is based on the work of (Xinjian Li, 2020).

The resulting string of IPA phonemes of an audio file is then encoded using a bag-of-words approach: we first obtain a list of all the IPA phonemes that appear within our audio files, and then we proceed to count the number of times each phoneme appears within a recording and use these counts as a feature vector.

We counted 57 unique IPA phonemes, so we created feature vector of length 58 - the unique phonemes plus a symbol representing periods of silence in the recordings, which is generated automatically by Allosaurus.

**MFCCs**: Mel Frequency Cepstral Coefficients (MFCCs) is a widely-used form of data compression in automatic speech recognition that allows audio to be converted to feature embeddings. The mel scale is a logarithmic transformation of a signal's frequency that converts hertz into the *mel* unit. *Mels* are defined in such a way that sounds of equal distance on the Mel Scale are perceived to be of equal distance to humans. The mel-frequency spectrum is a representation of the short-term power spectrum of a sound based on the mel scale. MFCCs are the values that represent the mel-frequency spectrum: each MFCC value represents how similar the mel-frequency spectrum is to cosine shapes created from different frequencies.

**Wav2Vec2**: Wav2Vec 2.0 is a transformer-based neural network model created by Facebook that automatically learns discrete speech units and projects recorded speech into a learned embedding space (Alexei Baevski, 2020). We used the pre-trained Wav2Vec2.0 from PyTorch, specifically, the base model pre-trained on 960 hours of unlabeled audio from LibriSpeech dataset.

The raw waveforms are first passed through a feature encoder and are converted into a dimensionally-reduced sequence of feature vectors, which then go through a feature projection and positional encoding convolution. Finally, they are fed into the context network, which is a transformer encoder, which in our case consisted of 12 transformers.

The result is 12 matrices of feature outputs per one waveform audio, corresponding to the output of each transformer. In order to get a single feature array, we average these matrices, and then flatten the resulting feature array into a feature vector. For our project, we generate 40-dimension MFCC embeddings for each audio clip.

### Clustering methods

**PCA**: Principal component analysis, or PCA, is a popular dimensionality reduction technique that linearly transforms data into a new coordinate system in which the variation in the data can be described with fewer dimensions. We believed that PCA would be a good method to identify accents that are strongly dissimilar even though it might not be able to capture the more subtle differences between accents due to its linearity.

**t-SNE**: The t-distributed stochastic neighbor embedding (t-SNE) is a nonlinear dimensionality reduction technique for embedding high dimensional data in a lower dimensional latent space. The main advantage of t-SNE is its ability to preserve local structure, where points that are close to one another in high-dimensional space tend to remain close in the low-dimensional space.

**UMAP**: Uniform Manifold Approximation and Projection (UMAP) works similarly to t-SNE in that it uses a graph layout to approximate data into a lower dimensional space. However, UMAP tends to be more efficient than t-SNE and also can be more effective by doing a better job at maintaining some of the global structure of the higher dimension space.

### Instructions

1. Click the "Preprocessed Recordings" link below, which will open our .zip file in your "Shared with Me" folder in your Google Drive.
2. Click on the three dots on your file's tab to open file options. From there, select "Add a shortcut" option for the folder, and select "My Drive". You should now be able to go to "My Drive" and see a shortcut to our data file there. This way, you will be able to access our data files via your Google Colab without having to redownload it.
3. Repeat steps 2 and 3 for the "Wav2Vec2.0 Features".
4. After opening the notebook of your choice in Google Colab and mounting your drive, you will now be able to run our code.

###  Data
Original Recordings: https://drive.google.com/file/d/1rR4ly_ocE5-oks-PA5JYpc7Uo5MrbeEn/view?usp=share_link

Preprocessed Recordings (converted to .wav): https://drive.google.com/file/d/1XbOw6jx65goNEfGThr0z84-4ERHjnsV5/view?usp=share_link

Wav2Vec2.0 Features (pickled embeddings): https://drive.google.com/drive/folders/1CaX36rGgFCFYMeAqmB3QewNgzidPoBS6?usp=sharing
