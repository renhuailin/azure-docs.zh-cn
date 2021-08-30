---
title: Azure SQL Edge 发行说明
description: 发行说明详细介绍了 Azure SQL Edge 映像中的新增功能或已更改的内容。
keywords: 发行说明 SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 11/24/2020
ms.openlocfilehash: 29ab3c33611b0f525b28fe51245edba377bae0b5
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114441420"
---
# <a name="azure-sql-edge-release-notes"></a>Azure SQL Edge 发行说明 

本文介绍每个新的 Azure SQL Edge 内部版本中的新增功能或已更改的内容。

## <a name="azure-sql-edge-104"></a>Azure SQL Edge 1.0.4

SQL 引擎内部版本 15.0.2000.1559

### <a name="whats-new"></a>新增功能

- 对 ONNX 的 PREDICT 支持 
    - 改进了在对 ONNX 进行 PREDICT 时对 null 数据的处理

## <a name="azure-sql-edge-103"></a>Azure SQL Edge 1.0.3

SQL 引擎内部版本 15.0.2000.1557

### <a name="fixes"></a>修复项

- 将 ONNX 运行时升级到 1.5.3
- 更新到 Microsoft.SqlServer.DACFx 版本 150.5084.2
- 其他 Bug 修复  
   
## <a name="azure-sql-edge-102"></a>Azure SQL Edge 1.0.2

SQL 引擎内部版本 15.0.2000.1557

### <a name="fixes"></a>修复项

- T-SQL 流式处理  
   - 对流式处理对象的所有权和权限进行修复
   - 实现日志轮换和日志添加前缀的日志记录改进
   - Azure 流分析：日志记录改进，改进适配器中的错误代码/错误消息 

- ONNX
    - 并行查询方案和模型清理失败的 Bug 修复
    - 将 ONNX 运行时升级到 1.5.1

## <a name="azure-sql-edge-101"></a>Azure SQL Edge 1.0.1

SQL 引擎内部版本 15.0.2000.1553

### <a name="whats-new"></a>新增功能

- 允许在计算列中定义 Date_Bucket 表达式。

### <a name="fixes"></a>修复项

- 保留策略修补程序，用于删除启用了保留策略且具有无限超时的表
- DacFx 部署支持流式传输和保留策略功能 
- DacFx 部署修补程序，可启用从 SAS URL 中的嵌套文件夹进行部署的功能 
- 预测修补程序，用于支持错误消息中的长列名

## <a name="azure-sql-edge-100-rtm"></a>Azure SQL Edge 1.0.0 (RTM)

SQL 引擎内部版本 15.0.2000.1552

### <a name="whats-new"></a>新增功能
- 基于 Ubuntu 18.04 的容器映像 
- 支持包含 `LAST_VALUE()` 和 `FIRST_VALUE()` 函数的 `IGNORE NULL` 和 `RESPECT NULL` 语法 
- 针对 PREDICT with ONNX 的可靠性改进
- 支持基于数据保留策略的清理：
   - 环形缓冲区支持用于故障排除的保留清理任务
- 新功能支持： 
   - 快速恢复
   - 自动优化查询
   - 并行执行方案
- 针对低功耗模式的节能改进
- 流式处理新功能支持： 
   - [快照窗口](/stream-analytics-query/snapshot-window-azure-stream-analytics)：新的窗口类型允许你对同时到达的事件进行分组。
   - [TopOne](/stream-analytics-query/topone-azure-stream-analytics) 和 [CollectTop](/stream-analytics-query/collecttop-azure-stream-analytics) 可作为分析函数启用。 可以返回按你选择的列排序的记录。 它们不一定要是窗口的一部分。 
   - 对 [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics) 的改进。 

### <a name="fixes"></a>修复项
- 用于排查 T-SQL 流式处理操作问题的附加错误消息和详细信息 
- 为了在空闲模式下维持电池寿命进行的改进 
- T-SQL 流式处理引擎修复： 
   - 清理了已停止的流式处理作业 
   - 本地化修复项 
   - 改进了 Unicode 处理 
   - 提高了 SQL Edge T-SQL 流式处理的可调试性，允许用户从 get_streaming_job 查询作业失败错误
- 基于数据保留策略的清理： 
    - 修复了保留策略创建和清理方案
- 修复了后台计时器任务，以提高低功耗模式下的节能能力

### <a name="known-issues"></a>已知问题 
- 无法在计算列中使用 Date_Bucket T-SQL 函数。


## <a name="ctp-23"></a>CTP 2.3
SQL 引擎内部版本 15.0.2000.1549
### <a name="whats-new"></a>新增功能
- 支持在 Date_Bucket() 函数中自定义原点 
- 在 SQL 部署中支持 BACPAC 文件
- 支持基于数据保留策略的清理：      
   - DDL 支持启用保留策略 
   - 清理存储过程和后台清理任务
   - 用于监视清理任务的扩展事件

### <a name="fixes"></a>修复项
- 用于排查 T-SQL 流式处理操作问题的附加错误消息和详细信息 
- 为了在空闲模式下维持电池寿命进行的改进 
- T-SQL 流式处理引擎： 
   - 修复了子流跳跃窗口的卡水印问题 
   - 修复了框架异常处理，以确保将其收集为用户可操作的错误


## <a name="ctp-22"></a>CTP 2.2
SQL 引擎内部版本 15.0.2000.1546
### <a name="whats-new"></a>新增功能
- 支持非根容器 
- 支持使用情况和诊断数据收集 
- T-SQL 流式处理更新：
   - 支持流对象名称的 Unicode 字符

### <a name="fixes"></a>修复项
- T-SQL 流式处理更新：
   - 进程清理改进
   - 日志记录和诊断改进
- 针对数据引入的性能改进

## <a name="ctp-21"></a>CTP 2.1 
SQL 引擎内部版本 15.0.2000.1545
### <a name="fixes"></a>修复项
- 允许 PREDICT-with-ONNX 模型处理 ARM 中的 CPUID 问题 
- 改进了 T-SQL 流式处理启动时对故障路径的处理
- 更正了没有数据时作业指标中水印延迟的值。
- 修复了输出适配器在批之间具有可变架构时出现的问题  

## <a name="ctp-20"></a>CTP 2.0 
SQL 引擎内部版本 15.0.2000.1401
### <a name="whats-new"></a>新增功能
-   产品名称更新为 Azure SQL Edge
-  Date_bucket 函数：
    - 支持日期、时间和日期/时间类型
- PREDICT with ONNX：
    - RUNTIME 参数的 ONNX 要求  
- T-SQL 流式处理支持（受限预览版） 
 
### <a name="known-issues"></a>已知问题

- 问题：如果在启动时应用 DACPAC，可能会因计时问题而出现故障。
- 解决方法：请重新启动 SQL Server。 否则，容器将重试应用 DACPAC。

### <a name="request-support"></a>请求支持
可以在[支持页面](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)上请求支持。 选择以下字段： 
- **问题类型**：*技术* 
- **服务**：*IoT Edge*
- 问题类型：我的问题与 IoT Edge 模块相关
- 问题子类型：*Azure SQL Edge*

:::image type="content" source="media/get-support/support-ticket.png" alt-text="显示示例支持票证的屏幕截图。":::

## <a name="ctp-15"></a>CTP 1.5
SQL 引擎内部版本 15.0.2000.1331
### <a name="whats-new"></a>新增功能
- Date_bucket 函数：
    - 支持 DateTimeOffset 类型
- PREDICT with ONNX 模型：
  - NVARCHAR 支持
 
## <a name="ctp-14"></a>CTP 1.4
SQL 引擎内部版本 15.0.2000.1247
### <a name="whats-new"></a>新增功能
-   PREDICT with ONNX 模型：
    - VARCHAR 支持
    - 迁移到 ONNX 运行时版本 1.0 

- 启用了以下功能：
  - CDC 支持
  - 带压缩功能的历史记录表
  - 提高预读日志的比例因子
  - 批处理模式 ES 筛选器下推
  - 预读优化
 
## <a name="ctp-13"></a>CTP 1.3
SQL 引擎内部版本 15.0.2000.1147
### <a name="whats-new"></a>新增功能
- Azure IOT 门户部署： 
    - 支持部署 AMD64 和 ARM 映像
    - 支持创建流式处理作业
    - DACPAC 部署
- PREDICT with ONNX 模型：
    - 数值类型支持
- 启用了以下功能：
    - 将聚合下推到列存储扫描
    - 旋转木马式扫描
- 占用空间和内存消耗缩减工作
