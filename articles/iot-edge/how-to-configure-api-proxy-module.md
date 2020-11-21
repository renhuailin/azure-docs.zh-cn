---
title: 配置 API 代理模块-Azure IoT Edge |Microsoft Docs
description: 了解如何为 IoT Edge 网关层次结构自定义 API 代理模块。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
monikerRange: '>=iotedge-2020-11'
ms.openlocfilehash: 894398d63e326db3c6ee9de9bebc426a6e621600
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95024664"
---
# <a name="configure-the-api-proxy-module-for-your-gateway-hierarchy-scenario-preview"></a>为网关层次结构方案配置 API 代理模块 (预览) 

API 代理模块允许 IoT Edge 设备通过网关发送 HTTP 请求，而不是直接连接到云服务。 本文将逐步讲解配置选项，以便您可以自定义该模块以支持您的网关层次结构要求。

>[!NOTE]
>此功能要求运行 Linux 容器的 IoT Edge 1.2 版本，该版本为公共预览版。

在某些网络体系结构中，网关后 IoT Edge 设备不能直接访问云。 尝试连接到云服务的任何模块都将失败。 API 代理模块支持此配置中的下游 IoT Edge 设备，方法是将模块连接重新路由到网关层次结构的各层。 它提供一种安全的方法，使客户端无需隧道即可通过 HTTPS 与多个服务通信，而是通过在每个层终止连接。 API 代理模块在网关层次结构中的 IoT Edge 设备之间充当代理模块，直到它们到达顶层的 IoT Edge 设备。 此时，在顶层运行的服务 IoT Edge 设备处理这些请求，API 代理模块通过单个端口将来自本地服务的所有 HTTP 流量代理到云中。

API 代理模块可以为网关层次结构启用多种方案，包括允许较低层的设备将容器映像或推送 blob 提取到存储。 代理规则的配置定义数据的路由方式。 本文讨论了几种常见的配置选项。

## <a name="deploy-the-proxy-module"></a>部署代理模块

API 代理模块可从 Microsoft 容器注册表 (MCR) ： `mcr.microsoft.com/azureiotedge-api-proxy:latest` 。

还可以直接从 Azure Marketplace 部署 API 代理模块： [IOT EDGE Api 代理](https://azuremarketplace.microsoft.com/marketplace/apps/azure-iot.azureiotedge-api-proxy?tab=Overview)。

## <a name="understand-the-proxy-module"></a>了解代理模块

API 代理模块利用 nginx 反向代理通过网络层路由数据。 代理嵌入在模块中，这意味着模块映像需要支持代理配置。 例如，如果代理正在侦听某个端口，则该模块需要打开该端口。

代理以模块中嵌入的默认配置文件开头。 你可以 [使用模块克隆](../iot-hub/iot-hub-devguide-module-twins.md)从云中将新配置传递到模块。 此外，还可以使用环境变量在部署时启用或禁用配置设置。

本文首先重点介绍默认的配置文件，以及如何使用环境变量来启用其设置。 接下来，我们讨论如何自定义配置文件。

### <a name="default-configuration"></a>默认配置

API 代理模块附带了支持常见方案并允许自定义的默认配置。 可以通过模块的环境变量控制默认配置。

当前，默认环境变量包括：

| 环境变量 | 说明 |
| -------------------- | ----------- |
| `PROXY_CONFIG_ENV_VAR_LIST` | 列出要在逗号分隔的列表中更新的所有变量。 此步骤可防止意外修改错误的配置设置。
| `NGINX_DEFAULT_PORT` | 更改 nginx 代理侦听的端口。 如果更新此环境变量，请确保所选端口也在模块 dockerfile 中公开，并在部署清单中声明为端口绑定。<br><br>默认值为443。<br><br>从 Azure Marketplace 部署时，默认端口将更新为8000，以防止与 edgeHub 模块冲突。 有关详细信息，请参阅 [最小化打开的端口](#minimize-open-ports)。 |
| `DOCKER_REQUEST_ROUTE_ADDRESS` | 用于路由 docker 请求的地址。 将顶层设备上的此变量修改为指向注册表模块。<br><br>默认值为父主机名。 |
| `BLOB_UPLOAD_ROUTE_ADDRESS` | 用于路由 blob 注册表请求的地址。 将顶层设备上的此变量修改为指向 blob 存储模块。<br><br>默认值为父主机名。 |

## <a name="minimize-open-ports"></a>最小化打开的端口

为了最大限度地减少打开端口的数量，API 代理模块应将所有 HTTPS 流量中继 (端口 443) ，包括面向 edgeHub 模块的流量。 默认情况下，API 代理模块已配置为重新路由端口443上的所有 edgeHub 通信。

使用以下步骤来配置部署，以最大程度地减少打开的端口：

1. 将 edgeHub 模块设置更新为不在端口443上绑定，否则将出现端口绑定冲突。 默认情况下，edgeHub 模块绑定到端口443、5671和8883。 删除端口443绑定，并将另两个绑定保留原样：

   ```json
   {
     "HostConfig": {
       "PortBindings": {
         "5671/tcp": [
           {
             "HostPort": "5671"
           }
         ],
         "8883/tcp": [
           {
             "HostPort": "8883"
           }
         ]
       }
     }
   }
   ```

1. 配置 API 代理模块以绑定到端口443。

   1. 将 **NGINX_DEFAULT_PORT** 环境变量的值设置为 `443` 。
   1. 更新要在端口443上绑定的容器创建选项。

      ```json
      {
        "HostConfig": {
          "PortBindings": {
            "443/tcp": [
              {
                "HostPort": "443"
              }
            ]
          }
        }
      }
      ```

如果不需要最小化打开的端口，则可以让 edgeHub 模块使用端口443，并将 API 代理模块配置为侦听另一个端口。 例如，API 代理模块可以通过将 **NGINX_DEFAULT_PORT** 环境变量的值设置为 `8000` 并为端口8000创建端口绑定来侦听端口8000。

## <a name="enable-container-image-download"></a>启用容器映像下载

API 代理模块的一个常见用例是在较低层中启用 IoT Edge 设备来请求容器映像。 此方案使用 [Docker 注册表模块](https://hub.docker.com/_/registry) 从云中检索容器映像，并将它们缓存在顶部。 API 代理中继所有 HTTPS 请求，从较低层下载容器映像，以供顶层中的注册表模块提供服务。

此方案要求下游 IoT Edge 设备指向域名， `$upstream` 后跟 API 代理模块端口号，而不是映像的容器注册表。 例如：`$upstream:8000/azureiotedge-api-proxy:1.0`。

本教程演示如何 [使用网关创建 IoT Edge 设备的层次结构](tutorial-nested-iot-edge.md)。

在 **顶部层** 配置以下模块：

* Docker 注册表模块
  * 使用便于记忆的名称（如 *注册表* ）配置模块，并在模块中公开端口以接收请求。
  * 配置要映射到容器注册表的模块。
* API 代理模块
  * 配置以下环境变量：

    | 名称 | 值 |
    | ---- | ----- |
    | `DOCKER_REQUEST_ROUTE_ADDRESS` | 注册表模块名称和开放端口。 例如，`registry:5000` 。 |
    | `NGINX_DEFAULT_PORT` | Nginx 代理侦听来自下游设备的请求的端口。 例如，`8000` 。 |

  * 配置以下 createOptions：

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

在此方案的任何 **较低层** 上配置以下模块：

* API 代理模块
  * 配置以下环境变量：

    | 名称 | 值 |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | Nginx 代理侦听来自下游设备的请求的端口。 例如，`8000` 。 |

  * 配置以下 createOptions：

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```  

## <a name="enable-blob-upload"></a>启用 blob 上传

API 代理模块的另一个用例是在较低层中启用 IoT Edge 设备上传 blob。 使用此用例可以在较低层设备上启用疑难解答功能，如上传模块日志或上传支持捆绑。

此方案使用顶层 [IoT Edge 模块上的 Azure Blob 存储](https://azuremarketplace.microsoft.com/marketplace/apps/azure-blob-storage.edge-azure-blob-storage) 来处理 Blob 创建和上载。

在 **顶部层** 配置以下模块：

* IoT Edge 模块上的 Azure Blob 存储。
* API 代理模块
  * 配置以下环境变量：

    | 名称 | 值 |
    | ---- | ----- |
    | `BLOB_UPLOAD_ROUTE_ADDRESS` | Blob 存储模块名称和打开端口。 例如，`azureblobstorageoniotedge:1102` 。 |
    | `NGINX_DEFAULT_PORT` | Nginx 代理侦听来自下游设备的请求的端口。 例如，`8000` 。 |

  * 配置以下 createOptions：

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

在此方案的任何 **较低层** 上配置以下模块：

* API 代理模块
  * 配置以下环境变量：

    | 名称 | 值 |
    | ---- | ----- |
    | `NGINX_DEFAULT_PORT` | Nginx 代理侦听来自下游设备的请求的端口。 例如，`8000` 。 |

  * 配置以下 createOptions：

    ```json
    {
       "HostConfig": {
          "PortBindings": {
             "8000/tcp": [
                {
                   "HostPort": "8000"
                }
             ]
          }
       }
    }
    ```

使用以下步骤将支持捆绑或日志文件上传到位于顶部层的 blob 存储模块：

1. 使用 Azure 存储资源管理器或 REST Api 创建 blob 容器。 有关详细信息，请参阅 [IoT Edge 上的将数据存储在 Azure Blob 存储的边缘](how-to-store-data-blob.md)。
1. 根据 [从 IoT Edge 部署中检索日志](how-to-retrieve-iot-edge-logs.md)中的步骤请求日志或支持捆绑包，但使用域名 `$upstream` 和打开代理端口来代替 blob 存储模块地址。 例如：

   ```json
   {
      "schemaVersion": "1.0",
      "sasUrl": "https://$upstream:8000/myBlobStorageName/myContainerName?SAS_key",
      "since": "2d",
      "until": "1d",
      "edgeRuntimeOnly": false
   }
   ```

## <a name="edit-the-proxy-configuration"></a>编辑代理配置

默认配置文件嵌入在 API 代理模块中，但你可以使用模块克隆通过云将新配置传递到模块。

当你编写自己的配置时，仍可以使用环境来调整每个部署的设置。 使用以下语法：

* 用于 `${MY_ENVIRONMENT_VARIABLE}` 检索环境变量的值。
* 使用条件语句根据环境变量的值打开或关闭设置：

   ```conf
   #if_tag ${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 1
   #endif_tag ${MY_ENVIRONMENT_VARIABLE}

   #if_tag !${MY_ENVIRONMENT_VARIABLE}
       statement to execute if environment variable evaluates to 0
   #endif_tag !${MY_ENVIRONMENT_VARIABLE}
   ```

当 API 代理模块分析代理配置时，它会首先使用替换将中列出的所有环境变量替换 `PROXY_CONFIG_ENV_VAR_LIST` 为其提供的值。 然后，将 `#if_tag` 替换和对之间的所有内容 `#endif_tag` 。 然后，该模块向 nginx 反向代理提供分析的配置。

若要动态更新代理配置，请使用以下步骤：

1. 写入配置文件。 可以使用此默认模板作为参考： [nginx_default_config](https://github.com/Azure/iotedge/blob/master/edge-modules/api-proxy-module/templates/nginx_default_config.conf)
1. 复制配置文件的文本，并将其转换为 base64。
1. 粘贴编码的配置文件作为模块克隆中所 `proxy_config` 需属性的值。

   ![粘贴编码的配置文件作为 proxy_config 属性的值](./media/how-to-configure-api-proxy-module/change-config.png)

## <a name="next-steps"></a>后续步骤

使用 API 代理模块将 [下游 IoT Edge 设备连接到 Azure IoT Edge 的网关](how-to-connect-downstream-iot-edge-device.md)。