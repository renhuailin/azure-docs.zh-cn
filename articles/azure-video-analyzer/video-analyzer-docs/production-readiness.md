---
title: 生产就绪情况和最佳做法
description: 本文提供如何在生产环境中配置和部署 Azure 视频分析器模块的指南。
ms.topic: reference
ms.date: 06/01/2021
ms.openlocfilehash: 1f7477be52d99bdfca91fd0d122d2db63ef27827
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114602142"
---
# <a name="production-readiness-and-best-practices"></a>生产就绪情况和最佳做法

本文提供如何在生产环境中配置和部署 Azure 视频分析器模块和云服务的指南。 在准备 IoT Edge 解决方案时，你还应该参阅[准备在生产环境中部署 IoT Edge 解决方案](../../iot-edge/production-checklist.md)一文。

> [!NOTE]
> 就安全性方面的相关问题，你应该咨询组织的 IT 部门。

## <a name="creating-the-video-analyzer-account"></a>创建视频分析器帐户
[创建](create-video-analyzer-account.md)视频分析器帐户时，建议执行以下操作：
1. 订阅所有者应创建一个资源组，用于创建视频分析器所需的所有资源。
1. 然后，所有者应向你授予该资源组的[参与者](../../role-based-access-control/built-in-roles.md#contributor)和[用户访问管理员](../../role-based-access-control/built-in-roles.md#user-access-administrator)角色。
1. 然后，你可以在该资源组下创建相关资源：存储帐户、用户分配的托管标识和视频分析器帐户。

## <a name="running-the-module-as-a-local-user"></a>以本地用户的身份运行模块

将视频分析器边缘模块部署到 IoT Edge 设备时，默认情况下，它将以提升的权限运行。 可以使用模块 (`sudo iotedge logs {name-of-module}`) 中的日志进行检查，将显示：

```
!! production readiness: user accounts – Warning
       LOCAL_USER_ID and LOCAL_GROUP_ID environment variables are not set. The program will run as root!
       For optimum security, make sure to set LOCAL_USER_ID and LOCAL_GROUP_ID environment variables to a non-root user and group.
```

以下各部分讨论了如何处理上述警告。

### <a name="creating-and-using-a-local-user-account"></a>创建和使用本地用户帐户

可以并且应该使用具有尽可能少权限的帐户在生产环境中运行视频分析器边缘模块。 例如，以下命令展示了如何在 Linux VM 上创建本地用户帐户：

```
sudo groupadd -g 1010 localedgegroup
sudo useradd --home-dir /home/localedgeuser --uid 1010 --gid 1010 localedgeuser
```

接下来，在部署清单中，可以将 LOCAL_USER_ID 和 LOCAL_GROUP_ID 环境变量设置为该非根用户和组：

```
"avaedge": {
"version": "1.0",
…
"env": {
    "LOCAL_USER_ID": 
    {
        "value": "1010"
    },
    "LOCAL_GROUP_ID": {
        "value": "1010"
    }
}
},
…
```

### <a name="granting-permissions-to-device-storage"></a>授予设备存储权限

视频分析器边缘模块需要能在执行以下操作时将文件写入本地文件系统：

- 使用模块孪生属性 [`applicationDataDirectory`](module-twin-configuration-schema.md) 时，应通过此属性指定本地文件系统上用于存储配置数据的目录。
- 使用管道将视频录制到云中时，此模块需要将边缘设备上的目录用作缓存（有关详细信息，请参阅[连续视频录制](continuous-video-recording.md)一文）。
- [到本地文件](event-based-video-recording-concept.md)，可以在其中指定所录制视频的文件路径。

如果你打算使用上述任何操作，应确保上述用户帐户有权访问相关目录。 例如，请考虑 `applicationDataDirectory`。 你可以在边缘设备上创建目录，并将设备存储链接到模块存储。

```
sudo mkdir /var/lib/videoanalyzer
sudo chown -R localedgeuser:localedgegroup /var/lib/videoanalyzer
```

接下来，在部署清单中适用于边缘模块的创建选项中，你可以将上述映射目录（“/var/lib/videoanalyzer”）的 `binds` 设置添加到模块中的目录（例如“/var/lib/videoanalyzer”）。 并且将后一个目录用作 `applicationDataDirectory` 的值。

```
        "modules": {
          "avaedge": {
            "version": "1.1",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/media/video-analyzer:1",
              "createOptions": "{ \"HostConfig\": { \"LogConfig\": { \"Type\": \"\", \"Config\": { \"max-size\": \"10m\", \"max-file\": \"10\" } }, \"Binds\": [ \"/var/media/:/var/media/\", \"/var/lib/videoanalyzer/:/var/lib/videoanalyzer\" ], \"IpcMode\": \"host\", \"ShmSize\": 1536870912 } }"
            },
            "env": {
              "LOCAL_USER_ID": {
                "value": "1010"
              },
              "LOCAL_GROUP_ID": {
                "value": "1010"
              }
            }
          },
          …
        },
        
    …
    
    "avaedge": {
       "properties.desired": {
          "applicationDataDirectory": "/var/lib/videoanalyzer",
          "ProvisioningToken": "{your-token}",
          "diagnosticsEventsOutputName": "diagnostics",
          "operationalEventsOutputName": "operational",
          "logLevel": "information",
          "LogCategories": "Application,Events",
          "allowUnsecuredEndpoints": true,
          "telemetryOptOut": false
          "allowUnsecuredEndpoints": false
    }
}
```

如果查看快速入门的示例管道和教程（例如[连续视频录制](use-continuous-video-recording.md)），你会注意到媒体缓存目录 (`localMediaCachePath`) 使用了 `applicationDataDirectory` 下的子目录。 这是建议的方法，因为缓存包含暂时性数据。

另请注意，将 `allowedUnsecuredEndpoints` 设置为 `true`，正如将使用 TLS 加密来保护流量的生产环境中所建议的。

### <a name="tips-about-maintaining-your-edge-device"></a>有关维护边缘设备的提示

> [!Note]
> 以下提示并不详尽，但应有助于解决我们遇到的常见已知问题。

如果不定期对用作 IoT Edge 设备的 Linux VM 进行管理，它可能会变得无响应。 因此，务必要保持缓存的清洁、清除不必要的包，并且从 VM 中删除未使用的容器。 为此，可以在边缘 VM 上使用下面一组建议的命令。

- `sudo apt-get clean`

apt-get clean 命令清理留在 /var/cache 中的检索的包文件的本地存储库。 它清理的目录是 /var/cache/apt/archives/ 和 /var/cache/apt/archives/partial/。 它在 /var/cache/apt/archives 中保留的唯一文件是锁定文件和部分子目录。 apt-get clean 命令通常用于根据需要清理磁盘空间，通常作为定期计划性维护的一部分。 有关详细信息，请参阅[使用 apt-get 进行清理](https://www.networkworld.com/article/3453032/cleaning-up-with-apt-get.html)。

- `sudo apt-get autoclean`

与 apt-get clean 一样，apt-get autoclean 将清理检索的包文件的本地存储库，但它只会删除无法再下载且没有用的文件。 它有助于防止缓存变得太大。

- `sudo apt-get autoremove`

自动删除选项将删除自动安装的包，因为其他的一些包需要这些自动安装的包，但在那些需要它们的包被删除后，将不再需要它们了

- `sudo docker image ls` - 提供边缘系统上的 Docker 映像列表

- `sudo docker system prune`

Docker 采用保守的方法来清理未使用的对象（通常称为“垃圾回收”），例如映像、容器、卷和网络：除非你明确要求 Docker 这样做，否则通常不会删除这些对象。 这可能导致 Docker 会使用额外的磁盘空间。 对于每种类型的对象，Docker 都提供了 prune 命令。 此外，还可以使用 `docker system prune` 一次性清理多种类型的对象。 有关详细信息，请参阅[删除未使用的 Docker 对象](https://docs.docker.com/config/pruning/)。

- `sudo docker rmi REPOSITORY:TAG`

当边缘模块在进行更新时，docker 仍可有旧版本的边缘模块。 在这种情况下，建议使用 `docker rmi` 命令删除映像版本标记标识的特定映像。

## <a name="next-steps"></a>后续步骤

[快速入门：入门 - Azure 视频分析器](get-started-detect-motion-emit-events.md)
