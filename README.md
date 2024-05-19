# Survey-on-LLM-Inference

## Contents

- [0. Introduction](#0-Introduction) <br />

- [1. Paper Reading Progress](#1-Paper-Reading-Progress) <br />

- [2. All Papers (Classifications according to Related Topics)](#2-all-papers-classifications-according-to-related-topics) <br />

- [3. All Paper Survey](#3-all-paper-survey) <br />


# 0. Introduction

The repository archives papers concerning **Efficient LLM Inference** and corresponding paper reading notes. 

## Contributors

Corresponding authors: Yuan Mingxuan, Zhen Huiling, Bai Haoli, Chen Lei, Yin Lihao, Li Xing, Li Yiming, Jiang Yuan; @ Huawei Noah's Ark Lab EDA4LLM Group;

## Useful Reference

## Taxonomy

![Taxonomy of Efficient LLM Inference](https://github.com/LihaoYin/Survey-on-LLM-Inference/blob/main/Images/Taxonomy.png)

# 1. Paper Reading Progress

| Paper Title | Taxonomy | Reader | Link |
| :-------------------------------------------------------------| :-------- | :-------- | :--------|
| SpecInfer: Accelerating Large Language Model Serving with Tree-based Speculative Inference and Verification (ASPLOS 2024)| Dynamic Inference | Yin, Lihao | [paper](https://dl.acm.org/doi/abs/10.1145/3620666.3651335), [note](#specinfer-accelerating-large-language-model-serving-with-tree-based-speculative-inference-and-verification-asplos-2024)|


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

Speculator 的主要作用是利用 SSM 快速产生对 LLM 未来输出的推测结果，SSM 可以是（微调后）小版本的 LLM（如 LLaMA 7B），也可以是量化或蒸馏的小规模 LLM，还可以是可供检索的知识库（如参考文本）亦或是用户的自定义函数。总之，SSM 的输出结果越接近 LLM，验证时才会更容易通过，整体的推理效率才会更高。为此，SpecInfer 引入集成学习的思想，将多个 SSM 的结果融合，提高输出的差异化程度。为了尽可能提高匹配率，Speculator 提出了 Collective Boost-Tuning 方法，即在一个公开的通用数据集（如 OpenWebText）上，从一个较弱的 SSM 开始进行微调，将匹配程度较低的序列不断从数据中过滤，交由新的 SSM 来学习，持续多次，提高整体的推测质量；此外，Speculator 还引入了一个可学习的调度器（scheduler）来决定选用哪些 SSM 以获得更长的匹配序列长度。

SSM 的推理速度优势是 SpecInfer 能够加速推理的前提，但另一个不可或缺的因素就是 LLM 对并行化推理的支持。在 SpecInfer 中，LLM 并不直接作为推理引擎产生输出 token，但是它需要对 Speculator 中 SSM 产生的 token 进行验证，确保输出内容符合 LLM 的推理语义。在 SpecInfer 中，SSM 产生的输出序列会被组织成 token tree 的树形结构，避免冗余的存储开销。为了能够在 token tree 上进行并行化的验证，SpecInfer 提出了一种树形注意力（Tree Attention）计算方法，通过构造的 mask 矩阵和基于深度优先的 KV-cache 更新机制，Verifier 可以在不增加额外存储的同时，尽可能并行化树中每一条路径的解码过程。相比于朴素的逐序列或逐 Token 的解码方式，树形解码可以同时在内存开销和计算效率上达到最优。

## 3.7 System-level Optimization

## 3.8 Retrieval-Augmented Generation
