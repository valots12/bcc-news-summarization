# Comparison of extreme text summarization models on BCC news dataset

***Part of the Text Mining and Search project | UniMiB***

For the following analysis, the dataset used is the X-SUM database, a collection of online articles from the British BBC. Retrieval of the data needed for analysis was done by directly downloading the different articles limited to sport category, effectively creating the database. Once these operations were performed, a final data frame containing about 50k purely sports articles was obtained, with about 60 sports covered. 

The text technique applied on the dataset is text summarization; text summarization is the technique for generating a concise and precise summary of voluminous texts while focusing on the sections that convey useful information, and without losing the overall meaning. There are two main families of methods to solve a text summarization problem: extraction-based summarization and abstraction-based summarization. 

- Extraction-based summarization is a family of techniques that, starting from the dictionary of words of a specific document, try to determine which are the most relevant words and combine them in order to create a summary. 

- Abstraction-based summarization, on the other hand, is based on more advanced deep learning techniques. This family tries to copy a typical human behavior, allowing the model to add and generate words that are considered relevant but are not in the vocabulary of a given document.

<img src="Images/extractive.jpg" width=420>

For the given analysis, the following models have been considered:

#### Baseline

The baseline pipeline is used only as a benchmark. It consists of the selection of the first three sentences of each document. The idea that the most relevant information of a document is inside the first part is the document was one of the most popular in the early stages of text summarization technique, while it is used mainly only for comparison purposes.

#### T5

T5 (Text-To-Text Transfer Transformer) is a transformer model that is trained in an end-to-end manner with text as input and modified text as output, in contrast to BERT-style models that can only output either a class label or a span of the input. This text-to-text formatting makes the T5 model fit for multiple NLP tasks like Question-Answering, Machine Translation and also Summarization tasks like the current one.

#### BART

BART is a denoising autoencoder for pretraining sequence-to-sequence models. It is trained by corrupting text with an arbitrary noising function, and learning a model to reconstruct the original text. It uses a standard Transformer-based neural machine translation architecture.

#### PEGASUS

PEGASUS (Pre-training with Extracted Gap-sentences for Abstractive SUmmarization Sequenceto-sequence) was specifically designed for abstractive summarization and is pre-trained with a self-supervised gap-sentence-generation objective. In this task, entire sentences are masked from the source document, concatenated, and used as the target “summary”.

### Evaluation of the results

At the state of the art, one of the most used alternatives of a human evaluation of text summarization results, clearly not a scalable method, is the Rouge statistic, that is the one used for the current work. ROUGE stands for Recall-Oriented Understudy for Gisting Evaluation. It includes measures to automatically determine the quality of a summary by comparing it to ideal summaries created by humans. The general formula for ROUGE-n is the following one:

<img src="Images/rouge.png" width=420>
where N is the length of the n-gram chosen.

For the given analysis we are considering two different types of ROUGE-N, that are ROUGE1 and ROUGE-2, respectively for uni-grams and bi-grams. A different approach is possible using ROUGE-L. ROUGE-L is based on the longest common subsequence (LCS) between our model output and reference. It means that is considering the longest sequence of words (not necessarily consecutive, but still in order) that is shared between both. A longer shared sequence should indicate more similarity between the two sequences. For the given analysis we are considering two different types of ROUGE-N, that are ROUGE-L and ROUGE-L Sum. The main difference between the two is that, while ROUGE-L computes the longest common subsequence (LCS) between the generated text and the reference text ignoring newlines, ROUGE-L-sum does the same considering newlines as sentence boundaries.

|          | ROUGE-1 | ROUGE-2 | ROUGE-L | ROUGE-L SUM |
| :---:    | :-----: | :-----: | :-----: | :---------: |
| Baseline | 0.168   | 0.020   | 0.107   | 0.107       |
| T5       | 0.171   | 0.023   | 0.117   | 0.117       |
| BART     | 0.203   | 0.041   | 0.135   | 0.166       |
| PEGASUS  | 0.472   | 0.269   | 0.412   | 0.414       |

In the following table, we report the results obtained on a specific document from the test set.

<img src="Images/rouge_sum.png" width=640>

From both a human evaluation and an analytical one, it is clear the below summary obtained from the Pegasus model is the best one, still keeping a considerable short length of the summary.

### Fine tuning of the best model

In order to improve the results obtained on the best model, we have implemented a fine tuning strategy on the Pegasus model. Fine tuning allows us to adjust the weights estimated on a huge dataset in order to become more similar to a specific task. In this case, the goal of the strategy is to adapt the Pegasus model to sport documents, but still starting from the original weights. To avoid GPU limitations, we randomly selected 10000 documents from the original training dataset and 1000 from the validation one with their respective summaries. The training phase uses 8 elements for each training batch and 4 for each validation one. This step required 8 epochs and around 9 hours of training. After this step the improvement on the validation set was very limited so we decided to not continue more.

|                     | ROUGE-1 | ROUGE-2 | ROUGE-L | ROUGE-L SUM |
| :---:               | :-----: | :-----: | :-----: | :---------: |
| PEGASUS             | 0.472   | 0.269   | 0.412   | 0.414       |
| PEGASUS FINE-TUNED  | 0.497   | 0.275   | 0.418   | 0.418       |

The table above shows that the fine tuning model proposed improves the performance compared to the original one. It means that the new version is more capable to summarize sport documents, that is the main goal of the analysis. In particular, the best improvement is observed on the ROUGE-1 metric, so we can interpret the results as an improvement in recognizing relevant unigrams in a starting document.
