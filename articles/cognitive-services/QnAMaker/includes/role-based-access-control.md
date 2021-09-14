---
title: include 文件
description: include 文件
ms.topic: include
ms.custom: include file
ms.date: 03/11/2020
ms.openlocfilehash: 615346c16fbbb31bcefc53fc68d04c1255ece88c
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123453419"
---
提供以下角色以进行协作：

|角色|功能|API 访问|API 权限|
|--|--|--|--|
|“所有者”|全部|身份验证密钥|全部|
|参与者|除了将新成员添加到角色之外的所有功能|身份验证密钥|除了将新成员添加到角色之外的所有功能|
|QnA Maker 读取<br>（读取）|导出/下载<br>测试|持有者令牌|1. 下载 KB API<br>2. 列出用户 API 的 KB<br>3. 获取知识库详细信息<br>4. 下载变更<br>生成答案 |
|QnA Maker 编辑器<br>（读取/写入）|导出/下载<br>测试<br>更新知识库<br>导出知识库<br>导入知识库<br>替换知识库<br>创建知识库|持有者令牌|1. 创建 KB API<br>2. 更新 KB API<br>3. 替换 KB API<br>4. 替换变更<br>5.“训练 API”[在新服务模型 v5 中]|
|认知服务用户<br>（读取/写入/发布）|全部|身份验证密钥|对认知服务资源的完全访问权限，但无法： <br>1. 将新成员添加到角色。<br>2. 创建新资源。|
