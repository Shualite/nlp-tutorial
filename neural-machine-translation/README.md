# Neural Machine Translation
Translation with a sequence-to-sequence (seq2seq) network and attention.

## Model Overview
As you already know, a Recurrent Neural Network, or RNN, is a network that operates on a sequence and uses its own output as input for subsequent steps.
A seq2seq network(model), or Encoder-Decoder network, is a model consisting of two RNNs called the encoder and decoder. The encoder reads an input sequence and outputs a single vector, and the decoder reads that vector to produce an output sequence.

<p align="left">
<img width="700" src="https://github.com/lyeoni/nlp-tutorial/blob/master/neural-machine-translation/images/seq2seq.png">
</p>

Unlike sequence prediction with a single RNN , where every input corresponds to an output, the seq2seq model frees us from sequence length and order, which makes it ideal for translation between two languages.

Consider a pair of sentences "Je ne suis pas le chat noir" and "I am not the black cat". Most of the words in the input sentence have a direct translation in the output sentence, but are in slightly different orders. Because of the language construction, there is also one more word in the input sentence. It would be difficult to produce a correct translation directly from the sequence of input words.

With a seq2seq model, the encoder creates a single vector which, in the ideal case, encodes the "meaning" of the input sequence into a single vector - a single point in some N dimensional space of sentences.

### Encoder
The encoder of seq2seq network is a RNN that outputs some value for every word from the input sentence. For every input word, the encoder outputs a `(context) vector` and `hidden state`. The last hidden state of the encoder would be a initial hidden state of decoder.

### Decoder
The decoder is another RNN that takes the encoder output vector(s) and outputs a sequence of words to create the translation.

This last output is sometimes called the context vector as it encodes context from the entire sequence. **This context vector is used as the initial hidden state of the decoder**. At every step of decoding, the decoder is given an input token and hidden state. The initial input token is the start of string <SOS> token, and the first hidden state is the context vector (= the encoder's last hidden state). The decoder continues generating words until it outputs an <EOS> token, representing the end of the sentence.

We are also going to use an `Attention Mechanism` in our decoder to help it to pay attention to certain parts of the input when generating the output.

## Training

### Auto-regressive and Teacher Forcing
The training method of sequence-to-sequence network is different from the way of inference. The difference between training and inference is fundamentally due to the property **auto-regressive**.

#### Auto-regressive
`Auto-regressive`: infers (or predicts) the present value by reffering to its own values in the past. As shown below, the current time-step output value *y_t* is determined by both the encoder's input sentence (or sequence) *X* and the previous time-step output *(y_1,...y_t-1)*.
<p align="center">
<img src="https://latex.codecogs.com/gif.latex?\dpi{100}&space;\fn_cm&space;y_{i}&space;=&space;argmax_{y}P(y|X,&space;y_{<i})\;&space;where\;&space;y_{0}&space;=&space;SOS\;&space;token" title="y_{i} = argmax_{y}P(y|X, y_{<i})\; where\; y_{0} = SOS\; token" />
</p>
If our decoder makes false prediction in the past, it can lead to a larger false prediction. (e.g. incorrect sentence structure or length)

#### Teacher Forcing
So we train using a method called Teacher Forcing, which is the concept of using the real target outputs as each next input, instead of using the decoer's guess as the next input.
- `In training mode`, seq2seq network considers that we already know all answers (= the outputs of the last time-step).
- `In inference mode`, seq2seq network takes the input from the output of the last time-step.
<p align="center">
<img src="https://latex.codecogs.com/gif.latex?\dpi{100}&space;\fn_cm&space;\hat{y}&space;=&space;argmax_{y}P(y|X,&space;y_{<t};\theta)\;&space;where\;&space;X&space;=&space;{x_{1},...,x_{n}}\;&space;and\;&space;Y=&space;{y_{0},...,y_{n}}" title="\hat{y} = argmax_{y}P(y|X, y_{<t};\theta)\; where\; X = {x_{1},...,x_{n}}\; and\; Y= {y_{0},...,y_{n}}" />
</p>
<p align="center">
<img width="600" src="https://github.com/lyeoni/nlp-tutorial/blob/master/neural-machine-translation/images/teacher-forcing.png" />
</p>

## References

- [spro/practical-pytorch](https://github.com/spro/practical-pytorch)
- [spro/practical-pytorch-Translation with a Sequence to Sequence Network and Attention](https://pytorch.org/tutorials/intermediate/seq2seq_translation_tutorial.html#the-seq2seq-model)
 