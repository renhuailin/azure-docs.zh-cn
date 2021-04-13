---
title: 如何部署表单识别器示例标记工具
titleSuffix: Azure Cognitive Services
description: 了解部署表单识别器示例标记工具的不同方法，以帮助进行监督式学习。
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 02/11/2021
ms.author: lajanuar
ms.openlocfilehash: 0f5f0714235ee23624b3a199eac744155d2bbdd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "101093387"
---
# <a name="deploy-the-sample-labeling-tool"></a>部署示例标记工具

表单识别器示例标记工具是一款应用程序，它提供一个简单的用户界面 (UI)，可用来手动标记表单（文档）以便进行监督式学习。 在本文中，我们将提供链接和说明来指导你执行以下操作：

* [本地运行示例标记工具](#run-the-sample-labeling-tool-locally)
* [将示例标记工具部署到 Azure 容器实例 (ACI)](#deploy-with-azure-container-instances-aci)
* [使用并参与开源 OCR 表单标记工具](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>本地运行示例标记工具

开始标记数据的最快方法是在本地运行示例标记工具。 以下快速入门结合使用表单识别器 REST API 和示例标记工具，通过手动标记的数据来训练自定义模型。 

* [快速入门：使用示例标记工具标记表单、训练模型和分析表单](./quickstarts/label-tool.md)。

## <a name="deploy-with-azure-container-instances-aci"></a>使用 Azure 容器实例 (ACI) 部署

开始之前，请务必注意，可以通过两种方法将示例标记工具部署到 Azure 容器实例 (ACI)。 这两个选项旨在通过 ACI 运行示例标记工具：

* [使用 Azure 门户](#azure-portal)
* [使用 Azure CLI](#azure-cli)

### <a name="azure-portal"></a>Azure 门户

请按照下列步骤使用 Azure 门户创建新资源： 

1. 登录 [Azure 门户](https://portal.azure.com/signin/index/)。
2. 选择“创建资源”。 
3. 接下来，选择“Web 应用”。

   > [!div class="mx-imgBorder"]
   > ![选择“Web 应用”](./media/quickstarts/create-web-app.png)

4. 首先，确保已选择“基本信息”选项卡。 接下来，需要提供一些信息：

   > [!div class="mx-imgBorder"]
   > ![选择“基本信息”](./media/quickstarts/select-basics.png)
   * 订阅：选择现有的 Azure 订阅
   * 资源组 - 可以重复使用现有资源组，也可以为此项目创建一个新资源组。 建议创建新的资源组。
   * 名称 - 为 Web 应用提供一个名称。 
   * 发布 - 选择“Docker 容器”
   * 操作系统 - 选择“Linux”
   * 区域 - 选择适合你的区域。
   * Linux 计划 - 为应用服务选择定价层/计划。 

   > [!div class="mx-imgBorder"]
   > ![配置 Web 应用](./media/quickstarts/select-docker.png)

5. 接下来，选择“Docker”选项卡。

   > [!div class="mx-imgBorder"]
   > ![选择“Docker”](./media/quickstarts/select-docker.png)

6. 现在，让我们来配置 Docker 容器。 除非另有说明，否则所有字段都是必填字段：
<!-- markdownlint-disable MD025 -->
# <a name="v21-preview"></a>[v2.1 预览版](#tab/v2-1)

* 选项 - 选择“单个容器”
* 图像源 - 选择“专用注册表” 
* 服务器 URL - 将此项设置为 `https://mcr.microsoft.com`
* 用户名（可选）- 创建用户名。 
* 密码（可选）- 创建可记住的安全密码。
* 图像和标记 - 将此项设置为 `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview`
* 持续部署 - 如果想要在开发团队对示例标记工具进行更改时接收自动更新，请将此项设置为“开启”。
* 启动命令 - 将此项设置为 `./run.sh eula=accept`

# <a name="v20"></a>[v2.0](#tab/v2-0)  

* 选项 - 选择“单个容器”
* 图像源 - 选择“专用注册表” 
* 服务器 URL - 将此项设置为 `https://mcr.microsoft.com`
* 用户名（可选）- 创建用户名。 
* 密码（可选）- 创建可记住的安全密码。
* 图像和标记 - 将此项设置为 `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest`
* 持续部署 - 如果想要在开发团队对示例标记工具进行更改时接收自动更新，请将此项设置为“开启”。
* 启动命令 - 将此项设置为 `./run.sh eula=accept`

 ---

   > [!div class="mx-imgBorder"]
   > ![配置 Docker](./media/quickstarts/configure-docker.png)

7. 就这么简单。 接下来，依次选择“查看 + 创建”，然后单击“创建”以部署 Web 应用。 完成后，你可以通过资源“概述”中提供的 URL 访问该 Web 应用。

> [!NOTE]
> 创建 Web 应用时，还可以配置授权/身份验证。 这并不是必需的。

> [!IMPORTANT]
> 可能需要为 Web 应用启用 TLS，才能通过其 `https` 地址查看它。 请按照 [启用 TLS 终结点](../../container-instances/container-instances-container-group-ssl.md)中的说明设置 Sidercar 容器，而不是为 Web 应用启用 TLS/SSL。
<!-- markdownlint-disable MD001 -->
### <a name="azure-cli"></a>Azure CLI

除使用 Azure 门户之外，还可以使用 Azure CLI 创建资源。 继续之前，需要安装 [Azure CLI](/cli/azure/install-azure-cli)。 如果已在使用 Azure CLI，则可以跳过此步骤。 

有关此命令，需要了解以下几个事项：

* `DNS_NAME_LABEL=aci-demo-$RANDOM` 会生成一个随机 DNS 名称。 
* 此示例假设您有一个可用于创建资源的资源组。 将 `<resource_group_name>` 替换为与订阅关联的有效资源组。 
* 你需要指定要在哪里创建资源。 将 `<region name>` 替换为所需的 Web 应用区域。
* 此命令自动接受 EULA。

在 Azure CLI 中运行以下命令，可为示例标记工具创建 Web 应用资源：

<!-- markdownlint-disable MD024 -->
# <a name="v21-preview"></a>[v2.1 预览版](#tab/v2-1)

```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-preview \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"

```

# <a name="v20"></a>[v2.0](#tab/v2-0)


```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"
``` 


---

### <a name="connect-to-azure-ad-for-authorization"></a>连接到 Azure AD 进行授权

建议将 Web 应用连接到 Azure Active Directory。 这样，可确保只有具有有效凭据的用户才能登录和使用你的 Web 应用。 请按照 [配置应用服务应用](../../app-service/configure-authentication-provider-aad.md)中的说明，将其连接到 Azure Active Directory。

## <a name="open-source-on-github"></a>GitHub 上的开源

OCR 表单标记工具也可用作 GitHub 上的开源项目。 该工具是使用 React + Redux 生成并采用 TypeScript 编写的 web 应用程序。 若要了解详细信息或参与，请参阅 [OCR 窗体标记工具](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)。

## <a name="next-steps"></a>后续步骤

使用[使用标签进行训练](./quickstarts/label-tool.md)快速入门，了解如何使用该工具手动标记训练数据和执行监督式学习。