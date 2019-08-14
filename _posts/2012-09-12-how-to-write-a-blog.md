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
1.	*Model*: The model’s architecture and decoding techniques
2.	*Data*: The (artificial) data used to train the model
3.	*Training*: The training procedure

# Model

###Architecture
The vast majority of submissions are based on a neural machine translation approach, with two-thirds using the transformer architecture while the rest are based on a convolutional sequence-to-sequence architecture or a combination of the two. When comparing the two architectures, 

**Hello world**, this is my first Jekyll blog post.

testestsetsetsetset
