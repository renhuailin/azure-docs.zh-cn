---
title: Azure Percept Vision 数据表
description: 查看 Azure Percept 远景数据表了解详细设备规范
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: reference
ms.date: 02/16/2021
ms.openlocfilehash: 8814192274a81938d53ffc68c02dfaa9ac1da8ad
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661851"
---
# <a name="azure-percept-vision-datasheet"></a>Azure Percept Vision 数据表

下面列出的规格适用于 azure Percept 远景设备，包括在 [Azure PERCEPT 深色](./azure-percept-dk-datasheet.md)中。

|产品规格           |“值”     |
|--------------------------------|---------------------|
|目标行业               |制造 <br> 智能建筑物 <br> 自动 <br> 零售 |
|英雄方案                  |购物者分析 <br> 保质期可用性 <br> 缩减缩减 <br> 安全/监视 <br> 工作区安全性|
|维度                      |42mm x 42mm x 40mm (的 Azure Percept 远景 SoM 程序集)  <br> 42mm x 42mm x 6mm (愿景 SoM 芯片) |
|管理控制平面        |Azure 设备更新 (ADU)           |
|支持的软件和服务 |[Azure IoT 中心](https://azure.microsoft.com/services/iot-hub/) <br> [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) <br> [Azure 机器学习](https://azure.microsoft.com/services/machine-learning/) <br> [ONNX 运行时](https://www.onnxruntime.ai/) <br> [OpenVINO](https://docs.openvinotoolkit.org/latest/index.html) <br> Azure 设备更新 |
|AI 加速                 |Intel Movidius 无数 X (MA2085) 视觉处理单元 (VPU) 与 Intel 相机 ISP 集成，0.7 顶部 |
|传感器和视觉指示器   |全方位远景5670照相机 <br> GSO GS8882AA Lens (分辨率： 5MP at 30FPS，距离： 50cm-无限大，FoV：120度，颜色：宽动态范围，固定焦点滚动快门)           |
|照相机支持                  |RGB、IR 和深度相机 <br> 2个照相机可以同时运行 |
|安全 Crypto-Controller      |ST-Micro STM32L462CE      |
|版本控制/ID 组件       |64kb EEPROM |
|内存                          |LPDDR4 2GB     |
|强力                           |3.5 W     |
|端口                           |1x USB 3.0 类型 C <br> 2 MIPI 4 通道 (每个通道 1.5 Gbps)      |
|控制接口              |2x I2C <br> 2x SPI <br> 6倍 PWM (GPIOs：2x 时钟、2x 帧同步、未使用的 2x)  <br> 2x 备用 GPIO |
|认证                   |CE <br> FCC      |
|操作温度           |0到27摄氏度 (Azure Percept 愿景 SoM assembly with 壳体)  <br> -10 到70摄氏度 (愿景 SoM 芯片)  |
|触摸温度               |<= 48 摄氏度 |
|相对湿度               |8% 至90%    |