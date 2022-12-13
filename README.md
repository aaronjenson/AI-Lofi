# AI-Lofi
## Abstract
In this project I use an LSTM neural network to generate Lo-Fi songs based off a midi file data set.

## Problem
When I study or do homework I typically listen to music. One of my go-to genres of music is Lo-Fi because it's usually very repetitive and makes for good focus music. Because of this repetitive nature it lends itself well to a machine learning task. Neural networks are typically pretty good at detecting patterns and replicating them so this task should be doable.

## Resources
https://www.kaggle.com/datasets/zakarii/lofi-hip-hop-midi
The first thing I needed to do this project was a data set, which I found on kaggle. The author of the data set also had a Jupyter notebook that they had used to demonstrate their work on the same problem. I read their article which they posted and used it as a proof of concept that this idea could work using a recurrent neural network. I didn't reference their code very often during the process of this project.

https://github.com/pjreddie/dubnet/blob/main/hw2.ipynb
Another resource I used was our implementation of a recurrent neural network for our homework assignment of text generation. My first attempt at this project was to modify this code to accept a different input and generate MIDI files as a result. Though I eventually changed to using Tensorflow instead of Pytorch, the code from class and my implementation from homework were helpful in determining the network architecture to use and training parameters.

https://www.tensorflow.org/tutorials/audio/music_generation
The last resource that I used was a tutorial on the Tensorflow website. One of their tutorials involved a very similar task. Throughout the process of writing the code for this project I referenced this tutorial to learn what tensorflow had available in its API since I had never worked with tensorflow before.

## Methodology
### Data
I obtained my data from a kaggle data set which consisted of several MIDI files in a folder. To use this data I needed to process it into notes which could then be used to feed a recurrent neural network. I used the pretty_midi library for this which made it really easy to process the MIDI files. One of the difficulties with MIDI is that it stores the songs as a collection of messages. Each message contains information about what type of message it is, the pitch, and the timing. The only types of messages that we care about for this project are note on and note off messages. However these messages use a relative time step parameter, meaning the time attribute of the message is actually just a time difference from the last message. A note on message doesn't contain the length of the note, a midi player just knows to turn off the note when it receives the note off message of that same pitch.

Generating midi messages in this way would be difficult for a machine learning model because it might forget about the note that it needs to turn off. To make this simpler, we processed the notes in a different format, where we instead stored the pitch, start time, and duration of the note. We kept the concept from MIDI files of using a relative time step so that the network would have a small range of time values that it should predict rather than having a possible explosion of very large numbers. The pretty_midi library processed the MIDI files into this data format easily for us, with only a little bit of data transformations needed to get the necessary information.

### Model
The model architecture was a two layer LSTM with three separate output layers, one for each output value. Using separate output layers allowed us to use different loss functions for each output, which is necessary because we have both categorical outputs, the pitch, and numerical outputs, time step and duration. I tested several different network architectures, and this was the best performing one that I found.

### Training
I mostly just used the built-in Tensorflow model.train function, which did almost everything I needed and is one of the main reasons that I decided to use tensorflow for this project. One of the unique things about the training process for this project was that I only used a training data set. I chose not to split the data for testing because in my experience, the testing data set didn't give me any useful information and didn't help the model to improve. In my first tests, I used a test data set but found that the model always overfit to the training data set and would produce very low accuracy on the test data set. I tried several different networks to fix this problem, but found that it persisted no matter what network I used. Music is not even close to deterministic in nature, meaning that there are many different notes that could be the correct next note in a sequence. This makes it very hard to have a test data set, since it's very unlikely that the model will predict the same next note that is in the test sequence. Because this problem meant that the test data set wouldn't give me any useful information, I chose to use all of the data for training to help the model improve a little bit more.

## Evaluation
To evaluate my results I used some functions to produce a midi file, and then a wav file that could be played in the browser to listen to the song the model wrote. Since a test data set wasn't used for this, I instead evaluated the results by listening to the songs and tuning the model to produce the best results to my ear. Generating the songs was done using standard temperature based sampling methods. Each generated note was then added to the list of notes, and the last 30 notes were fed into the model to generate the next one.

## Results
The model was able to write songs, but unfortunately the songs it wrote are not likely to be on anyone's top playlists. Typically, it would start each song off with a jumble of notes that didn't really sound like music at all. Then, it would sometimes write a simple melody in the middle of the song that could potentially be the start of a nice Lo-Fi beat. However, this was typically very short-lived, as it would quickly return to its cacophony of notes.

One of the things this model was particularly bad at was determining the timing and length of notes. This is likely because of the data set that I used, which consisted of mostly just The Melody of Lo-Fi songs. The data set didn't include any baselines, and had very few chords. Because of this, the model wasn't used to having very many notes played at the same time, and therefore when it generated notes, usually would generate them as a run of notes played either very quickly, or slowly and evenly spaced out. When it did generate notes that were played together, it would usually not produce notes that would typically be played at the same time.

## Demo
Examples of outputs and the code for this project can be found at this link: https://colab.research.google.com/drive/1A9_8PbFjlT0zrIJD6oSjbYXlE8iL6RQu?usp=sharing
Several generated songs are at the bottom of the notebook, and you can also run the code yourself by downloading the dataset and placing it in the correct folder in your Google Drive. Instructions for running yourself are available in teh notebook.

## Video
https://youtu.be/ExF9zncgE1A
