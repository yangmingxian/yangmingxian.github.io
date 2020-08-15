---
layout: post
title: "ANLP复习提纲"
subtitle: "Accelerated Natural Language Processing"
author: "Mingxian Yang"
header-img: "img/post-bg-infinity.jpg"
header-mask: 0.3
mathjax: true
tags:
  - 笔记
  - NLP
---

## 1. Generative probablistic models
- N-Gram Language Model  
- Hidden Markov Model  
- Probabilistic Context-Free Grammar  
- Naive Bayes classifier 

describe the generative process and write down the associated formula for the joint probability of latent and observed variables.

compute the probability of (say) a tag-word sequence, parse tree, or whatever the model describes (assuming you know the model parameters).  

for the models with latent variables, compute the most probable [tag sequence/parse tree/class] for a particular input, hand-simulating any algorithms that might be needed (again assuming you know the model parameters).  

explain how the model is trained.  
give examples of tasks the model could be applied to, and how it would be applied.  

say what the model can and cannot capture about natural language, ideally giving examples of its failure modes.

## 2. Discriminative probablistic models
- Logistic Regression/MaxEnt model  

understand the formula for computing the conditional probability of the hidden class given the obervations/features, and be able to apply that formula if you are given an example problem with features and weights. We are not likely to ask you to write down the full formula yourself, but you must know it well enough to be able to answer questions like "which class is the most probable" (without us giving you the formula).    

give examples of tasks the model could be applied to, and how it would apply (e.g., what features might be useful).  

explain at a high level what training the model aims to achieve, and how it differs from training a generative model.
explain the role of regularization, and identify situations in which it is most important.  

discuss the pros and cons of discriminative vs generative models.
  
## 3. Other furmulas
In addition to the equations for the models listed above, you should know the formulas for the following concepts, what they may be used for, and be able to apply them appropriately. Where relevant you should be able to discuss strengths and weaknesses of the associated method, and alternatives.
- Bayes' Rule (also: defn of Condition Probability, law of Total Probability aka Sum Rule, and all other relevant formulas in the Basic Probability Theory reading)
- Noisy channel model  
- Maximum Likelihood Estimation
- Add-One / Add-Alpha Smoothing  
- Interpolation (for smoothing)  
- Dot product, cosine similarity   
- Pointwise mutual information  
- Precision and recall  

## Algorithms and computational methods
For each of the following algorithms, you should be able to explain what each of these computes (its input and output), what it is used for, and be able to hand simulate each one. Some of these algorithms are naive, or solve problems that more naive algorithms face. You should be able to explain what those problems are and how the better algorithms solve them
- Minimum string edit distance algorithm  
- Viterbi Algorithm for Hidden Markov Models     
- Forward Algorithm for Hidden Markov Models (to compute likelihood)  
- Recursive descent parsing  
- CKY parsing  
- arc-standard transition-based dependency parsing  
  
For each of the following methods, we haven't discussed algorithms at the level of data structures or implemention, but you should still be able to explain what each method computes (its input and output), what it is used for, and be able to hand simulate each one.  
  
- Finite State Automata and Transducers  
- Parsing with semantic attachments

For each of the following methods, you should be able to explain what it computes (its input and output), what it is used for, and be able to describe how it works in some detail.  

- Expectation-Maximization (forward-backward algorithm) for HMMs

## ADDITIONAL MATHEMATICAL AND COMPUTATIONAL CONCEPTS
Overarching concepts:

- Dynamic programming: 

What characterizes the tasks this is applied to, and the way that DP solves them? What are examples of DP algorithms? In parsing, be able to contrast DP with other search strategies (e.g. breadth-first or depth-first search).  
- Zipf's Law and sparse data: 
  
What is Zipf's law and what are its implications? What does "sparse data" refer to? Be able to discuss these with respect to specific tasks.  
$f × r ≈ k$  
• f = frequency of a word  
• r = rank of a word (if sorted by frequency)  
• k = a constant  
Zipf’s law means there will always be rare instances  

- Probability estimation and smoothing:

What are different methods for estimating probabilities from corpus data, and what are the pros and cons of each, and the characteristic errors? Under what circumstances might you find simpler methods acceptable, or unacceptable? You should be familiar at a high level at least with: 
   
   - Maximum Likelihood Estimation  
   - Add-One / Add-Alpha Smoothing  
   - Interpolation  
   - Backoff  
   - Kneser-Ney Smoothing  
  
Except as noted under "Formulas" above, you do not need to memorize the formulas, but should understand the conceptual differences and motivation behind each method, and should be able to *use* the formulas if they are given to you.   

- Prior and likelihood: What do these refer to (in general, and in specific models)? What is their role in a probabilistic model?  
- Training, development, and test sets: How are these used and for what reason? Be able to explain their application to particular problems.  

In addition, for the following concepts you should be able to explain each one, give one or two examples where appropriate, and be able to identify examples if given to you. You should be able to say what NLP tasks these are relevant to and why.

- Regular Language: *Languages produced by FSMs are called regular languages* and Regular Expression: *Reg. languages can also be described with regular expressions*
- Noisy channel model  
- Search space in parsing: What is one searching for and what is one searching through?  
- Breadth-first search, depth-first search, and differences between them  
- Top-Down parsing vs. Bottom-Up parsing  
- Best-first probabilistic parsing  
- Inside cost  
- Well-formed Substring Tables  
- Difference between recognition and parsing  
- Pointwise mutual information  
- Context vector  
- Vector representation of words/word embedding  
- Dimensionality reduction  
- Vector-based similarity measures  

## LINGUISTIC AND REPRESENTATIONAL CONCEPTS
You should be able to explain each of these concepts, give one or two examples where appropriate, and be able to identify examples if given to you. You should be able to say what NLP tasks these are relevant to and why.

- Ambiguity (of many varieties, wrt all tasks we've discussed)
- Stem: *often: the part of the word that
is common to all its variants*, Affixes, Root, Lemma: *the canonical form or dictionary form of a set of words*
- Inflectional Morphology: count(+s) possessive(+'s) tense(+ed,+ing) 3rd(+s) comparative(+er) & superlative(+est)  
  Derivational Morphology: may change the part of speech or meaning of a word  drama+(t)ize govern+ment+s, centr+al+ize+d (whereas inflection occurs at word
edges)  
- Lexical compound: *Creating new words by merging multiple words*
- Part-of-Speech: *Identify parts of speech (syntactic categories)*
- Open-class Words(nouns, verbs, adjectives, adverbs), 
  mostly content-bearing, they refer to objects, actions, and
features in the world  
  there is no limit to what these words are, new
ones are added all the time   
- Closed-class Words: pronouns, determiners, prepositions, connectives,
    there is a limited number of these  
    mostly functional: to tie the concepts of a sentence together
- Context-Free Grammar (p11)
- Terminal and non-terminal (phrasal) categories
- Bounded and unbounded dependencies
- Dependency syntax
- Head Words (in Syntax)
- Word Senses and relations between them (synonym, hypernym, hyponym, similarity)
- Distributional hypothesis
- Collocations
- Meaning representations (MR)
- First Order Logic
- Canonical Form
- Verifiability
- Compositionality
- Expressivity
- Quantifiers and quantifier scoping
- Lambda expressions
- Reification of events

## TASKS
You should be able to explain each of these tasks, give one or two examples where appropriate, and discuss cases of ambiguity or what makes the task difficult. In most cases you should be able to say what algorithm(s) or general method(s) can be used to solve the task, and what evaluation method(s) are typically used.

- Tokenization
- Spelling correction
- Morphological analysis
- Language modelling
- PoS-tagging
- Text categorization
- Syntactic parsing
- Word sense disambiguation
- Question answering
- Sentiment analysis
- Semantic analysis (semantic parsing)

## RESOURCES
You should be able to describe what linguistic information is captured in each of these resources, and how it might be used in an NLP system.

- Penn Treebank
- WordNet

You should also be able to identify legal and ethical issues in the creation and collection of linguistic resources.

## EVALUATION CONCEPTS AND METHODS
For each of the following, you should be able to explain what each of the specific methods measures, what tasks it would be appropriate for, and why.

Perplexity
Accuracy
Precision, recall, and F-measure (for parsing and for other tasks)
Correlation


In addition:

- Instrinsic vs. extrinsic evaluation: be able to explain the difference and give examples of each for particular tasks.
- Human evaluation: be able to give examples of tasks where it might be used and in what way, and be able to discuss any ethical issues that might arise.
- Statistical significance: be able to explain what it is and why it is relevant to evaluation.
- Corpora: Issues involved in collection, annotation and distribution
- Using social machines/crowd-sourcing for annotation/evaluation: benefits and weaknesses


## ADDITIONAL ETHICAL ISSUES
In addition to the topics listed under Resources and Evaluation, you should be able to identify and briefly discuss

- other potential ethical issues arising from developing or deploying NLP tools (e.g., fairness/bias, licensing and privacy concerns), and say how they might be relevant when presented with an example task;
- social responsibililty and how it may arise in work as a scientist
- issues addressed under the names Open Data and Open Science