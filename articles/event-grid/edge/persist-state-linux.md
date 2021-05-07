---
title: 在 Linux 中持久保存状态-Azure Event Grid IoT Edge |Microsoft Docs
description: 在 Linux 中持久保存元数据
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 92333c2528303a6fa53fa30f47def33c33235d39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171476"
---
# <a name="persist-state-in-linux"></a>在 Linux 中持久保存状态

默认情况下，在 Event Grid 模块中创建的主题和订阅存储在容器文件系统中。 如果不能持久保存，则在重新部署该模块时，创建的所有元数据都将丢失。 若要在部署和重启之间保留数据，你需要将数据保存在容器文件系统之外。

默认情况下，只保存元数据，事件仍存储在内存中，以提高性能。 按照 "持久事件" 部分的介绍，启用事件持久性。

本文介绍了在 Linux 部署中部署具有持久性的 Event Grid 模块的步骤。

> [!NOTE]
>Event Grid 模块以具有 UID `2000` 和名称 `eventgriduser`的低权限用户身份运行。

## <a name="persistence-via-volume-mount"></a>通过卷挂载持久保存

 [Docker 卷](https://docs.docker.com/storage/volumes/) 用于在部署之间保存数据。 可以让 docker 自动创建命名卷，作为部署 Event Grid 模块的一部分。 此选项是最简单的选项。 可以在 " **绑定** " 部分指定要创建的卷名，具体操作如下：

```json
  {
     "HostConfig": {
          "Binds": [
                 "<your-volume-name-here>:/app/metadataDb"
           ]
      }
   }
```

>[!IMPORTANT]
>请勿更改绑定值的第二部分。 它指向模块内的特定位置。 Linux 上的 Event Grid 模块必须是 **/app/metadataDb**。

例如，以下配置将会促使创建卷 **egmetadataDbVol** ，其中元数据将保存。

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "Binds": [
      "egmetadataDbVol:/app/metadataDb",
      "egdataDbVol:/app/eventsDb"
    ],
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```

你可以在主机系统上创建一个目录并挂载该目录，以替代卷。

## <a name="persistence-via-host-directory-mount"></a>通过主机目录挂载的持久性

你还可以选择挂载主机文件夹，以替代 docker 卷。

1. 首先，通过运行以下命令，在主机计算机上创建名称为 **eventgriduser** 、ID 为 **2000** 的用户：

    ```sh
    sudo useradd -u 2000 eventgriduser
    ```
1. 通过运行以下命令在主机文件系统上创建一个目录。

   ```sh
   md <your-directory-name-here>
   ```

    例如，运行以下命令将创建一个名为 **myhostdir** 的目录。

    ```sh
    md /myhostdir
    ```
1. 接下来，通过运行以下命令，将该文件夹的所有者设置为 **eventgriduser**。

   ```sh
   sudo chown eventgriduser:eventgriduser -hR <your-directory-name-here>
   ```

    例如，

    ```sh
    sudo chown eventgriduser:eventgriduser -hR /myhostdir
    ```
1. 使用 **绑定** 来挂载目录，并从 Azure 门户重新部署 Event Grid 模块。

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:/app/metadataDb",
                "<your-directory-name-here>:/app/eventsDb",
             ]
         }
    }
    ```

    例如，

    ```json
    {
          "Env": [
            "inbound__serverAuth__tlsPolicy=strict",
            "inbound__serverAuth__serverCert__source=IoTEdge",
            "inbound__clientAuth__sasKeys__enabled=false",
            "inbound__clientAuth__clientCert__enabled=true",
            "inbound__clientAuth__clientCert__source=IoTEdge",
            "inbound__clientAuth__clientCert__allowUnknownCA=true",
            "outbound__clientAuth__clientCert__enabled=true",
            "outbound__clientAuth__clientCert__source=IoTEdge",
            "outbound__webhook__httpsOnly=true",
            "outbound__webhook__skipServerCertValidation=false",
            "outbound__webhook__allowUnknownCA=true"
          ],
          "HostConfig": {
                "Binds": [
                  "/myhostdir:/app/metadataDb",
                  "/myhostdir2:/app/eventsDb"
                ],
                "PortBindings": {
                      "4438/tcp": [
                        {
                          "HostPort": "4438"
                        }
                      ]
                }
          }
    }
    ```

    >[!IMPORTANT]
    >请勿更改绑定值的第二部分。 它指向模块内的特定位置。 Linux 上的 Event Grid 模块必须是 **/app/metadataDb** 和 **/app/eventsDb**


## <a name="persist-events"></a>持久保存事件

若要启用事件持久性，必须首先使用上述部分所述的步骤，通过卷挂载或主机目录挂载启用元数据持久性。

有关保存事件的重要说明：

* 在每个“事件订阅”的基础上启用保存事件，并在挂载卷或目录后选择加入。
* 事件持久性在创建时配置在“事件订阅”上，并且在创建“事件订阅”后无法修改。 若要切换事件持久性，必须删除并重新创建“事件订阅”。
* 持久性事件几乎总是比内存操作慢，但是速度差异很大程度上取决于驱动器的特性。 速度和可靠性之间的权衡对于所有消息系统都是固有的，但通常情况下只有在超出一定范围时才明显。

若要在“事件订阅”上启用事件持久性，请将 `persistencePolicy` 设置为 `true` ：

 ```json
        {
          "properties": {
            "persistencePolicy": {
              "isPersisted": "true"
            },
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "<your-webhook-url>"
              }
            }
          }
        }
 ```
