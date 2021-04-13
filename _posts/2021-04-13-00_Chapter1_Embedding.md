## Embedding

###### Embedding
> Process of involved step(or itself) to change NLP used by human to the vector used by machine

Embedding have 3 major roles
1. similarity between word/sentence
2. linguistic information implication
3. transfer learning

a) Similarity between word/sentence
- cosine similarity
- t-SNE dimension reduction

b) linguistic information implication
- word analogy test<br>
  a word - b word = c word<br>
  e.g.) korean - asian = italian

c) transfer learning<br>
embedding often used as the input of other deep-learning model.<br>
<br>

###### History of embedding technique
statistical method -> neural network method<br>
> Latent Semantic Analysis, Singular Value Decomposition<br>

latent semantic analysis results in sparse matrix, because usually word/sentence data counts around 100,000~200,000.<br>
Therefore, to save memory leakage, they started to compress the dimension of the matrix.<br>

>TF-IDF matrix(Term Frequency-Inverse Document), Word-Context Matrix, Pointwise Mutual Information Matrix

Neural Network method is recently highlighted method since the publishing of "Neural Probabilistic Language Model(Bengio et al., 2003)". Neural Network method is performed with, for example, predicting the word after the given words or masking the word in the punched word-place in the given sentence.

###### From the word level to the sentence level embedding
Before 2017, embedding method was usually word level model, as
`NPLM, Word2Vec, GloVe, FastText, Swivel` shows.
The cons of the word-level embedding is that it cannot distinguish `homonym`.<br>
After 2018, ELMo(Embeddings from Language Models) is announced, sentence level embedding is highlighted.
`ELMo(Embeddings from Language Models), BERT(Bidirectional Encoder Representations from Transformer), GPT(Generative Pre-Training)`are the cases. 
