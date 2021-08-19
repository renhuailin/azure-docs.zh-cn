---
title: Azure 流分析功能比较
description: 本文比较了 Azure 流分析云和 IoT Edge 作业在 Azure 门户、Visual Studio 和 Visual Studio Code 中支持的功能。
author: an-emma
ms.author: raan
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 0cb51198fec12590b497f94b23fce8b32b67bb8c
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122396812"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Azure 流分析功能比较

借助 Azure 流分析，可以使用 [Azure 门户](stream-analytics-quick-create-portal.md)，[Visual Studio](stream-analytics-quick-create-vs.md) 和 [Visual Studio Code](quick-create-visual-studio-code.md) 在云端和 IoT Edge 中创建流式处理解决方案。 本文中的表显示了每种平台支持这两种作业类型的哪些功能。

> [!NOTE]
> Visual Studio 和 Visual Studio Code 工具不支持“中国东部”、“中国北部”、“德国中部”和“德国东北部”区域中的作业。

## <a name="cloud-job-features"></a>云作业功能


|功能  |门户  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|跨平台     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|脚本创作     |是         |是         |是         |
|脚本 Intellisense     |语法突出显示         |语法突出显示</br>代码完成</br>错误标记         |语法突出显示</br>代码完成</br>错误标记         |
|定义所有类型的输入、输出和作业配置     |是         |是         |是         |
|源代码管理     |否         |是         |是         |
|CI/CD 支持     |部分         |是         |是         |
|在多个查询之间共享输入和输出     |否         |是         |是         |
|使用示例文件进行查询测试     |是         |是        |是         |
|实时数据本地测试     |否         |是       |是      |
|列出作业和查看作业实体     |是         |是        |是         |
|将作业导出到本地项目     |否         |是         |是         |
|提交、启动和停止作业     |是         |是         |是         |
|查看作业指标和关系图     |是         |是         |是         |
|查看作业运行时错误     |是         |是         |是         |
|资源日志     |是         |否         |是         |
|自定义消息属性     |是         |是         |是       |
|C# 自定义代码函数和反序列化程序|只读模式|是|是|
|JavaScript UDF 和 UDA     |是         |是         |仅限 Windows         |
|机器学习服务     |是        |是         |是         |
|Azure 机器学习工作室（经典）|是，但无法测试查询        |是 |否         |
|兼容性级别     |1.0</br>1.1</br>1.2（默认值）         |1.0</br>1.1</br>1.2（默认值）           |1.0</br>1.1</br>1.2（默认值）           |
|基于 ML 的内置异常情况检测函数     |是         |是         |是         |
|内置地理空间函数     |是         |是         |是         |



## <a name="iot-edge-job-features"></a>IoT Edge 作业功能

|功能  |门户  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|作业创作     |是         |是         |否         |
|源代码管理     |否         |是         |否         |
|将作业导出到本地项目     |否         |是         |否         |
|使用示例文件进行查询测试     |是         |是         |否         |
|在多个查询之间共享输入和输出     |否         |是         |否         |
|C# UDF     |否         |是         |否         |
|提交作业     |是         |是         |否         |
|列出作业和查看作业实体     |是         |是         |否         |
|查看作业指标和关系图     |是         |部分         |否         |
|查看作业运行时错误     |是         |部分         |否         |
|CI/CD 支持     |否         |否         |否         |


## <a name="next-steps"></a>后续步骤

* [Azure IoT Edge 流分析](stream-analytics-edge.md)
* [教程：为 Azure 流分析 IoT Edge 作业编写 C# 用户定义函数（预览）](stream-analytics-edge-csharp-udf.md)
* [使用 Visual Studio 工具开发流分析 IoT Edge 作业](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [使用 Visual Studio 查看 Azure 流分析作业](stream-analytics-vs-tools.md)
* [使用 Visual Studio Code（预览版）浏览 Azure 流分析](visual-studio-code-explore-jobs.md)


