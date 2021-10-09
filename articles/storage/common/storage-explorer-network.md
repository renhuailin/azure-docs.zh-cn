---
title: Azure 存储资源管理器中的网络连接 | Microsoft Docs
description: 有关在 Azure 存储资源管理器中连接到网络的文档
services: storage
author: MRayermannMSFT
ms.service: storage
ms.topic: article
ms.date: 04/01/2021
ms.author: marayerm
ms.openlocfilehash: 2731f4473b726e7433167fe15c9e1f29c775d9cb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627874"
---
# <a name="network-connections-in-storage-explorer"></a>存储资源管理器中的网络连接

在未连接到本地仿真器时，存储资源管理器使用你的网络向存储资源及其他 Azure 服务和 Microsoft 服务发出请求。

## <a name="hostnames-accessed-by-storage-explorer"></a>存储资源管理器访问的主机名

存储资源管理器向正在使用中的各个终结点发出请求。 以下列表详细说明了存储资源管理器向其发出请求的常见主机名：

- ARM 终结点：
  - `management.azure.com`（全球 Azure）
  - `management.chinacloudapi.cn`（Azure 中国）
  - `management.microsoftazure.de`（Azure 德国）
  - `management.usgovcloudapi.net`（Azure 美国政府）
- 登录终结点：
  - `login.microsoftonline.com`（全球 Azure）
  - `login.chinacloudapi.cn`（Azure 中国）
  - `login.microsoftonline.de`（Azure 德国）
  - `login.microsoftonline.us`（Azure 美国政府）
- Graph 终结点：
  - `graph.windows.net`（全球 Azure）
  - `graph.chinacloudapi.cn`（Azure 中国）
  - `graph.cloudapi.de`（Azure 德国）
  - `graph.windows.net`（Azure 美国政府）
- Azure 存储终结点：
  - `(blob|file|queue|table|dfs).core.windows.net`（全球 Azure）
  - `(blob|file|queue|table|dfs).core.chinacloudapi.cn`（Azure 中国）
  - `(blob|file|queue|table|dfs).core.cloudapi.de`（Azure 德国）
  - `(blob|file|queue|table|dfs).core.usgovcloudapi.net`（Azure 美国政府）
- 存储资源管理器更新：`storageexplorerpublish.blob.core.windows.net`
- Microsoft 链接转发：
  - `aka.ms`
  - `go.microsoft.com`
- 资源所在的任何自定义域、专用链接或特定于 Azure Stack 实例的终结点
- 远程仿真器主机名

## <a name="proxy-sources"></a>代理源

存储资源管理器提供多个选项用于指定它如何/在何处寻找连接到代理所需的信息。 若要更改所用的选项，请转到“设置”（左侧垂直工具栏上的齿轮图标）>“应用程序” > “代理”  。 进入“设置”的“代理”部分后，可以选择你希望存储资源管理器如何/在何处寻找代理设置：
- 不使用代理
- 使用环境变量
- 使用应用代理设置
- 使用系统代理（预览版）

### <a name="do-not-use-proxy"></a>不使用代理

选择此选项后，存储资源管理器不会尝试连接到代理。 “不使用代理”是默认选项。

### <a name="use-environment-variables"></a>使用环境变量

选择此选项后，存储资源管理器将从特定的环境变量查找代理信息。 这些变量包括：
- `HTTP_PROXY`
- `HTTPS_PROXY`

如果定义了这两个变量，则存储资源管理器将从 `HTTPS_PROXY` 寻找代理信息。

这些环境变量的值必须是采用以下格式的 URL：

`(http|https)://(username:password@)<hostname>:<port>`

只有协议 (`http|https`) 和主机名是必需的。 如果你有用户名，你并非必须要提供密码。

### <a name="use-app-proxy-settings"></a>使用应用代理设置

选择此选项后，存储资源管理器将使用应用内部的代理设置。 这些设置包括：
- 协议
- 主机名
- 端口
- 使用/不使用凭据
- 凭据

可从以下任一位置管理除凭据以外的其他所有设置：
- “设置”（左侧垂直工具栏上的齿轮图标）>“应用程序” > “代理” > “使用凭据”   。
- "代理设置"对话框（“编辑” > “配置代理”） 。

若要设置凭据，必须转到“代理设置”对话框（“编辑” > “配置代理”） 。

### <a name="use-system-proxy-preview"></a>使用系统代理（预览版）

选择此选项后，存储资源管理器将使用 OS 代理设置。 更具体地说，这将导致使用 Chromium 网络堆栈发出网络调用。 Chromium 网络堆栈的可靠性比存储资源管理器通常使用的 NodeJS 网络堆栈要高得多。 以下摘自 [Chromium 文档](https://www.chromium.org/developers/design-documents/network-settings)的代码片段演示了 Chromium 网络堆栈的各种功能：

> Chromium 网络堆栈使用系统网络设置，使用户和管理员能够轻松控制所有应用程序的网络设置。 网络设置包括：
> - 代理设置
> - SSL/TLS 设置
> - 证书吊销检查设置
> - 证书和私钥存储

如果代理服务器需要凭据，但未在 OS 设置中配置这些凭据，则你需要在存储资源管理器中启用并设置这些凭据。 可从以下任一位置切换凭据的使用：
- “设置”（左侧垂直工具栏上的齿轮图标）>“应用程序” > “代理” > “使用凭据”   。
- "代理设置"对话框（“编辑” > “配置代理”） 。

若要设置凭据，必须转到“代理设置”对话框（“编辑” > “配置代理”） 。

由于并非所有功能目前都支持系统代理，因此，此选项目前以预览版提供。 有关功能的完整列表，请参阅[支持系统代理的功能](#features-that-support-system-proxy)。 启用系统代理后，不支持系统代理的功能不会尝试连接到代理。

如果在将系统代理与支持的功能配合使用时遇到问题，请[在 GitHub 上提出问题](https://github.com/Microsoft/AzureStorageExplorer/issues/new)。

## <a name="proxy-server-authentication"></a>代理服务器身份验证

如果已将存储资源管理器配置为从环境变量或应用代理设置中寻找代理设置，则仅支持使用基本身份验证的代理服务器 。

如果已将存储资源管理器配置为使用系统代理，则支持使用以下任一身份验证方法的代理服务器：
- 基本
- 摘要
- NTLM
- Negotiate

## <a name="which-proxy-source-should-i-choose"></a>我应该选择哪个代理源？

如果你仅使用[支持系统代理的功能](#features-that-support-system-proxy)，则应该先尝试使用[系统代理](#use-system-proxy-preview)。 如果在将系统代理与支持的功能配合使用时遇到问题，请[在 GitHub 上提出问题](https://github.com/Microsoft/AzureStorageExplorer/issues/new)。

如果使用的功能不支持系统代理，则[应用设置](#use-app-proxy-settings)也许是退而求其次的最佳选项。 用于完成代理配置的基于 GUI 的体验有助于减少错误输入代理信息的可能性。 但是，如果已配置代理环境变量，则使用[环境变量](#use-environment-variables)可能更好。

## <a name="azcopy-proxy-usage"></a>AzCopy 代理用法

存储资源管理器将 AzCopy 用于大多数的数据传输操作。 AzCopy 是使用一套不同于存储资源管理器的技术编写的，因此具有一组略微不同的代理功能。

如果存储资源管理器配置为不使用代理或配置为使用系统代理，则AzCopy 会被告知使用其自身的自动检测代理功能，来确定它是否应该以及应该如何向代理发出请求 。 但是，如果已将存储资源管理器配置为从环境变量或应用代理设置中寻找代理设置，则存储资源管理器将告知 AzCopy 使用相同的代理设置 。

如果无论在哪种情况下，你都不希望 AzCopy 使用代理，可以通过切换“设置”（左侧垂直工具栏上的齿轮图标）>“传输” > “AzCopy” > “禁止 AzCopy 使用代理”来禁止使用代理   。

目前，AzCopy 仅支持使用基本身份验证的代理服务器。

## <a name="ssl-certificates"></a>SSL 证书数

默认情况下，存储资源管理器使用 NodeJS 网络堆栈。 NodeJS 随附了预定义的受信任 SSL 证书列表。 某些网络技术（例如代理服务器或防病毒软件）会将自身的 SSL 证书注入到网络流量中。 这些证书通常不会出现在 NodeJS 的证书列表中。 NodeJS 不信任包含此类证书的响应。 当 NodeJS 不信任某个响应时，存储资源管理器将收到错误。

可通过多种做法解决此类错误：
- 使用[系统代理](#use-system-proxy-preview)作为代理源。
- 导入导致出错的 SSL 证书的副本。
- 禁用 SSL 证书。 （不建议）

## <a name="features-that-support-system-proxy"></a>支持系统代理的功能

下面列出了支持系统代理的功能：

- 检查和下载更新
- 列出订阅
- 存储帐户功能
  - 正在列出
- Blob 功能
  - 容器
    - 创建
    - 正在列出
    - 管理存储的访问策略
    - 更改公共访问级别
    - 租用
    - 属性
    - 删除
  - Blob
    - 正在列出
    - 统计信息
    - 未删除
  - ADLS Gen2 Blob
    - 正在列出
    - 统计信息
    - 管理 ACL（仅限查看和修改现有实体）
    - 传播 ACL
    - 移动
    - 重命名
    - 创建文件夹
- 队列功能
  - 队列
    - 创建
    - 正在列出
    - 管理访问策略
    - 属性
    - 删除
    - 清除
  - 队列消息
    - 正在列出
    - 移动
    - 添加
    - 取消排队
- 文件共享功能
  - 文件和文件夹
    - 新建文件夹
    - 属性
- 磁盘功能
  - 列出资源组
  - 列出磁盘
  - 上传磁盘
  - 下载磁盘
  - 复制磁盘
  - 创建快照
  - 删除磁盘

## <a name="next-steps"></a>后续步骤

- [排查代理问题](./storage-explorer-troubleshooting.md#proxy-issues)
- [排查证书问题](./storage-explorer-troubleshooting.md#ssl-certificate-issues)
