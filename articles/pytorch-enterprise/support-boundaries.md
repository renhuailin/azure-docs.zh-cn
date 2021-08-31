---
title: 支持 Azure 上的 PyTorch Enterprise 边界
description: 本文定义 PyTorch Enterprise 的支持边界。
author: alonbochman
ms.author: alonbochman
ms.service: pytorch-enterprise
ms.topic: conceptual
ms.date: 07/06/2021
ms.openlocfilehash: ea2f70af6611f6bed5a36f72324874e0461182f3
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598566"
---
# <a name="support-boundaries-for-pytorch-enterprise"></a>支持 PyTorch Enterprise 边界

本简短文档介绍 Pytorch Enterprise 下支持的模块和组件。


|区域|支持的版本|说明|
|----|----|----|
|OS|Windows 10、Debian 9、Debian 10、Ubuntu 16.04.7 LTS 和 Ubuntu 18.04.5 LTS|我们支持 Debian 和 Ubuntu 发行版的 LTS 版本，仅支持 x86_64 体系结构。|
|Python|3.6+||
|PyTorch|1.8.1+||
|CUDA 工具包|10.2，11.1||
|ONNX 运行时|1.7+||
|torchtext、torchvision、torch-tb-profiler、torchaudio| - |对于尚未发布 1.0 版本的库，我们支持与相应受支持的 PyTorch 版本兼容的特定版本。 有关示例，请参阅下表：[TorchVision](https://github.com/pytorch/vision#installation)、[TorchText](https://github.com/pytorch/text#installation) 和 [TorchAudio](https://github.com/pytorch/audio/#dependencies)|
|torchserve|0.4.0+||
