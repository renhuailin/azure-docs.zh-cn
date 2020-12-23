---
title: 在 Windows 上设置 Azure Service Fabric Linux 群集
description: 本文介绍如何设置在 Windows 开发计算机上运行的 Service Fabric Linux 群集。 此方法对于跨平台开发非常有用。
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 7b25a84e76773baea9f17430df1b7ba13aa661aa
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93087071"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>设置 Windows 开发人员计算机上的 Linux Service Fabric 群集

本文档介绍如何在 Windows 开发计算机上设置本地 Linux Service Fabric 群集。 设置本地 Linux 群集有助于快速测试在 Windows 计算机上开发的面向 Linux 群集的应用程序。

## <a name="prerequisites"></a>先决条件
基于 Linux 的 Service Fabric 群集不在 Windows 上运行，但为了实现跨平台原型，我们提供了一个可通过用于 Windows 的 Docker 部署的 Linux Service Fabric 一个 box 群集 docker 容器。

准备事项：

* 至少 4 GB RAM
* 最新版本的 [用于 Windows 的 Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* Docker 必须在 Linux 容器模式下运行

>[!TIP]
> 若要在 Windows 计算机上安装 Docker，请按照 [Docker 文档](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions)中的步骤进行操作。 安装之后，请[验证安装](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine)。
>

## <a name="create-a-local-container-and-setup-service-fabric"></a>创建本地容器和设置 Service Fabric
若要设置本地 Docker 容器并在其上运行 Service Fabric 群集，请运行以下步骤：


1. 使用以下内容更新主机上的 Docker 守护程序配置并重启 Docker 守护程序： 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    建议的更新方法是： 

    * Docker 图标 > 设置 > Docker 引擎
    * 添加上面列出的新字段
    * 应用 & 重新启动-重新启动 Docker 守护程序以使更改生效。

2. 通过 PowerShell 启动群集。<br/>
    <b>Ubuntu 18.04 LTS：</b>
    ```powershell
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16.04 LTS：</b>
    ```powershell
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u16
    ```

    >[!TIP]
    > 默认情况下，这样会拉取具有最新 Service Fabric 版本的映像。 对于特定的修订版本，请参阅 Docker Hub 上的 [Service Fabric Onebox](https://hub.docker.com/_/microsoft-service-fabric-onebox) 页面。



3. 可选：生成扩展 Service Fabric 映像。

    在新目录中，创建一个名为的文件 `Dockerfile` 来构建自定义映像：

    >[!NOTE]
    >您可以使用 Dockerfile 调整上面的图像，将其他程序或依赖项添加到容器中。
    >例如，添加 `RUN apt-get install nodejs -y` 可以支持将 `nodejs` 应用程序用作来宾可执行文件。
    ```Dockerfile
    FROM mcr.microsoft.com/service-fabric/onebox:u18
    RUN apt-get install nodejs -y
    EXPOSE 19080 19000 80 443
    WORKDIR /home/ClusterDeployer
    CMD ["./ClusterDeployer.sh"]
    ```
    
    >[!TIP]
    > 默认情况下，这样会拉取具有最新 Service Fabric 版本的映像。 对于特定的修订版本，请访问 [Docker 中心](https://hub.docker.com/r/microsoft/service-fabric-onebox/) 页。

    若要从生成可重复使用的映像 `Dockerfile` ，请打开终端并 `cd` 直接保存， `Dockerfile` 然后运行：

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >此操作需要一段时间，但只需执行一次。

    现在，你可以在每次需要时通过运行来快速启动 Service Fabric 的本地副本：

    ```powershell 
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >为容器实例提供一个名称，以更具可读性的方式对其进行处理。 
    >
    >如果应用程序正在侦听特定端口，则必须使用附加的 `-p` 标记指定这些端口。 例如，如果应用程序正在侦听端口 8080，请添加下面的 `-p` 标记：
    >
    >`docker run -itd -p 19000:19000 -p 19080:19080 -p 8080:8080 --name sfonebox mcr.microsoft.com/service-fabric/onebox:u18`
    >


4. 群集需要一小段时间来启动，可以使用以下命令查看日志，或者通过 `http://localhost:19080` 跳转到仪表板来查看群集运行状况：

    ```powershell 
    docker logs sftestcluster
    ```

5. 成功部署群集后，如步骤4中所述，你可以 ``http://localhost:19080`` 从 Windows 计算机中转到查找 Service Fabric Explorer 仪表板。 此时，你可以使用 Windows 开发人员计算机中的工具连接到此群集，并部署面向 Linux Service Fabric 群集的应用程序。 

    > [!NOTE]
    > Windows 当前不支持 Eclipse 插件。 

6. 完成后，请通过此命令停止并清除容器：

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>已知限制 
 
 以下是在 Mac 的容器中运行的本地群集的已知限制： 
 
 * DNS 服务未运行，并且在容器中目前不受支持。 [问题 #132](https://github.com/Microsoft/service-fabric/issues/132)
 * 运行基于容器的应用需要在 Linux 主机上运行 SF。 当前不支持嵌套容器应用。

## <a name="next-steps"></a>后续步骤
* [使用 Yeoman 在 Linux 上创建和部署第一个 Service Fabric Java 应用程序](service-fabric-create-your-first-linux-application-with-java.md)
* [Eclipse](./service-fabric-get-started-eclipse.md) 入门
* 查看其他 [Java 示例](https://github.com/Azure-Samples/service-fabric-java-getting-started)
* 了解 [Service Fabric 支持选项](service-fabric-support.md)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
