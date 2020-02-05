# Capstone
Deep Learning Techniques for Music Generation

This is my final project for the Flatiron Data Science immersive. I decided to tackle music generation and dig deep in my exploration of the possibilities. There is a panoply of model types and techniques that can be used to analyse and generate music. This project is based around deep neural networks, however there is scope to generate music using a Hidden Markov Model (HMM) which models probability distributions of notes at each time step and predicts the most likely. However, this model architecture is more simplistic and cannot model polyphony. One certainty is that music generation is the goal rather than a classification problem. Therefore, for the purposes of building a model, generative rather than discriminative models will be investigated. In terms of generation, this project explores the possiblity of creating a study music or ambient instrumentals. This could be done by training on Brian Eno or any dataset of calming or minimalist music. The simplicity of minimalist music will ease training and generation as complexity is reduced. Perhaps a problem for a later date would be to build a discriminative music genre classifier; this could be quite difficult and would require training the model on a massive corpus of music of all genres.

## Introduction

During my exploration of the field I found many papers outlining different techniques for music generation. One particularly useful document was "Deep Learning Techniques for Music Generation" (Briot et al., 2019), which outlines the various data structures for music encoding, then delves into various neural network architectures and examples of specific models. 

There have been myriad attempts at generating music, with varied success (in my opinion). Something pertinent to music is the subjectivity of the material. What does one consider 'good' music? This subjectivity adds complexity when evaluating a seemingly arbitrary sample. Music is very deliberate, therefore, I imagine a computer will struggle to create human-sounding pieces. Music tells a story and conveys emotion meaning that a computer inherently cannot understand those dimensions. The symbiosis of human and computer could allow for realistic and emotive expressions. It will be interesting to see where computer generated music will go when pre-mapped by humans.

Those who may not yet understand deep learning; it is a machine learning technique based on artificial neural networks. A deep (excuse the pun) explanation is out of the scope of this document, however as a high level abstraction, one can imagine a neural network or "brain" that is fed large volumes of data and learns the intrinsic patterns of that data. Artificial Neural Networks (ANNs) were inspired by information processing and distributed communication nodes in biological systems. What makes those ANNs deep (DNN) is the additional layers between the input and output layers. Some tasks which deep learning can be used for are: facial recognition, text recognition, sentiment analysis and many others. Within deep learning there are myriad neural network architectures which can be used for various problems.

For the purposes of generation, the DNN architecture will depend on the audio format. For image data, the Convolutional Neural Network (CNN/ConvNet) architecture will be most useful as it is able to recognize features in images very well. Another option would be to use a Recurrent Neural Network (RNN) which is basically a model that self-recurs and thereby is able to model sequential data very effectively. Autoencoders present another possible architecture. An autoencoder works by compressing input data into a smaller bottleneck hidden layer with fewer nodes than the input. Finally, one simple but possibly useful model is the Restricted Boltzmann Machine (RBM) which is a variation on a neural network that has only 2 layers, an input/output and a hidden layer. There are even models that span architectures or use features of multiple architectures, for example an RNN-RBM a recurrent NN combined with an RBM.

Further, the question of data format for the project is a salient one. The project goals and scope will determine formatting. As mentioned above, the options are varied and require very different architectures. Firstly, one must consider the difference between symbolic and audio. Symbolic being the note and beat representations and audio being a direct representation of the raw audio wave. Within the symbolic sphere, some options are MIDI (Music Instrument Digital Interface - data produced by MIDI/electronic instruments, piano roll (an unrolled representation of melody based on punch-holed roll for automatic pianos), text (musical notes from A to G) among many others. In the other sphere of audio, two main types of raw audio representation can be used: raw audio waveform (.wav file) and spectrogram (a transformation of the audio wave into frequency, amplitude and time. 

Each of these data types present their own challenges and benefits. Raw audio is the ideal format if memory and size were not an issue. The raw waveform encodes full resolution audio and is not restricted by symbolic representations which can lose features like texture. However, this comes with one major drawback, which is that raw signal is very dense data therefore memory and processing requirements will be very large. Some NNs have been implemented to tackle this problem, such as SampleRNN (Mehri et al., 2016) and WaveNet (Oord et al., 2016). These models are (subjectively) very successful in terms of their output, one can listen to the generated samples at https://deepmind.com/blog/article/wavenet-generative-model-raw-audio and http://deepsound.io/samplernn_first.html. WaveNet is a CNN with causal dilations (or à trous) which is like a CNN with a widened receptive field to allow for temporal analysis. A more recent model, MelNet uses the aforementioned modelling technique but applies it to a different representation of the audio data (spectrograms). This resolves the issue of data size by compressing the temporal axis of the audio.

Alternatively, there is the symbolic representation of data. As mentioned there are many types of symbolic representation. Piano roll and MIDI are very similar in terms of the data they hold, however, one major issue with MIDI raised in "Deep Learning for Music" (Huang et Wu, 2016) is that MIDI is read sequentially and therefore cannot encode multiple voices or polyphony. There is, however, a solution to this by using the Google Magenta NoteSequences data type. This format can read a MIDI or MusicXML file into a .mag format which is read more like a piano roll. MIDI data also contains "velocity" which is the encoding of volume or amplitude. This project will entail the use of MIDI data as this will allow for faster computation times and doesnt suffer the same aberations as some other data types.

## Method

Creating a truly creative and dynamic model for music generation with the data scales of audio requires a selective methodology. In order to allow for temporally feasible training times, a corpus of MIDI data will be transformed into NoteSequences. An exciting new platform for music and art creation is Magenta, created in 2015 by Google Brain in order to "build a community of artists, coders and machine learning researchers". While exploring possibilities for this project, many different architectures and implementations (WaveNet and SampleRNN) were attempted. A prevalent problem with many models is deprecation, wherein many models are written using versions of TensorFlow, PyTorch and Theano which are no longer supported. A more up-to-date model is "Performance RNN: Generating Music with Expressive
Timing and Dynamics." by (Simon et Oore, 2017). This model uses an LSTM (Long Short-Term Memory) based RNN. A common LSTM unit is composed of a cell, an input gate, an output gate and a forget gate. The cell remembers values over arbitrary time intervals and the three gates regulate the flow of information into and out of the cell. LSTM networks are well-suited to classifying, processing and making predictions based on time series data, since there can be lags of unknown duration between important events in a time series.

The programming of the neural network is implemented with TensorFlow. It is possible to use the pre-trained models on GitHub: https://github.com/tensorflow/magenta/tree/master/magenta/models/performance_rnn. However, in order to create music of a particular style, the NN must be trained on a new dataset. Initially, the intention was to train a network on a corpus of calming or minimalist music. This would have been straightforward if using raw audio. However, for the MIDI files, it was essential that piano music was used, due to the complexity of multi-voice music. Therefore a corpus of Ludovico Einaudi MIDI tracks was created. This corpus contained his albums *"Divenire"* and *"I Giorni"*. Firstly, this MIDI corpus was transformed into the NoteSequences format that is compatible with Magenta. This was subsequently transformed into "SequenceExamples" which is another formatting that contains the training and evaluation sets. In this case an `eval_ratio` of 0.1 was used, this split 90% of the data into training and the other 10% into evaluation. 

Training the model on this corpus requires a vast computational resources. PerformanceRNN has multiple configurations which can be used for specific use cases. When velocity is encoded in the MIDI, a `performance_with_dynamics` is used on the data. Additionally, there is the option to train a "conditional" model, which allows for a pitch or note density to be specified when training. This can be used to generate samples in a certain key signature.

## Results

This neural model successfully generated music, however, of varied subjective quality. Some of the music sounded harmonic and followed chord progressions properly. Other clips sounded far less human and lacked any musicality. Obviously the quality of music is entirely subjective however, many listeners agreed that the clips lacked a certain humanity and was unable to tell a story. This is most likely due to the inability of the RNN to model long-term latencies which are required for phrasing and storytelling in music. Most listeners agreed that the generated material was musical and displayed a clear understanding of latent features in Einaudi's music such as chords, scales and arpeggios. When comparing the generated music to Einaudi's music directly, listeners stated that the similarities could be heard, however, this depended on the generated piece. Some examples of generated music are much 'better' than others. Again preference being entirely subjective, some pieces were preferred by all but certain pieces lacked a consensus. This is certainly due to people's intrinsic music tastes which vary as a function of extrinsic features. For example, in most cultures a major chord is heard as positive, however, in Bali people associate it with funerary music. This demonstrates how challenging it can be to determine a metric of success for a piece of music. edit 

## Discussion and Conclusions

This was simply an exploration and experimentation into the possibility of applying a recurrent neural network to music data to further generate new music. In this goal, this project was successful and demonstrated the efficacy of RNNs in music generation. Despite the fact that the music generated lacked a certain subjective humanity, it is clear that the music that was generated was musical and similar to Einaudi's music. The musicality could have been improved by supplying the neural network with more data and would have been improved by training the RNN for longer on a faster GPU. 

An extension of this project will be to train an RNN on a variety of musical genres (classical) and build a web application that generates music with this same genre. Whether that Baroque, Romantic or Minimalist, the RNN will generate study music for students to play and never repeat a song again. 

Musicality 
