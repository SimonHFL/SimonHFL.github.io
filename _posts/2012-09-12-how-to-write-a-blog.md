---
layout: post
title: >-
  The BEA 2019 shared task: Techniques, tweaks, and tricks for Grammatical Error
  Correction
published: true
subtitle: >-
  This post presents an overview of the approaches and techniques used in the
  BEA 2019 shared task on Grammatical Error Correction.
---
The recent BEA shared task on Grammatical Error Correction had a total of 24 participating teams with many interesting approaches to the problem – many of them achieving very impressive results! A wide range of techniques, tweaks and tricks were used and in order to present a more easily comprehensible overview, I have composed a list of my main takeaways.

Looking through the submissions, two overall trends are clear:
1.	Neural machine translation approaches are dominating. Previous state-of-the-art approaches, based on rules, classifiers and statistical machine translation, have now been left in the dust.
2.	It has become standard practice to train systems on massive amounts of artificially generated examples of errors or weakly supervised data (e.g. from the Wikipedia revision history).

When describing the more detailed components I divide them into three areas:
1.	**Model**: The model’s architecture and decoding techniques
2.	**Data**: The (artificial) data used to train the model
3.	**Training**: The training procedure
<br/><br/>
### Model

**Architecture** &nbsp; The vast majority of submissions are based on a neural machine translation approach, with two-thirds using the transformer architecture while the rest are based on a convolutional sequence-to-sequence architectureor a combination of the two. When comparing the two architectures, [Yuan et al.](https://www.aclweb.org/anthology/W19-4424) see a very large performance gain favoring the transformer model.
[Choe et al.](https://www.aclweb.org/anthology/W19-4423) also take advantage of the copy augmented transformer architecture. This was originally suggested for Grammatical Error Correction by [Zhao et al.](https://arxiv.org/pdf/1903.00138.pdf) who showed improvements by incorporating an output mechanism that allows copying an input token. This makes sense, as whenever an error correction system is not correcting an error, it is simply copying the input.

**Re-ranking**     The two top systems in the restricted trackalso re-rank the output sentences from the beam search.

[Choe et al.](https://www.aclweb.org/anthology/W19-4423) notice that many of their model&#39;s corrections are unnatural or incorrect, which they improve by re-ranking using a pre-trained neural language model. In addition, [Grundkiewicz et al.](https://kheafield.com/papers/edinburgh/bea19.pdf) use a right-to-left neural language model as a feature when re-ranking – the motivation being that a right-to-left model can complement the standard left-to-right decoding.

Other approaches use error detection models for re-ranking: [Yuan et al.](https://www.aclweb.org/anthology/W19-4424)re-rank based on features derived from an error detection system while [Kaneko et al.](https://www.aclweb.org/anthology/W19-4422) fine-tune BERT on a sentence level error detection task and use its predictions as a feature for re-ranking. Both these approaches see improved overall score – especially recall, as systems are pushed towards correcting more errors.

**Filtering** [Asano et al.](https://www.aclweb.org/anthology/W19-4418)increase their precision by using a sentence level error detection system to filter sentences without errors before they are passed to the error correction model. This approach is also interesting for industrial use cases, as it could significantly decrease processing time since, in general, the majority of sentences do not contain errors.

**Iterative decoding** With iterative decoding, a sentence is continuously passed through the translation model until the model outputs the sentence unchanged. This allows for correcting the sentence in increments instead of only through one pass, thereby enabling the model to generate more corrections. [Náplava et al.](https://www.aclweb.org/anthology/W19-4419) sees improvements by using iterative decoding, however they increase their recall at the expense of precision. On the contrary, [Grundkiewicz et al.](https://kheafield.com/papers/edinburgh/bea19.pdf)forgo iterative decoding, as they contend it is only necessary if the system has low recall.

## Data

In general, there exist two approaches to generating artificial examples of errors:
1. Rule-based, using error statistics gathered from real data and confusion sets consisting of words that typically are mistakenly confused
2. Back-translation, where a correction model is trained in reverse to insert errors into correct text

**Rule-based generation** The top two contributions in the restricted track both used a rule-based approach.

[Grundkiewicz et al.](https://kheafield.com/papers/edinburgh/bea19.pdf)base their error generation approach on confusion sets extracted from the spell checker Aspell, which bases its suggestions on both lexical and phonological similarity.With a probability matching the word-error rate in the development set, a word is either deleted, swapped with its adjacent word, substituted with a word from its confusion set, or a random word is inserted. Additionally, in order to handle spelling errors, lexical noise is introduced to words, by using the same operations above on the character level.

The runner up, [Choe et al.](https://www.aclweb.org/anthology/W19-4423), attempt to make realistic errors by inserting n-gram patterns from real errors into correct text. They also employ rules for creating specific error types such as preposition, noun number and verb errors. When comparing their error generation approach with one based on random replacements, deletions, insertions and reordering, they see clear benefits. However, the difference is evened out if the model is fine-tuned on real errors.

**Back translation** Since back-translation is not guaranteed to inject errors and could simply paraphrase the sentence, in order to control the data quality [Yuan et al.](https://www.aclweb.org/anthology/W19-4424)filter the artificial sentence-pairs that are likely to be paraphrasing. This way, sentence pairs are filtered if the generated sentence does not have a decreased language model probability higher than a threshold learned from real error examples.

## Training

**Checkpoint averaging** [Náplava et al.](https://www.aclweb.org/anthology/W19-4419)see improved performance and lower variance by having the weights of the final model being an average of a number of previous checkpoints. For their final model, they end up averaging the 8 latest checkpoints.

**Weighted Maximum Likelihood Estimation** Since Grammatical Error Correction systems tend to converge to a local optimum, where the model often simply copies the input unchanged to the output, [Grundkiewicz et al.](https://kheafield.com/papers/edinburgh/bea19.pdf) and [Náplava et al.](https://www.aclweb.org/anthology/W19-4419) modify the MLE loss function to give higher weight to tokens that should be changed.

**Domain adaptation** Several approaches are suggested for dealing with the variance in error types and frequency across different domains. In one approach, [Náplava et al.](https://www.aclweb.org/anthology/W19-4419) generate their training set by oversampling a smaller dataset that shares the domain of the test set.
As an alternative to oversampling, [Choe et al.](https://www.aclweb.org/anthology/W19-4423)use a sequential transfer learning approach. This way, their model is trained in a three-stage process: 1) de-noising auto-encoder 2) training 3) fine-tuning.  At each stage, the model is trained on a progressively smaller dataset that is closer to the test domain.

**Multi-task learning** [Yuan et al.](https://www.aclweb.org/anthology/W19-4424)use the Grammatical Error Detection task as an auxiliary learning objective – both by labelling each input token as correct or incorrect and by binary classification if the sentence is correct or not. Their system performs very well on error detection, suggesting that the auxiliary detection task was beneficial.

## Conclusion

The impressive performance of systems in the BEA 2019 shared task show that the Grammatical Error Correction field has taken a great leap forward in the 5 years since the previous shared task, CONLL14. In particular, a lot of success has been achieved by modelling the problem as a low-resource neural machine translation task. Even in this relatively narrow setting, systems are still taking many different directions and many different techniques are being applied.
Going forward, each of the components I have listed should be explored further, to develop a set of best practices that can serve as the basis for industrial systems and future research directions.








