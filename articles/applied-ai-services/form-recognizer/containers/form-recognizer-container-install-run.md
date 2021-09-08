---
title: 安装和运行适用于表单识别器 v2.1 的 Docker 容器
titleSuffix: Azure Applied AI Services
description: 使用适用于本地表单识别器的 Docker 容器标识和提取表单和文档中的键值对、选择标记、表和结构。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/01/2021
ms.author: lajanuar
keywords: 本地, Docker, 容器, 标识
ms.openlocfilehash: 700e49f1fb635cc93797c5b39463dc3d76018c7e
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123480959"
---
# <a name="install-and-run-form-recognizer-v21-preview-containers"></a>安装和运行表单识别器 v2.1 预览版容器

> [!IMPORTANT]
>
> * 表单识别器容器为封闭预览版。 若要使用这些容器，必须提交[在线请求](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu)并获得批准。 有关详细信息，请参阅下面的[请求批准运行容器](#request-approval-to-run-the-container)。
>
> * 联机请求表单要求提供一个有效的电子邮件地址，该地址属于拥有 Azure 订阅 ID 的组织，并且用户需要具有或已获得对该订阅的访问权限。

Azure 表单识别器是一种 Azure 应用的 AI 服务，可让用户使用机器学习技术构建自动化数据处理软件。 使用表单识别器，可以从表单文档中识别并提取文本、键值对、选择标记、表数据等内容，并输出包括原始文件中关系的结构化数据。

在本文中，我们将学习如何下载、安装和运行表单识别器容器。 使用容器，可以在自己的环境中运行表单识别器服务。 容器非常适合用于满足特定的安全性和数据管理要求。 表单识别器功能由六个表单识别器功能容器提供支持 - 布局、名片、ID 文档、收据、发票和自定义     （对于收据、名片和 ID 文档容器，还需要读取 OCR 容器）。

## <a name="prerequisites"></a>必备条件

首先，需要一个有效的 [**Azure 帐户**](https://azure.microsoft.com/free/cognitive-services/)。  如果没有帐户，可以 [**创建一个免费帐户**](https://azure.microsoft.com/free/)。

此外，还需要满足以下条件才能使用表单识别器容器：

| 必须 | 目的 |
|----------|---------|
| **熟悉 Docker** | 应对 Docker 概念有基本的了解，例如注册表、存储库、容器和容器映像，以及基本的 `docker` [术语和命令](/dotnet/architecture/microservices/container-docker-introduction/docker-terminology)的知识。 |
| 已安装 Docker 引擎 | <ul><li>需要在[主计算机](#host-computer-requirements)上安装 Docker 引擎。 Docker 提供用于在 [macOS](https://docs.docker.com/docker-for-mac/)、[Windows](https://docs.docker.com/docker-for-windows/) 和 [Linux](https://docs.docker.com/engine/installation/#supported-platforms) 上配置 Docker 环境的包。 有关 Docker 和容器的基础知识，请参阅 [Docker 概述](https://docs.docker.com/engine/docker-overview/)。</li><li> 必须将 Docker 配置为允许容器连接 Azure 并向其发送账单数据。 </li><li> 在 Windows 上，还必须将 Docker 配置为支持 Linux 容器 。</li></ul>  |
|**表单识别器资源** | Azure 门户中的[单服务 Azure 表单识别器](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer)或[多服务认知服务](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)资源。  若要使用这些容器，必须具有关联的 API 密钥和终结点 URI。 这两个值可以从 Azure 门户的表单识别器“密钥和终结点”页获取： <ul><li>{FORM_RECOGNIZER_API_KEY}：两个可用资源密钥之一。<li>{FORM_RECOGNIZER_ENDPOINT_URI}：用于跟踪计费信息的资源的终结点。</li></li></ul>|
| **计算机视觉 API 资源** | 若要处理名片、ID 文档或收据，需要一个计算机视觉资源。 <ul><li>可以访问作为 Azure 资源（REST API 或 SDK）或 **cognitive-services-recognize-text** [容器](../../../cognitive-services/Computer-vision/computer-vision-how-to-install-containers.md#get-the-container-image-with-docker-pull)提供的文本识别功能。 使用此功能需要按普通[费率](#billing)付费。</li> <li>如果使用 cognitive-services-recognize-text 容器，请确保表单识别器容器的计算机视觉密钥是 cognitive-services-recognize-text 容器的计算机视觉 `docker run` 或 `docker compose` 命令中指定的密钥，并且计费终结点是该容器的终结点（例如 `http://localhost:5000`）。 如果在同一台主机上同时使用计算机视觉容器和表单识别器容器，不能使用默认端口 *5000* 将两者同时启动。 </li></ul></br>传入计算机视觉 Azure 云或认知服务容器的 API 密钥和终结点：<ul><li>{COMPUTER_VISION_API_KEY}：两个可用的资源密钥之一。</li><li> {COMPUTER_VISION_ENDPOINT_URI}：用于跟踪计费信息的资源的终结点。</li></ul> |

|可选|目的|
|---------|----------|
|**Azure CLI（命令行接口）** | [Azure CLI](/cli/azure/install-azure-cli) 使你能够使用一组联机命令来创建和管理 Azure 资源。 它可用于在 Windows、macOS 和 Linux 环境中安装，并且可在 Docker 容器和 Azure Cloud Shell 中运行。 |
|||

## <a name="request-approval-to-run-the-container"></a>请求批准运行容器

完成并提交[封闭服务表单申请](https://customervoice.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR7en2Ais5pxKtso_Pz4b1_xUNlpBU1lFSjJUMFhKNzVHUUVLN1NIOEZETiQlQCN0PWcu)，以请求批准运行容器。

通过该表单请求有关你、你的公司以及要使用该容器的用户方案的信息。 提交表单后，Azure 认知服务团队将对其进行审核，并通过电子邮件向你告知决定。

在此表单上，必须使用与 Azure 订阅 ID 关联的电子邮件地址。 用于运行容器的 Azure 资源必须已使用批准的 Azure 订阅 ID 创建。 请检查你的电子邮件（“收件箱”和“垃圾邮件”文件夹）以获取来自 Microsoft 的应用程序状态更新。 获得批准后，便可以根据本文稍后所述从 Microsoft 容器注册表 (MCR) 下载该容器，然后运行该容器。

## <a name="host-computer-requirements"></a>主机计算机要求

主机是运行 Docker 容器且基于 x64 的计算机。 它可以是本地计算机或 Azure 中的 Docker 托管服务，例如：

* [Azure Kubernetes 服务](../../../aks/index.yml)。
* [Azure 容器实例](../../../container-instances/index.yml)。
* 部署到 [Azure Stack](/azure-stack/operator) 的 [Kubernetes](https://kubernetes.io/) 群集。 有关详细信息，请参阅[将 Kubernetes 部署到 Azure Stack](/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)。

### <a name="container-requirements-and-recommendations"></a>容器要求和建议

#### <a name="required-containers"></a>必需容器

下表列出了每个下载的表单识别器容器的其他支持容器。 有关详细信息，请参阅[计费](#billing)部分。

| 功能容器 | 支持容器 |
|---------|-----------|
| **布局** | 无 |
| 名片 | 计算机视觉读取|
| ID 文档 | 计算机视觉读取 |
| **发票**   | **布局** |
| **回执** |计算机视觉读取 |
| **自定义** | 自定义 API、自定义监督、布局|

#### <a name="recommended-cpu-cores-and-memory"></a>建议的 CPU 内核和内存

> [!Note]
> 最小值和建议值基于 Docker 限制，而不是基于主机资源。

##### <a name="read-layout-and-prebuilt-containers"></a>读取、布局和预生成的容器

| 容器 | 最小值 | 建议 |
|-----------|---------|-------------|
| Read 3.2 | 8 个内核，16 GB 内存 | 8 个内核，24 GB 内存|
| 布局 2.1- 预览版 | 8 个内核，16 GB 内存 | 8 个内核，24 GB 内存 |
| 名片 2.1 - 预览版 | 2 个内核，4 GB 内存 | 4 个内核，4 GB 内存 |
| ID 文档 2.1 - 预览版 | 单核，2-GB 内存 |2 个内核，2 GB 内存 |
| 发票 2.1 - 预览版 | 4 个内核，8 GB 内存 | 8 个内核，8 GB 内存 |
| 收据 2.1 - 预览版 |  4 个内核，8 GB 内存 | 8 个内核，8 GB 内存  |

##### <a name="custom-containers"></a>自定义容器

以下主机计算机要求适用于训练和分析请求：

| 容器 | 最小值 | 建议 |
|-----------|---------|-------------|
| 自定义的 API| 0.5 个内核，0.5 GB 内存| 1 个内核，1 GB 内存 |
|自定义监督 | 4 个内核，2 GB 内存 | 8 个内核，4 GB 内存|

如果只是进行分析调用，则主机计算机要求如下所示：

| 容器 | 最小值 | 建议 |
|-----------|---------|-------------|
|自定义监督（分析） | 1 个内核，0.5 GB | 2 个内核，1 GB 内存 |

* 每个核心必须至少为 2.6 千兆赫 (GHz) 或更快。
* 核心和内存对应于 `--cpus` 和 `--memory` 设置，用作 `docker compose` 或 `docker run` 命令的一部分。

> [!TIP]
> 可以使用 [docker images](https://docs.docker.com/engine/reference/commandline/images/) 命令列出下载的容器映像。 例如，以下命令以表格列出每个下载的容器映像的 ID、存储库和标记：
>
>  ```docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>
>  IMAGE ID         REPOSITORY                TAG
>  <image-id>       <repository-path/name>    <tag-name>
>  ```

## <a name="run-the-container-with-the-docker-compose-up-command"></a>通过 docker-compose up 命令运行容器

* 将 {ENDPOINT_URI} 和 {API_KEY} 值替换为“Azure 资源”页中的资源终结点 URI 和 API 密钥。

   :::image type="content" source="../media/containers/keys-and-endpoint.png" alt-text="屏幕截图：Azure 门户的“密钥和终结点”页面。":::

* 确保 EULA 值设置为“接受”。

* 必须指定 `EULA`、`Billing` 和 `ApiKey` 值，否则容器将无法启动。

> [!IMPORTANT]
> 订阅密钥用于访问表单识别器资源。 不要共享你的密钥。 以安全方式存储密钥（例如，使用 Azure Key Vault 来存储）。 此外，我们建议定期重新生成这些密钥。 发出 API 调用只需一个密钥。 重新生成第一个密钥时，可以使用第二个密钥来持续访问服务。

### <a name="layout"></a>[布局](#tab/layout)

下面是一个运行表单识别器布局容器的自包含 `docker compose` 示例。  利用 `docker compose`，可以使用 YAML 文件来配置应用程序服务。 然后，通过 `docker-compose up` 命令基于配置创建并启动所有服务。 为布局容器实例输入 {FORM_RECOGNIZER_ENDPOINT_URI} 和 {{FORM_RECOGNIZER_API_KEY} 值。

```yml
version: "3.9"
services:
azure-cognitive-service-layout:
    container_name: azure-cognitive-service-layout
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
    ports:
      - "5000"
    networks:
      - ocrvnet
networks:
  ocrvnet:
    driver: bridge
```

现在，则可以通过 [docker compose](https://docs.docker.com/compose/) 命令启动该服务：

```bash
docker-compose up
```

### <a name="business-card"></a>[名片](#tab/business-card)

下面是一个同时运行表单识别器名片和读取容器的自包含 `docker compose` 示例。 利用 `docker compose`，可以使用 YAML 文件来配置应用程序服务。 然后，通过 `docker-compose up` 命令基于配置创建并启动所有服务。 为名片容器实例输入 {FORM_RECOGNIZER_ENDPOINT_URI} 和 {FORM_RECOGNIZER_API_KEY} 值。 为计算机视觉读取容器输入 {COMPUTER_VISION_ENDPOINT_URI} 和 {COMPUTER_VISION_API_KEY}。

```yml
version: "3.9"
services:
  azure-cognitive-service-businesscard:
    container_name: azure-cognitive-service-businesscard
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/businesscard
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
      - AzureCognitiveServiceReadHost=http://azure-cognitive-service-read:5000
    ports:
      - "5000:5050"
    networks:
      - ocrvnet
  azure-cognitive-service-read:
    container_name: azure-cognitive-service-read
    image: mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
    environment:
      - EULA=accept
      - billing={COMPUTER_VISION_ENDPOINT_URI}
      - apikey={COMPUTER_VISION_API_KEY}
    networks:
      - ocrvnet

networks:
  ocrvnet:
    driver: bridge
```

现在，则可以通过 [docker compose](https://docs.docker.com/compose/) 命令启动该服务：

```bash
docker-compose up
```

### <a name="id-document"></a>[ID 文档](#tab/id-document)

下面是一个同时运行表单识别器 ID 文档和读取容器的自包含 `docker compose` 示例。 利用 `docker compose`，可以使用 YAML 文件来配置应用程序服务。 然后，通过 `docker-compose up` 命令基于配置创建并启动所有服务。 为 ID 文档容器输入 {FORM_RECOGNIZER_ENDPOINT_URI} 和 {FORM_RECOGNIZER_API_KEY} 值。 为计算机视觉读取容器输入 {COMPUTER_VISION_ENDPOINT_URI} 和 {COMPUTER_VISION_API_KEY} 值。

```yml
version: "3.9"
services:
  azure-cognitive-service-id:
    container_name: azure-cognitive-service-id
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/id-document
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
      - AzureCognitiveServiceReadHost=http://azure-cognitive-service-read:5000
    ports:
      - "5000:5050"
    networks:
      - ocrvnet
  azure-cognitive-service-read:
    container_name: azure-cognitive-service-read
    image: mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
    environment:
      - EULA=accept
      - billing={COMPUTER_VISION_ENDPOINT_URI}
      - apikey={COMPUTER_VISION_API_KEY}
    networks:
      - ocrvnet

networks:
  ocrvnet:
    driver: bridge
```

现在，则可以通过 [docker compose](https://docs.docker.com/compose/) 命令启动该服务：

```bash
docker-compose up
```

### <a name="invoice"></a>[发票](#tab/invoice)

下面是一个同时运行表单识别器发票和布局容器的自包含 `docker compose` 示例。 利用 `docker compose`，可以使用 YAML 文件来配置应用程序服务。 然后，通过 `docker-compose up` 命令基于配置创建并启动所有服务。  为发票和布局容器输入 {FORM_RECOGNIZER_ENDPOINT_URI} 和 {FORM_RECOGNIZER_API_KEY} 值。

```yml
version: "3.9"
services:
  azure-cognitive-service-invoice:
    container_name: azure-cognitive-service-invoice
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/invoice
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
      - AzureCognitiveServiceLayoutHost=http://azure-cognitive-service-layout:5000
    ports:
      - "5000:5050"
    networks:
      - ocrvnet
  azure-cognitive-service-layout:
    container_name: azure-cognitive-service-layout
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout
    user: root
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
    networks:
      - ocrvnet

networks:
  ocrvnet:
    driver: bridge
```

现在，则可以通过 [docker compose](https://docs.docker.com/compose/) 命令启动该服务：

```bash
docker-compose up
```

### <a name="receipt"></a>[回执](#tab/receipt)

下面是一个同时运行表单识别器收据和读取容器的自包含 `docker compose` 示例。 利用 `docker compose`，可以使用 YAML 文件来配置应用程序服务。 然后，通过 `docker-compose up` 命令基于配置创建并启动所有服务。 为收据容器输入 {FORM_RECOGNIZER_ENDPOINT_URI} 和 {FORM_RECOGNIZER_API_KEY} 值。 为计算机视觉读取容器输入 {COMPUTER_VISION_ENDPOINT_URI} 和 {COMPUTER_VISION_API_KEY} 值。

```yml
version: "3.9"
services:
  azure-cognitive-service-receipt:
    container_name: azure-cognitive-service-receipt
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/receipt
    environment:
      - EULA=accept
      - billing={FORM_RECOGNIZER_ENDPOINT_URI}
      - apikey={FORM_RECOGNIZER_API_KEY}
      - AzureCognitiveServiceReadHost=http://azure-cognitive-service-read:5000
    ports:
      - "5000:5050"
    networks:
      - ocrvnet
  azure-cognitive-service-read:
    container_name: azure-cognitive-service-read
    image: mcr.microsoft.com/azure-cognitive-services/vision/read:3.2
    environment:
      - EULA=accept
       - billing={COMPUTER_VISION_ENDPOINT_URI}
      - apikey={COMPUTER_VISION_API_KEY}
    networks:
      - ocrvnet

networks:
  ocrvnet:
    driver: bridge
```

现在，则可以通过 [docker compose](https://docs.docker.com/compose/) 命令启动该服务：

```bash
docker-compose up
```

### <a name="custom"></a>[自定义](#tab/custom)

除上面提到的[先决条件](#prerequisites)之外，还需要执行以下操作来处理自定义文档：

####  <a name="bullet-create-a-folder-to-store-the-following-files"></a>&bullet; 创建一个文件夹，用于存储以下文件：

  1. [.env](#-create-an-environment-file)
  1. [**nginx.conf**](#-create-a-nginx-file)
  1. [**docker-compose.yml**](#-create-a-docker-compose-file)

#### <a name="bullet-create-a-folder-to-store-your-input-data"></a>&bullet; 创建一个文件夹，用于存储输入数据

  1. 将此文件夹命名为 shared。
  1. 我们将以 {SHARED_MOUNT_PATH} 形式引用此文件夹的文件路径。
  1. 将文件路径复制到一个方便的位置，例如 Microsoft 记事本。 需要将其添加到下面的 .env 文件中。

#### <a name="bullet-create-a-folder-to-store-the-logs--written-by-the-form-recognizer-service-on-your-local-machine"></a>&bullet; 在本地计算机上创建一个文件夹，用于存储由表单识别器服务写入的日志。

  1. 将此文件夹命名为 output。
  1. 我们将以 {OUTPUT_MOUNT_PATH} 形式引用此文件夹的文件路径。
  1. 将文件路径复制到一个方便的位置，例如 Microsoft 记事本。 需要将其添加到下面的 .env 文件中。

#### <a name="bullet-create-an-environment-file"></a>&bullet; 创建环境文件

  1. 将此文件命名为 .env。

  1. 声明以下环境变量：

  ```text
  SHARED_MOUNT_PATH="<file-path-to-shared-folder>"
  OUTPUT_MOUNT_PATH="<file -path-to-output-folder>"
  FORM_RECOGNIZER_ENDPOINT_URI="<your-form-recognizer-endpoint>"
  FORM_RECOGNIZER_API_KEY="<your-form-recognizer-apiKey>"
  RABBITMQ_HOSTNAME="rabbitmq"
  RABBITMQ_PORT=5672
  NGINX_CONF_FILE="<file-path>"
  ```

#### <a name="bullet-create-a-nginx-file"></a>&bullet; 创建 nginx 文件

  1. 将此文件命名为 nginx.conf。

  1. 输入以下配置：

```text
worker_processes 1;

events { worker_connections 1024; }

http {

    sendfile on;

    upstream docker-api {
        server azure-cognitive-service-custom-api:5000;
    }

    upstream docker-layout {
        server  azure-cognitive-service-layout:5000;
    }

    server {
        listen 5000;

        location = / {
            proxy_pass         http://docker-api/;

        }

        location /status {
            proxy_pass         http://docker-api/status;

        }

        location /ready {
            proxy_pass         http://docker-api/ready;

        }

        location /swagger {
            proxy_pass         http://docker-api/swagger;

        }

        location /formrecognizer/v2.1/custom/ {
            proxy_pass         http://docker-api/formrecognizer/v2.1/custom/;

        }

        location /formrecognizer/v2.1/layout/ {
            proxy_pass         http://docker-layout/formrecognizer/v2.1/layout/;

        }
    }
}
```

* 至少收集一组六个相同类型的表单。 你将使用此数据训练模型并测试表单。 可使用[示例数据集](https://go.microsoft.com/fwlink/?linkid=2090451)（下载并提取 sample_data.zip）。 将训练文件下载到上面创建的 shared 文件夹中。

* 如果要标记数据，请下载[适用于 Windows 的表单识别器示例标记工具](https://github.com/microsoft/OCR-Form-Tools/releases/tag/v2.1-ga)。 下载将导入标记工具 .exe 文件，该文件用于对本地文件系统上存在的数据进行标记。 可以忽略在下载过程中出现的任何警告。

#### <a name="create-a-new-sample-labeling-tool-project"></a>创建新的示例标记工具项目

* 双击示例标记工具 .exe 文件，打开该标记工具。
* 在工具的左窗格中，选择“连接”选项卡。
* 选择“创建新项目”，并指定其名称和说明。
* 对于提供程序，选择“本地文件系统”选项。 对于本地文件夹，请确保输入指向存储示例数据文件的文件夹的路径。
* 导航回“主页”选项卡，然后选择“使用自定义训练具有标签和键值对的模型选项”。
* 选择左窗格中的“训练”按钮以训练标记的模型。
* 保存此连接，并使用它来标记请求。
* 可以选择根据训练的模型来分析所选的文件。

#### <a name="bullet-create-a-docker-compose-file"></a>&bullet; 创建 docker 编译文件

1. 将此文件命名为 docker-compose.yml

2. 下面是一个同时运行表单识别器布局、标签工具、自定义 API 和自定义受监督容器的自包含 `docker compose` 示例。 利用 `docker compose`，可以使用 YAML 文件来配置应用程序服务。 然后，通过 `docker-compose up` 命令基于配置创建并启动所有服务。

  ```yml
  version: '3.3'
  services:
   nginx:
    image: nginx:alpine
    container_name: reverseproxy
    volumes:
      - ${NGINX_CONF_FILE}:/etc/nginx/nginx.conf
    ports:
      - "5000:5000"
   rabbitmq:
    container_name: ${RABBITMQ_HOSTNAME}
    image: rabbitmq:3
    expose:
      - "5672"
   layout:
    container_name: azure-cognitive-service-layout
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/layout
    depends_on:
      - rabbitmq
    environment:
      eula: accept
      apikey: ${FORM_RECOGNIZER_API_KEY}
      billing: ${FORM_RECOGNIZER_ENDPOINT_URI}
      Queue:RabbitMQ:HostName: ${RABBITMQ_HOSTNAME}
      Queue:RabbitMQ:Port: ${RABBITMQ_PORT}
      Logging:Console:LogLevel:Default: Information
      SharedRootFolder: /shared
      Mounts:Shared: /shared
      Mounts:Output: /logs
    volumes:
      - type: bind
        source: ${SHARED_MOUNT_PATH}
        target: /shared
      - type: bind
        source: ${OUTPUT_MOUNT_PATH}
        target: /logs
    expose:
      - "5000"

   custom-api:
    container_name: azure-cognitive-service-custom-api
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/custom-api
    restart: always
    depends_on:
      - rabbitmq
    environment:
      eula: accept
      apikey: ${FORM_RECOGNIZER_API_KEY}
      billing: ${FORM_RECOGNIZER_ENDPOINT_URI}
      Logging:Console:LogLevel:Default: Information
      Queue:RabbitMQ:HostName: ${RABBITMQ_HOSTNAME}
      Queue:RabbitMQ:Port: ${RABBITMQ_PORT}
      SharedRootFolder: /shared
      Mounts:Shared: /shared
      Mounts:Output: /logs
    volumes:
      - type: bind
        source: ${SHARED_MOUNT_PATH}
        target: /shared
      - type: bind
        source: ${OUTPUT_MOUNT_PATH}
        target: /logs
    expose:
      - "5000"

   custom-supervised:
    container_name: azure-cognitive-service-custom-supervised
    image: mcr.microsoft.com/azure-cognitive-services/form-recognizer/custom-supervised
    restart: always
    depends_on:
      - rabbitmq
    environment:
      eula: accept
      apikey: ${FORM_RECOGNIZER_API_KEY}
      billing: ${FORM_RECOGNIZER_ENDPOINT_URI}
      CustomFormRecognizer:ContainerPhase: All
      CustomFormRecognizer:LayoutAnalyzeUri: http://azure-cognitive-service-layout:5000/formrecognizer/v2.1/layout/analyze
      Logging:Console:LogLevel:Default: Information
      Queue:RabbitMQ:HostName: ${RABBITMQ_HOSTNAME}
      Queue:RabbitMQ:Port: ${RABBITMQ_PORT}
      SharedRootFolder: /shared
      Mounts:Shared: /shared
      Mounts:Output: /logs
    volumes:
      - type: bind
        source: ${SHARED_MOUNT_PATH}
        target: /shared
      - type: bind
        source: ${OUTPUT_MOUNT_PATH}
        target: /logs
  ```

### <a name="ensure-the-service-is-running"></a>确保该服务正在运行

确保该服务已启动并正在运行。 在 Ubuntu shell 中运行这些命令。

```bash
$cd <folder containing the docker-compose file>

$source .env

$docker-compose up
```

### <a name="create-a-new-connection"></a>创建新连接

* 在工具的左窗格中，选择“连接”选项卡。
* 选择“创建新项目”，并指定其名称和说明。
* 对于提供程序，选择“本地文件系统”选项。 对于本地文件夹，请确保输入指向存储示例数据文件的文件夹的路径。
* 导航回“主页”选项卡，然后选择“使用自定义训练具有标签和键值对的模型”选项。
* 选择左窗格中的“训练”按钮以训练标记的模型。
* 保存此连接，并使用它来标记请求。
* 可以选择根据训练的模型来分析所选的文件。

---

## <a name="validate-that-the-service-is-running"></a>验证该服务是否正在运行

有几种方法可用于验证容器是否正在运行：

* 容器在 `\` 处提供主页作为容器正在运行的视觉验证。

* 可以打开你常用的 Web 浏览器并导航到相关容器的外部 IP 地址和公开端口。 使用以下各种请求 URL 验证容器是否正在运行。 下面列出的示例请求 URL 是 `http://localhost:5000`，但是你的特定容器可能会有所不同。 请记住，你要导航至容器的外部 IP 地址和公开端口。

  请求 URL    | 用途
  ----------- | --------
  |http://<span></span>localhost:5000/ | 容器提供主页。
  |http://<span></span>localhost:5000/ready     | 使用 GET 对此 URL 进行请求，可以验证容器是否已准备好接受针对模型的查询。 此请求可用于 Kubernetes 运行情况和就绪情况探测。
  |http://<span></span>localhost:5000/status | 使用 GET 对此 URL 进行请求，可以验证用于启动容器的 api-key 是否有效，而不会导致终结点查询。 此请求可用于 Kubernetes 运行情况和就绪情况探测。
  |http://<span></span>localhost:5000/swagger | 容器为终结点提供一组完整的文档以及“尝试”功能。 使用此功能可以将设置输入到基于 Web 的 HTML 表单并进行查询，而无需编写任何代码。 查询返回后，将提供示例 CURL 命令，用于演示所需的 HTTP 标头和正文格式。
  |

:::image type="content" source="../media/containers/container-webpage.png" alt-text="屏幕截图：Azure 容器欢迎页。":::

## <a name="stop-the-containers"></a>停止容器

若要停止容器，请使用以下命令：

```console
docker-compose down
```

## <a name="billing"></a>计费

表单识别器容器使用 Azure 帐户中的“表单识别器”资源向 Azure 发送账单信息。

对该容器的查询在用于 `ApiKey` 的 Azure 资源的定价层计费。 将针对用于处理文档和映像的每个容器实例计费。 因此，如果使用名片功能，将针对表单识别器 `BusinessCard` 和 `Compuer Vision Read` 容器实例计费。 对于发票功能，将针对表单识别器 `Invoice` 和 `Layout` 容器实例计费。 请参阅[表单识别器](https://azure.microsoft.com/pricing/details/form-recognizer/)和计算机视觉[读取功能](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/)容器的定价。

Azure 认知服务容器在未连接到计量/计费终结点的情况下无权运行。 必须始终让容器可以向计费终结点传送计费信息。 认知服务容器不会将客户数据（例如，正在分析的图像或文本）发送给 Microsoft。

### <a name="connect-to-azure"></a>连接到 Azure

容器需要计费参数值才能运行。 这些值使容器可以连接到计费终结点。 容器约每 10 到 15 分钟报告一次使用情况。 如果容器未在允许的时间范围内连接到 Azure，容器将继续运行，但不会为查询提供服务，直到计费终结点恢复。 尝试连接按 10 到 15 分钟的相同时间间隔进行 10 次。 如果无法在 10 次尝试内连接到计费终结点，容器会停止处理请求。 有关发送给 Microsoft 进行计费的信息的示例，请参阅[认知服务容器常见问题解答](../../../cognitive-services/containers/container-faq.yml#how-does-billing-work)。

### <a name="billing-arguments"></a>计费参数

当以下三个选项都提供了有效值时，[docker-compose up](https://docs.docker.com/engine/reference/commandline/compose_up/) 命令将启动容器：

| 选项 | 说明 |
|--------|-------------|
| `ApiKey` | 用于跟踪计费信息的认知服务资源的 API 密钥。<br/>必须将此选项的值设置为 `Billing` 中指定的已预配资源的 API 密钥。 |
| `Billing` | 用于跟踪计费信息的认知服务资源的终结点。<br/>必须将此选项的值设置为已预配的 Azure 资源的终结点 URI。|
| `Eula` | 表示已接受容器的许可条款。<br/>此选项的值必须设置为 **accept**。 |

有关这些选项的详细信息，请参阅[配置容器](form-recognizer-container-configuration.md)。

## <a name="summary"></a>摘要

就这么简单！ 在本文中，我们学习了下载、安装和运行表单识别器容器的概念与工作流。 综上所述：

* 表单识别器提供七个适用于 Docker 的 Linux 容器。
* 容器映像可从 mcr 下载。
* 容器映像在 Docker 中运行。
* 必须在实例化容器时指定账单信息。

> [!IMPORTANT]
> 如果未连接到 Azure 进行计量，则无法授权并运行认知服务容器。 客户需要始终让容器向计量服务传送账单信息。 认知服务容器不会将客户数据（例如，正在分析的图像或文本）发送给 Microsoft。

## <a name="next-steps"></a>后续步骤

* [表单识别器容器配置设置](form-recognizer-container-configuration.md) 