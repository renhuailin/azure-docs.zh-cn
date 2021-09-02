---
title: Azure Percept Vision 数据表
description: 有关详细设备规格，请查看 Azure Percept Vision 数据表
author: MrHamlet
ms.author: amiyouss
ms.service: azure-percept
ms.topic: reference
ms.date: 02/16/2021
ms.openlocfilehash: cc15e8986058e01116341e970bfcf90799a5d416
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222393"
---
# <a name="azure-percept-vision-datasheet"></a>Azure Percept Vision 数据表

下面列出的规格适用于 [Azure Percept DK](./azure-percept-dk-datasheet.md) 中包括的 Azure Percept Vision 设备。

|产品规格           |“值”     |
|--------------------------------|---------------------|
|目标行业               |制造 <br> 智能建筑 <br> Auto <br> Retail |
|Hero 方案                  |购物者分析 <br> 保质期 <br> 收缩减少 <br> 工作区监视|
|维度                      |42 毫米 x 42 毫米 x 40 毫米（带外壳的 Azure Percept Vision SoM 程序集） <br> 42 毫米 x 42 毫米 x 6 毫米（Vision SoM 芯片）|
|管理控制平面        |Azure Device Update (ADU)          |
|受支持的软件和服务 |[Azure IoT 中心](https://azure.microsoft.com/services/iot-hub/) <br> [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) <br> [Azure 机器学习](https://azure.microsoft.com/services/machine-learning/) <br> [ONNX 运行时](https://www.onnxruntime.ai/) <br> [OpenVINO](https://docs.openvinotoolkit.org/latest/index.html) <br> Azure Device Update |
|AI 加速                 |Intel Movidius Myriad X (MA2085) 视觉处理单元 (VPU)，集成 Intel 相机 ISP，0.7 TOPS |
|传感器和视觉对象指示器   |带有 6P 镜头的 Sony IMX219 相机传感器<br>分辨率：8MP/30FPS；距离：50 厘米 - 无限<br>FoV：120 度对角线；颜色：宽动态范围，定焦卷帘快门|
|相机支持                  |RGB |
|安全加密控制器      |ST-Micro STM32L462CE      |
|版本控制/ID 组件       |64 kb EEPROM |
|内存                          |LPDDR4 2GB     |
|强力                           |3.5 W     |
|端口                           |1 个 USB 3.0 Type C <br> 2 个 MIPI 4 通道（每个通道高达 1.5 Gbps）     |
|控制接口              |2 个 I2C <br> 2 个 SPI <br> 6 个 PWM（GPIO：2 个用于时钟，2 个用于帧同步，2 个未使用） <br> 2 个备用 GPIO |
|认证                   |CE <br> ACMA <br> FCC <br> IC <br> VCCI <br> NRTL <br> CB  |
|工作温度           |0 至 27 摄氏度（带外壳的 Azure Percept Vision SoM 程序集） <br> -10 到 70 摄氏度（Vision SoM 芯片） |
|触摸温度               |<= 48 摄氏度 |
|相对湿度               |8% 到 90%    |
