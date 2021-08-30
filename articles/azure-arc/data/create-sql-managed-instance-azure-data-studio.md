---
title: 使用 Azure Data Studio 创建 Azure SQL 托管实例
description: 使用 Azure Data Studio 创建 Azure SQL 托管实例
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 15d4ba669f736fd1322c137a16d8b79f40cea6d5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743882"
---
# <a name="create-sql-managed-instance---azure-arc-using-azure-data-studio"></a>使用 Azure Data Studio 创建 SQL 托管实例 - Azure Arc

本文档分步讲解如何使用 Azure Data Studio 安装 Azure SQL 托管实例 - Azure Arc

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

## <a name="create-azure-sql-managed-instance-on-azure-arc"></a>在 Azure Arc 上创建 Azure SQL 托管实例

- 启动 Azure Data Studio
- 在“连接”选项卡上，单击左上角的三个点，然后选择“新建部署”
- 从部署选项中，选择“Azure SQL 托管实例 - Azure Arc” 
  > [!NOTE]
  > 如果当前未安装，系统可能会提示在此处安装适当的 CLI。
- 接受“隐私和许可条款”，然后单击底部的“选择”

- 在“部署 Azure SQL 托管实例 - Azure Arc”边栏选项卡中输入以下信息：
  - 为 SQL Server 实例输入名称
  - 为 SQL Server 实例输入并确认密码
  - 选择适用于数据的存储类
  - 选择适用于日志的存储类

- 单击“部署”按钮

- 这应该会启动在数据控制器上创建 Azure SQL 托管实例 - Azure Arc。

- 几分钟后，创建应成功完成

## <a name="connect-to-azure-sql-managed-instance---azure-arc-from-azure-data-studio"></a>从 Azure Data Studio 连接到 Azure SQL 托管实例 - Azure Arc

- 使用以下命令查看预配的所有 Azure SQL 托管实例：

```azurecli
az sql mi-arc list --k8s-namespace <namespace> --use-k8s
```

输出应与此类似，从此处复制 ServerEndpoint（包括端口号）。

```console

Name          Replicas    ServerEndpoint     State
------------  ----------  -----------------  -------
sqlinstance1  1/1         25.51.65.109:1433  Ready
```

- 在 Azure Data Studio 的“连接”选项卡下，单击“服务器”视图上的“新建连接”  
- 在“连接”边栏选项卡中，将 ServerEndpoint 粘贴到服务器文本框中
- 选择“SQL 登录”作为身份验证类型
- 输入“sa”作为用户名
- 输入 `sa` 帐户的密码
- （可选）输入要连接到的特定数据库名称
- （可选）选择/添加适当的新服务器组
- 选择“连接”以连接到 Azure SQL 托管实例 - Azure Arc

## <a name="next-steps"></a>后续步骤

现在尝试[监视 SQL 实例](monitor-grafana-kibana.md)
