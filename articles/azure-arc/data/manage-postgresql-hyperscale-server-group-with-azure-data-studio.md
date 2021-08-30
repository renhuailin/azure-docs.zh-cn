---
title: 使用 Azure Data Studio 管理 PostgreSQL 实例
description: 使用 Azure Data Studio 管理 PostgreSQL 实例
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 1849e317c4245eb1aa96ff96f0ffac8bd8425299
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750264"
---
# <a name="use-azure-data-studio-to-manage-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>使用 Azure Data Studio 管理已启用 Azure Arc 的 PostgreSQL 超大规模服务器组


本文介绍如何：
- 使用“概述”、“连接字符串”、“属性”、“资源运行状况”等仪表板视图管理 PostgreSQL 实例
- 处理数据和架构

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>先决条件

- [安装 azdata、Azure Data Studio 和 Azure CLI](install-client-tools.md)
- 在 Azure Data Studio 中安装 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]、Azure Arc 和 PostgreSQL 扩展  

   [!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

- 创建 [Azure Arc 数据控制器](./create-data-controller-indirect-cli.md)
- 启动 Azure Data Studio

## <a name="connect-to-the-azure-arc-data-controller"></a>连接到 Azure Arc 数据控制器

在 Azure Data Studio 中，展开“Azure Arc 控制器”节点并选择“连接控制器”按钮 ：

输入 Azure 数据控制器连接信息：

- **控制器 URL：**

    用于连接到 Kubernetes 中的控制器的 URL。 以 `https://<IP_address_of_the_controller>:<Kubernetes_port.` 格式输入。例如：

    ```console
    https://12.345.67.890:30080
    ```
- **用户名：**

    用于连接到控制器的用户帐户的名称。 请使用在运行 `az login` 时通常使用的名称。 此名称不是从 psql 连接到 PostgreSQL 数据库引擎时通常使用的 PostgreSQL 用户名称。
- 密码：用于连接到控制器的用户帐户的密码。


Azure Data Studio 将显示你的 Arc 数据控制器。 展开该数据控制器，它会显示它所管理的 PostgreSQL 实例的列表。

## <a name="manage-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>管理已启用 Azure Arc 的 PostgreSQL 超大规模服务器组

右键单击要管理的 PostgreSQL 实例并选择“[管理]”

PostgreSQL 仪表板视图：

它具有该窗格左侧列出的多个仪表板：

- 概述：显示有关实例的摘要信息，例如名称、PostgreSQL 管理员用户名、Azure 订阅 ID、配置、数据库引擎版本、Grafana 和 Kibana 的终结点，等等
- 连接字符串：显示连接到 PostgreSQL 实例（例如 psql、Node.js、PHP、Ruby 等）时可能需要用到的各种连接字符串
- 诊断并解决问题：这是登陆页，在其中可以查找各种资源，随着我们不断扩充故障排除笔记本的内容，这些资源可帮助你对实例进行故障排除
- 新建支持请求：这是登陆页，从宣布推出公共预览版开始，你可以在此页中向我们的支持服务部门请求帮助。

## <a name="work-with-your-data-and-schema"></a>处理数据和架构

在 Azure Data Studio 窗口的左侧，展开“服务器”节点：

然后选择“[添加连接]”，并填写 PostgreSQL 实例连接详细信息：
- 连接类型：PostgreSQL
- 服务器名称：输入 PostgreSQL 实例的名称。 例如：postgres01
- 身份验证类型：密码
- 用户名：例如，可以使用标准/默认的 PostgreSQL 管理员用户名。 请注意，此字段区分大小写。
- 密码：可以在 `az postgres arc-server endpoint -n postgres01` 命令输出的 psql 连接字符串中找到 PostgreSQL 用户名的密码
- 数据库名称：设置要连接到的数据库的名称。 可将其保持设置为“默认”
- 服务器组：可将其保持设置为“默认”
- 名称（可选）：可将其留空
- 高级：
    - 主机 IP 地址：Kubernetes 群集的公共 IP 地址
    - 端口：PostgreSQL 实例侦听的端口。 可在 `az postgres arc-server endpoint -n postgres01` 命令输出的 psql 连接字符串的末尾找到此端口。 不是 Kubernetes 正在侦听的、当你在 Azure Data Studio 中连接到 Azure 数据控制器时输入的端口 30080。
    - 其他参数：这些参数应该都是自释性的，可以保留为它们显示的默认值/空白值。

选择“[确定]”和“[连接]”以连接到服务器。

连接后，可以使用多个体验：
- **新查询**
- **新建笔记本**
- **展开服务器的显示内容并浏览/处理数据库中的对象**
- **...**

## <a name="next-step"></a>后续步骤
[监视服务器组](monitor-grafana-kibana.md)