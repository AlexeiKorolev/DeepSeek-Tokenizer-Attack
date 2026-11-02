# Training Data Mixture Inference Using BPE Tokenizers

This repository contains our final project for COS 484 (Natural Language Processing at Princeton University), where we audit the pretraining data of large language models using only their tokenizers. We build upon the method introduced by Hayase et al. (2024) to infer training data proportions by analyzing the structure of BPE tokenizers. We then propose a defence against the inference attack by creating a controlled randomization of the tokenizer.

**Authors**:
Alexei Korolev – ak9088@princeton.edu
Merritt Zhang – mz5872@princeton.edu
Brian Zhou – bz6603@princeton.edu

## Abstract

Modern language model developers rarely disclose details about their training corpora. However, the structure of their tokenizers—especially byte pair encoding (BPE)—can leak clues about the underlying data mixture.

We reproduce and extend the method by Hayase et al. (2024) that casts BPE merge order into a linear program (LP). This LP estimates the proportion of categories (e.g., English text, programming languages, multilingual data) in the training set of a model.

We apply this attack to DeepSeek-V3 and DeepSeek-R1 and compare them to models in the GPT family. We also propose and evaluate a novel defense mechanism against the attack by introducing random noise into the BPE merge process.

## Attack

We applied our attack to the merge lists of:
* DeepSeek-V3: 38% code, 22% non-English natural language
* DeepSeek-R1: 56% code, only 7% non-English

> **Caveat (under re-verification):** DeepSeek-R1 is built on the DeepSeek-V3 base model and should share its tokenizer, so the attack would be expected to give the same estimate for both. The divergence above may come from differences in the tokenizer files we used or from a pipeline issue, and we are re-checking it before treating the V3/R1 comparison as a finding.

Compared against:
* GPT-3.5-turbo (63% code)
* GPT-4o (high multilinguality)

## Defence

We trained a new tokenizer using the K-Random BPE strategy (e.g., k=10), which drastically reduced the accuracy of the LP-based inference:
* Masked underrepresented langauges within the training corpus.
* Only caused a slight increase in average token length per word (~+0.02 tokens), which is a measure of the tokenizers quality.

Original attack: https://github.com/alisawuffles/tokenizer-attack
