---
title: Azure IoT 设备和应用程序开发简介
description: 了解如何使用 Azure IoT 来开发嵌入式设备和构建支持设备的云应用程序。
author: ryanwinter
ms.author: rywinter
ms.service: iot-develop
ms.topic: overview
ms.date: 01/11/2021
ms.openlocfilehash: dd4e53eebe6593db457798f009d3d05ddcbd77b8
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654707"
---
# <a name="what-is-azure-iot-device-and-application-development"></a>Azure IoT 设备和应用程序开发指的是什么？

Azure IoT 是可以连接、监视和控制 IoT 设备的托管服务与平台服务的集合。 Azure IoT 为开发人员提供一组全面的选项。 选项包括设备平台、支持性云服务、SDK，以及用于生成设备所支持的云应用程序的工具。

本文为想要开始使用 Azure IoT 的开发人员概述了几条关键注意事项。 这些概念将引导 IoT 设备开发人员熟悉 Azure IoT 选项，帮助他们着手进行开发。 具体而言，本文概述了以下概念：
- [了解设备开发角色](#device-development-roles)
- [选择硬件](#choosing-your-hardware)
- [选择 SDK](#choosing-an-sdk)
- [选择连接选项](#selecting-connection-options)

## <a name="device-development-roles"></a>设备开发角色
本文将介绍在设备开发人员之间可以注意到的两个常用角色。 本文中所用的角色是指相关开发任务的集合。 了解你当前充当的开发角色类型会很有用。 角色会影响所能做出的许多开发选择。

* **设备应用程序开发：** 与现代开发实践相一致，面向许多高阶语言，在 Windows 或 Linux 等通用操作系统上执行。

* **嵌入式设备开发：** 描述面向资源受限型设备的开发。 资源受限型设备往往用于降低每单位成本、功耗或设备大小。 这些设备能够直接控制其执行所在的硬件平台。

### <a name="device-application-development"></a>设备应用程序开发
设备应用程序开发人员需要适应现有设备，以连接到云并集成到其 IoT 解决方案中。 这些设备能够支持高阶语言（例如 C# 或 Python），并且往往支持 Windows 或 Linux 等可靠的通用操作系统。 常见目标设备包括电脑、容器、Raspberry Pi 和移动设备。 

这些开发人员不是大规模开发受限型设备，而是专注于实现其云解决方案所需的特定 IoT 方案。 其中一些开发人员也会出于其云解决方案的原因而使用受限型设备。 对于使用受限型设备的开发人员，请参阅下面的[嵌入式设备开发](#embedded-device-development)路径。

> [!TIP]
> 请参阅[非受限型设备 SDK](about-iot-sdks.md#unconstrained-device-sdks) 获取入门信息。

### <a name="embedded-device-development"></a>嵌入式设备开发
嵌入式开发面向内存和处理能力有限的受限型设备。 与传统的开发平台相比，受限型设备会限制可实现的目标。

嵌入式设备通常使用实时操作系统 (RTOS)，或者根本不使用操作系统。 由于缺少通用操作系统，嵌入式设备可以全面控制其硬件。 这一事实使得嵌入式设备成了实时系统的极好选择。

当前的嵌入式 SDK 面向 **C** 语言。 嵌入式 SDK 不提供操作系统，也不提供 Azure RTOS 支持。 它们是在考虑到嵌入式目标的前提下设计的。 设计注意事项包括尽最大限度减少空间占用量，并采用无需分配内存的设计。

如果设备能够运行通用操作系统，则我们建议遵循[设备应用程序开发](#device-application-development)路径。 该路径提供更丰富的一组开发选项。

> [!TIP]
> 请参阅[受限型设备 SDK](about-iot-sdks.md#constrained-device-sdks) 获取入门信息。

## <a name="choosing-your-hardware"></a>选择硬件
Azure IoT 设备是 IoT 解决方案的构建基块，负责观察环境并与之交互。 有许多不同类型的 IoT 设备，了解现存的设备类型以及这些设备如何影响开发过程会很有帮助。

有关本文中所述设备类型之间的差异的详细信息，请参阅[关于 IoT 设备类型](concepts-iot-device-types.md)。

## <a name="choosing-an-sdk"></a>选择 SDK
Azure IoT 设备开发人员可以借助各种不同的设备 SDK 和 Azure 服务 SDK 来生成设备所支持的云应用程序。 SDK 能使开发工作变得简单有序，并可大大简化连接和管理设备的复杂性。 

如[设备开发角色](#device-development-roles)部分所述，有三种类型的 IoT SDK 可用于设备开发：
- 嵌入式设备 SDK（适用于受限型设备）
- 设备 SDK（用于通过高阶语言将现有设备连接到 IoT 应用程序）
- 服务 SDK（用于生成可将设备连接到服务的 Azure IoT 解决方案）

若要详细了解如何选择 Azure IoT 设备或服务 SDK，请参阅 [Azure IoT 设备 SDK 概述](about-iot-sdks.md)。

## <a name="selecting-connection-options"></a>选择连接选项
开发过程中的一个重要步骤是选择用于连接和管理设备的一组选项。 需要考虑两个重要方面：
- 选择 IoT 应用程序平台来托管设备。 对于 Azure IoT 而言，这意味着要选择 IoT 中心或 IoT Central。
- 选择开发人员工具以帮助连接、管理和监视设备。

若要详细了解如何选择应用程序平台和工具，请参阅[概述：适用于 Azure IoT 设备开发人员的连接选项](concepts-overview-connection-options.md)。

## <a name="next-steps"></a>后续步骤
选择与你的开发角色最为相关的以下快速入门系列之一。 这些文章演示了有关创建用于托管设备的 Azure IoT 应用程序、使用 SDK、连接设备和发送遥测数据的基础知识。  
- 有关设备应用程序开发：[快速入门：将来自设备的遥测数据发送到 Azure IoT Central](quickstart-send-telemetry-python.md)
- 有关嵌入式设备开发：[Azure IoT 嵌入式设备开发入门](quickstart-device-development.md)
