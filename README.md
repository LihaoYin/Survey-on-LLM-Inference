# Survey-on-LLM-Inference

## Contents

- [0. Introduction](#0-Introduction) <br />

- [1. Paper Reading Progress](#1-Paper-Reading-Progress) <br />

- [2. All Papers (Classifications according to Related Topics)](#2-all-papers-classifications-according-to-related-topics) <br />

- [3. All Paper Survey](#3-all-paper-survey) <br />


# 0. Introduction

The repository archives papers concerning **Efficient LLM Inference** and corresponding paper reading notes. 

## Contributors

Corresponding authors: 
- Yuan Mingxuan, Zhen Huiling, Bai Haoli, Chen Lei, Yin Lihao, Li Xing, Li Yiming, Jiang Yuan; @ Huawei Noah's Ark Lab EDA4LLM Group;
- He Bowei; @ City University of Hong Kong;
- Zhang Yu; @ Chinese University of Hong Kong;

## Useful Resources
- **Awesome LLM Inference**, git repo [DefTruth/Awesome-LLM-Inference](https://github.com/DefTruth/Awesome-LLM-Inference).
- **Awesome LLM**, [git repo](https://github.com/Hannibal046/Awesome-LLM), detailed LLM resource collecions. [a list of paper collections](https://github.com/Hannibal046/Awesome-LLM?tab=readme-ov-file#other-papers).

## Taxonomy

![Taxonomy of Efficient LLM Inference](https://github.com/LihaoYin/Survey-on-LLM-Inference/blob/main/Images/Taxonomy.png)

# 1. Paper Reading Progress

| Paper Title | Taxonomy | Reader | Link |
| :-------------------------------------------------------------| :-------- | :-------- | :--------|
| SpecInfer: Accelerating Large Language Model Serving with Tree-based Speculative Inference and Verification (ASPLOS 2024)| Dynamic Inference | Yin, Lihao | [paper](https://dl.acm.org/doi/abs/10.1145/3620666.3651335), [notes](#specinfer-accelerating-large-language-model-serving-with-tree-based-speculative-inference-and-verification-asplos-2024)|


# 2. All Papers (Classifications according to Taxonomy)
- [**Survey/Review**](#31-surveyreview)
    - [A Survey on Efficient Inference for Large Language Models](#a-survey-on-efficient-inference-for-large-language-models-2024)
    - [Model Compression and Efficient Inference for Large Language Models: A Survey](#model-compression-and-efficient-inference-for-large-language-models-a-survey-2024)
  
- [**大算子**](#32-大算子)

- [**Quantization**](#33-quantization)

- [**Structure Optimization**](#34-structure-optimization)

- [**Knowledge Distillation**](#35-knowledge-distillation)

- [**Dynamic Inference**](#36-dynamic-inference)
    - [SpecInfer: Accelerating Large Language Model Serving with Tree-based Speculative Inference and Verification (ASPLOS 2024)](#specinfer-accelerating-large-language-model-serving-with-tree-based-speculative-inference-and-verification-asplos-2024)

- [**System-level Optimization**](#37-system-level-optimization)

- [**Retrieval-Augmented Generation**](#38-retrieval-augmented-generation)


# 3. All Paper Reading Notes

## 3.1 Survey/Review

### A Survey on Efficient Inference for Large Language Models (2024)

* <img src="Images/pdf_24px.png">[Paper](https://arxiv.org/pdf/2404.14294)
  
**Notes:** 

### Model Compression and Efficient Inference for Large Language Models: A Survey (2024)

* <img src="Images/pdf_24px.png">[Paper](https://arxiv.org/pdf/2402.09748)
  
**Notes:**

## 3.2 大算子

## 3.3 Quantization

## 3.4 Structure Optimization

## 3.5 Knowledge Distillation

## 3.6 Dynamic Inference

### SpecInfer: Accelerating Large Language Model Serving with Tree-based Speculative Inference and Verification (ASPLOS 2024)

* <img src="Images/pdf_24px.png">[Paper](https://dl.acm.org/doi/abs/10.1145/3620666.3651335)
  
**Notes:** 

目前 LLM 推理主要依赖于自回归式（auto-regressive）的解码（decoding）方式，每步解码只能够产生一个输出 token，并且需要将历史输出内容拼接后重新作为 LLM 的输入，才能进行下一步的解码。考虑到这种数据依赖，现有 LLM 推理系统如 FasterTransformer 会采用一种增量式解码（incremental decoding）技术，将已经解码的 token 对应的 key/value 进行缓存，避免重新计算。但是，这类系统仍然面临两个关键的缺陷：1）由于逐 token 计算的解码范式，算子并行度有限，GPU 硬件资源难以被充分利用；2）当序列过长时，KV-cache 空间消耗过大，有限的 GPU 显存无法承载。因此，当面对超大规模的 LLM 推理时（如 GPT-4 32K tokens），现有系统往往面临资源利用低效，推理延迟过高的问题。

为了解决上述问题，研究者提出了一种「投机式」推理引擎 SpecInfer，其核心思想是通过计算代价远低于 LLM 的 “小模型” SSM（Small Speculative Model）替代 LLM 进行投机式地推理（Speculative Inference），每次会试探性地推理多步，将多个 SSM 的推理结果汇聚成一个 Speculated Token Tree，交由 LLM 进行验证，通过高效的树形解码算子实现并行化推理，验证通过的路径将会作为模型的推理结果序列，进行输出。SpecInfer 利用了 SSM 的内在知识帮助 LLM 以更低廉的计算成本完成了主要的推理过程，而 LLM 则在一定程度上破除了逐 token 解码的计算依赖，通过并行计算确保最终输出的结果完全符合原始的推理语义。


## 3.7 System-level Optimization

## 3.8 Retrieval-Augmented Generation
