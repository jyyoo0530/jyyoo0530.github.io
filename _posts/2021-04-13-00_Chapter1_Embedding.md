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

###### Rule base -> end-to-end -> pre-train/fine-tuning base
Until 1990, people selected feature by themselves from most of the NLP processing model. Therefore, it was required to have Linguistics knowledge to perform NLP processing.<br>
From mid 2000, the deep-learning model has been involved, as the model doesn't required to input the rule in every cases. It was enough to get model by giving "input" and "output" to the deep-learning model.(End-to-End Model).<br>
Then, in 2018, after ELMo proposed, NLP model is developing to the `pretrain` and `fine tuning` steps.

###### Types of Embedding and performances
1) Factorization (GloVe, Swivel)
The method to split original corpus matrix into two or more smaller matrix.<br>
   
2) Prediction (Word2Vec, FastText, BERT, ELMo, GPT)
The Neural Network method. It denote the method which predicts the specific word around the certain word, or predict the word from the given preceeding words, or guess the word from the random empty word space from the sentence.<br>
   
3) Topic-based Approach (LDA(Latent Dirichlet Allocation))
The method that uses `inference` and `latent topic` from the given document.<br>
   
4) Assessment of embedding performance
In case of English Embedding, 형태소 분석, 문장 성분 분석, 의존관계분석, 의미역분석, 상호참조해결등등.However, in case of Korean, it is hard to measure the performance due to lack of open-data.<br>
   
