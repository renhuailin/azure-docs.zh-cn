---
title: Azure SQL Edge 发行说明
description: 详细说明了 Azure SQL Edge 映像中的新增功能或更改的内容。
keywords: 发行说明 SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 11/24/2020
ms.openlocfilehash: c18cb1cef9b46b4b3cd2f614945f98abf9de7c58
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704344"
---
# <a name="azure-sql-edge-release-notes"></a>Azure SQL Edge 发行说明 

本文介绍 Azure SQL Edge 的每个新版本的新增功能和变化。

## <a name="azure-sql-edge-101"></a>Azure SQL Edge 1.0。1

SQL 引擎生成15.0.2000.1553

### <a name="whats-new"></a>新增功能

- 允许在计算列中定义 Date_Bucket 表达式。

### <a name="fixes"></a>修复项

- 保留策略修复，用于删除启用了保留策略且具有无限超时的表
- DacFx 部署对流式处理功能和保留策略功能的支持 
- DacFx 部署修复，以允许从 SAS URL 中的嵌套文件夹进行部署 
- 预测修补程序，用于支持错误消息中的长列名

## <a name="azure-sql-edge-100-rtm"></a>Azure SQL Edge 1.0.0 (RTM) 

SQL 引擎生成15.0.2000.1552

### <a name="whats-new"></a>新增功能
- 基于 Ubuntu 18.04 的容器映像 
- 和函数的支持 `IGNORE NULL` 和 `RESPECT NULL` 语法 `LAST_VALUE()` `FIRST_VALUE()` 
- 通过 ONNX 预测的可靠性改进
- 基于数据保留策略的清理支持：
   - 用于故障排除的保留清理任务的环形缓冲区支持
- 新功能支持： 
   - 快速恢复
   - 自动优化查询
   - 并行执行方案
- 低能耗模式的节能改善
- 流式处理新功能支持： 
   - [快照窗口](/stream-analytics-query/snapshot-window-azure-stream-analytics)：新的窗口类型允许你按同时到达的事件进行分组。
   - 可以将[TopOne](/stream-analytics-query/topone-azure-stream-analytics)和[CollectTop](/stream-analytics-query/collecttop-azure-stream-analytics)启用为分析函数。 您可以返回按您选择的列排序的记录。 它们不需要是窗口的一部分。 
   - 对 [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics) 的改进。 

### <a name="fixes"></a>修复项
- T-sql 流式处理操作故障排除的其他错误消息和详细信息 
- 改善了在空闲模式下保留电池寿命 
- T-sql 流式处理引擎修复： 
   - 清除已停止的流式处理作业 
   - 本地化修补程序 
   - 改进了 Unicode 处理 
   - 改善了 SQL Edge T-sql 流式处理的调试，使用户能够从 get_streaming_job 查询作业失败错误
- 基于数据保留策略的清理： 
    - 保留策略创建和清理方案的修复
- 修复了后台计时器任务，以提高低功耗模式下的节能能力

### <a name="known-issues"></a>已知问题 
- 不能在计算列中使用 Date_Bucket T-sql 函数。


## <a name="ctp-23"></a>CTP 2.3
SQL 引擎生成15.0.2000.1549
### <a name="whats-new"></a>新增功能
- 支持 Date_Bucket ( # A1 函数中的自定义源 
- 支持作为 SQL 部署的一部分的 BACPAC 文件
- 基于数据保留策略的清理支持：      
   - 启用保留策略的 DDL 支持 
   - 清除存储过程和后台清除任务
   - 用于监视清理任务的扩展事件

### <a name="fixes"></a>修复项
- T-sql 流式处理操作故障排除的其他错误消息和详细信息 
- 改善了在空闲模式下保留电池寿命 
- T-sql 流式处理引擎： 
   - 修复 substreamed 跳跃窗口中停滞水印的问题 
   - 解决框架异常处理问题，以确保将其收集为用户可操作的错误


## <a name="ctp-22"></a>CTP 2.2
SQL 引擎生成15.0.2000.1546
### <a name="whats-new"></a>新增功能
- 支持 nonroot 容器 
- 支持使用情况和诊断数据收集 
- T-sql 流更新：
   - 支持流对象名称的 Unicode 字符

### <a name="fixes"></a>修复项
- T-sql 流更新：
   - 进程清理改进
   - 日志记录和诊断改进
- 针对数据引入的性能改进

## <a name="ctp-21"></a>CTP 2.1 
SQL 引擎生成15.0.2000.1545
### <a name="fixes"></a>修复项
- 允许预测 ONNX 模型处理 ARM 中的 CPUID 问题 
- 提高了 T-sql 流式处理启动时失败路径的处理
- 如果没有数据，则更正了作业指标中水印延迟的值。
- 修复了当适配器在批处理之间具有可变架构时输出适配器的问题  

## <a name="ctp-20"></a>CTP 2.0 
SQL 引擎生成15.0.2000.1401
### <a name="whats-new"></a>新增功能
-   产品名称已更新到 *AZURE SQL Edge*
-  Date_Bucket 函数：
    - 支持日期、时间和日期时间类型
- 通过 ONNX 预测：
    - 运行时参数的 ONNX 要求  
- T-sql 流式处理支持 (有限预览)  
 
### <a name="known-issues"></a>已知问题

- 问题：由于计时问题，在启动时应用 DACPAC 可能出现故障。
- 解决方法：重新启动 SQL Server。 否则，容器将重试应用 DACPAC。

### <a name="request-support"></a>请求支持
你可以在 [支持页](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)上请求支持。 选择以下字段： 
- **问题类型**：*技术* 
- **服务**： *IoT Edge*
- **问题类型**： *我的问题与 IoT Edge 模块相关*
- **问题子类型**： *Azure SQL Edge*

:::image type="content" source="media/get-support/support-ticket.png" alt-text="显示示例支持票证的屏幕截图。":::

## <a name="ctp-15"></a>CTP 1.5
SQL 引擎生成15.0.2000.1331
### <a name="whats-new"></a>新增功能
- Date_Bucket 函数：
    - 对 DateTimeOffset 类型的支持
- 预测 with ONNX 模型：
  - NVARCHAR 支持
 
## <a name="ctp-14"></a>CTP 1.4
SQL 引擎生成15.0.2000.1247
### <a name="whats-new"></a>新增功能
-   预测 with ONNX 模型：
    - VARCHAR 支持
    - 迁移到 ONNX 运行时版本 1.0 

- 启用以下功能：
  - CDC 支持
  - 带压缩功能的历史记录表
  - 更高的日志预读系数
  - 批处理模式 ES 筛选器下推
  - 预读优化
 
## <a name="ctp-13"></a>CTP 1.3
SQL 引擎生成15.0.2000.1147
### <a name="whats-new"></a>新增功能
- Azure IoT 门户部署： 
    - 支持部署 AMD64 和 ARM 映像
    - 支持创建流式处理作业
    - DACPAC 部署
- 预测 with ONNX 模型：
    - 数值类型支持
- 启用以下功能：
    - 将聚合下推到列存储扫描
    - 旋转木马式扫描
- 占用空间和内存消耗缩减工作
