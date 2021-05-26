---
title: 在 Windows 中持久保存状态 - Azure 事件网格 IoT Edge | Microsoft Docs
description: 在 Windows 中持久保存状态
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.subservice: iot-edge
ms.date: 05/10/2021
ms.topic: article
ms.openlocfilehash: 23428688d7f65bf311ecd23d47a23ab9b2e89617
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110367737"
---
# <a name="persist-state-in-windows"></a>在 Windows 中持久保存状态

默认情况下，在事件网格模块中创建的主题和订阅存储在容器文件系统中。 如果不能持久保存，则在重新部署该模块时，创建的所有元数据都将丢失。 若要在部署和重启之间保留数据，你需要将数据保存在容器文件系统之外。 

默认情况下，只保存元数据，事件仍存储在内存中，以提高性能。 按照“持久事件”部分的介绍，启用事件持久性。

本文介绍了在 Windows 部署中部署具有持久性的事件网格模块所需的步骤。

> [!NOTE]
>事件网格模块在 Windows 中作为低权限用户“ContainerUser”运行。

## <a name="persistence-via-volume-mount"></a>通过卷装载持久保存

[Docker 卷](https://docs.docker.com/storage/volumes/) 用于在部署之间保留数据。 要装载卷，需要使用 docker 命令创建它，并授予权限，以便容器可对其进行读取和写入，然后部署该模块。

1. 通过运行以下命令创建一个卷：

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume create <your-volume-name-here>
    ```

    例如，

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume create myeventgridvol
   ```
1. 通过运行以下命令获取卷映射到的主机目录

    ```sh
    docker -H npipe:////./pipe/iotedge_moby_engine volume inspect <your-volume-name-here>
    ```

    例如，

   ```sh
   docker -H npipe:////./pipe/iotedge_moby_engine volume inspect myeventgridvol
   ```

   示例输出：-

   ```json
   [
          {
            "CreatedAt": "2019-07-30T21:20:59Z",
            "Driver": "local",
            "Labels": {},
            "Mountpoint": "C:\\ProgramData\\iotedge-moby\u000bolumes\\myeventgridvol\\_data",
            "Name": "myeventgridvol",
            "Options": {},
            "Scope": "local"
          }
   ]
   ```
1. 按照以下步骤将“用户”组添加到 Mountpoint 指向的值 ：
    1. 启动文件资源管理器。
    1. 导航到 Mountpoint 指向的文件夹。
    1. 右键单击，然后选择“属性”。
    1. 选择“安全”。
    1. 在 *组或用户名下，选择“编辑”。
    1. 选择“添加”，输入 `Users`，选择“检查名称”，然后选择“确定”  。
    1. 在“用户权限”下，选择“修改”，然后选择“确定” 。
1. 使用“绑定”装载此卷，并从 Azure 门户重新部署事件网格模块

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
                  "<your-volume-name-here>:C:\\app\\metadataDb"
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
   >请勿更改绑定值的第二部分。 它指向模块中的特定位置。 对于窗口上的事件网格模块，该位置必须为 C:\\app\\metadataDb。


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
                "myeventgridvol:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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

## <a name="persistence-via-host-directory-mount"></a>通过主机目录装载的持久性

你可以在主机系统上创建一个目录并装载该目录，以替代卷。

1. 通过运行以下命令在主机文件系统上创建一个目录。

   ```sh
   mkdir <your-directory-name-here>
   ```

   例如，

   ```sh
   mkdir C:\myhostdir
   ```
1. 使用“绑定”装载目录，并从 Azure 门户重新部署事件网格模块。

    ```json
    {
         "HostConfig": {
            "Binds": [
                "<your-directory-name-here>:C:\\app\\metadataDb"
             ]
         }
    }
    ```

    >[!IMPORTANT]
    >请勿更改绑定值的第二部分。 它指向模块中的特定位置。 对于窗口上的事件网格模块，该位置必须为 C:\\app\\metadataDb。

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
                "C:\\myhostdir:C:\\app\\metadataDb",
                "C:\\myhostdir2:C:\\app\\eventsDb"
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
## <a name="persist-events"></a>持久保存事件

要启用事件持久性，必须首先使用上述部分所述的步骤，通过卷装载或主机目录装载启用事件持久性。

有关永久保存事件的重要说明：

* 在每个“事件订阅”的基础上启用永久保存事件，并在装载卷或目录后选择加入。
* 事件持久性在创建时配置在“事件订阅”上，并且在创建“事件订阅”后无法修改。 若要切换事件持久性，必须删除并重新创建“事件订阅”。
* 持久性事件几乎总是比内存操作慢，但是速度差异很大程度上取决于驱动器的特性。 速度和可靠性之间的权衡对于所有消息系统都是固有的，但只有在超出一定范围时才明显。

若要在“事件订阅”上启用事件持久性，请将 `persistencePolicy` 设置为 `true`：

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