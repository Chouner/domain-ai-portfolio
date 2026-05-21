# ARIS Jellyfish Detection / ARIS 水母检测

## One-line Summary / 一句话概述

A multimodal-assisted annotation and detection workflow for jellyfish targets in ARIS sonar images.

一个面向 ARIS 声呐图像中水母目标的多模态辅助标注与检测工作流。

## Core Problem / 核心问题

ARIS sonar images have fuzzy boundaries, dense targets, sparse texture, and high annotation cost.

ARIS 声呐图像存在边界模糊、目标密集、纹理稀疏和标注成本高的问题。

## Core Innovation / 核心创新

Use multimodal LLMs to reduce annotation cost, then train a stable detection model and refine results with traditional image processing.

使用多模态大模型降低标注成本，再训练稳定检测模型，并结合传统图像处理进行结果修正。

## Key Evidence / 关键证据

- Parsed 86,342 ARIS frames / 解析 86,342 帧 ARIS 数据
- Single-frame size: 512 x 96 / 单帧尺寸：512 x 96
- Generated sonar tensor: (86342, 512, 2) / 生成声呐张量：(86342, 512, 2)
- Ran YOLO detection on 74,092 video frames / 对 74,092 帧视频执行 YOLO 检测
- YOLO model: 157 layers, 7,012,822 parameters, 15.8 GFLOPs / YOLO 模型：157 层，7,012,822 参数，15.8 GFLOPs
- Observed inference speed improved from about 5.6 FPS to about 26.7 FPS during processing logs / 日志中观察到推理速度从约 5.6 FPS 提升到约 26.7 FPS

