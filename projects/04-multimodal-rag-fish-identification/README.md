# Multimodal RAG Fish Identification / 多模态 RAG 鱼类识别

## One-line Summary / 一句话概述

A multimodal RAG workflow for deep-sea fish identification under small-sample, low-quality image conditions.

一个面向小样本、低质量图像条件下深海鱼类识别的多模态 RAG 工作流。

## Core Problem / 核心问题

Deep-sea fish images often have low quality, limited samples, and many visually similar species. Pure image classification methods such as YOLO/CLIP performed poorly in early trials.

深海鱼类图像通常质量较低、样本有限，且相似物种较多。早期试验中，纯图像分类方法如 YOLO/CLIP 表现不佳。

## Core Innovation / 核心创新

Reframe fish identification from pure visual classification into image understanding -> species knowledge retrieval -> expert-style reasoning answer.

将鱼类识别从纯视觉分类问题，重构为图像理解 -> 物种知识检索 -> 专家式推理回答。

## Tech Stack / 技术栈

- Python / Python
- LangChain / LangChain
- Chroma / Chroma
- DashScope API / DashScope API
- Qwen3-VL-Plus / Qwen3-VL-Plus
- Qwen3-Max / Qwen3-Max
- text-embedding-v4 / text-embedding-v4

