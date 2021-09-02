---
title: 在用于 Windows 的 Docker 上运行模拟器
itleSuffix: Running the Azure Cosmos DB emulator on Docker for Windows
description: 了解如何在用于 Windows 的 Docker 上运行和使用 Azure Cosmos DB 模拟器。 使用此模拟器，可以在本地免费开发和测试应用程序，无需创建 Azure 订阅。
ms.service: cosmos-db
ms.topic: how-to
author: StefArroyo
ms.author: esarroyo
ms.date: 04/20/2021
ms.openlocfilehash: bda82b51661ccd615e26c7abcc8002db3bb5d24f
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123111478"
---
# <a name="use-the-emulator-on-docker-for-windows"></a><a id="run-on-windows-docker"></a>在用于 Windows 的 Docker 上使用模拟器
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

可以在 Windows Docker 容器中运行 Azure Cosmos DB 模拟器。 有关 docker pull 命令，请参阅 [Docker 中心](https://hub.docker.com/r/microsoft/azure-cosmosdb-emulator/)；有关 `Dockerfile` 和详细信息，请参阅 [GitHub](https://github.com/Azure/azure-cosmos-db-emulator-docker)。 当前，该模拟器不适合于用于 Oracle Linux 的 Docker。 根据以下说明在用于 Windows 的 Docker 上运行模拟器：

1. 安装[用于 Windows 的 Docker](https://www.docker.com/docker-windows) 后，通过右键单击工具栏上的 Docker 图标并选择“切换到 Windows 容器”切换到 Windows 容器。

1. 接下来，通过从你喜欢使用的 shell 运行以下命令，从 Docker 中心拉取模拟器映像。

   ```bash
   docker pull mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```

1. 若要启动映像，请运行以下命令，具体取决于命令行或 PowerShell 环境：

   # <a name="command-line"></a>[命令行](#tab/cli)

   ```bash

   md %LOCALAPPDATA%\CosmosDBEmulator\bind-mount

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%LOCALAPPDATA%\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator
   ```
   基于 Windows 的 Docker 映像并非总是与每个 Windows 主机操作系统都兼容。 例如，默认的 Azure Cosmos DB 模拟器映像仅与 Windows 10 和 Windows Server 2016 兼容。 如果需要与 Windows Server 2019 兼容的映像，请改为运行以下命令：

   ```bash
   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=%hostDirectory%,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/winsrv2019/azure-cosmos-emulator:latest
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell

   md $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount 2>null

   docker run --name azure-cosmosdb-emulator --memory 2GB --mount "type=bind,source=$env:LOCALAPPDATA\CosmosDBEmulator\bind-mount,destination=C:\CosmosDB.Emulator\bind-mount" --interactive --tty -p 8081:8081 -p 8900:8900 -p 8901:8901 -p 8902:8902 -p 10250:10250 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254 -p 10255:10255 -p 10256:10256 -p 10350:10350 mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator

   ```

   响应类似于以下内容：

   ```bash
   Starting emulator
   Emulator Endpoint: https://172.20.229.193:8081/
   Primary Key: C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==
   Exporting SSL Certificate
   You can import the SSL certificate from an administrator command prompt on the host by running:
   cd /d %LOCALAPPDATA%\CosmosDBEmulatorCert
   powershell .\importcert.ps1
   --------------------------------------------------------------------------------------------------
   Starting interactive shell
   ```
   ---

   > [!NOTE]
   > 执行 `docker run` 命令时，如果发现端口冲突错误（也就是说，如果指定的端口已在使用中），则可以通过更改端口号来传递自定义端口。 例如，可以将“-p 8081:8081”参数更改为“-p 443:8081”

1. 现在，使用来自响应的模拟器终结点和主密钥，并将 TLS/SSL 证书导入到主机中。 若要导入 TLS/SSL 证书，请从管理员命令提示符处运行以下步骤：

   # <a name="command-line"></a>[命令行](#tab/cli)

   ```bash
   cd  %LOCALAPPDATA%\CosmosDBEmulator\bind-mount
   powershell .\importcert.ps1
   ```

   # <a name="powershell"></a>[PowerShell](#tab/powershell)

   ```powershell
   cd $env:LOCALAPPDATA\CosmosDBEmulator\bind-mount
   .\importcert.ps1
   ```
   ---

1. 如果你在模拟器启动之后关闭了交互式 shell，则会关闭模拟器的容器。 若要重新打开数据资源管理器，请在浏览器中导航到以下 URL。 上面所示的响应消息中提供了模拟器终结点。

   `https://<emulator endpoint provided in response>/_explorer/index.html`

如果你有在 Linux docker 容器中运行的 .NET 客户端应用程序，并且你在主机上运行 Azure Cosmos DB 模拟器，请根据下一部分的说明将证书导入到 Linux docker 容器中。

## <a name="regenerate-the-emulator-certificates"></a>重新生成模拟器证书

在 Docker 容器中运行模拟器时，每次停止并重启相应的容器都会重新生成与模拟器关联的证书。 因此，必须在每个容器启动后重新导入证书。 若要解决此限制，可以使用 Docker Compose 文件将 Docker 容器绑定到特定的 IP 地址和容器映像。

例如，你可以在 Docker Compose 文件中使用以下配置，确保根据你的要求设置其格式： 

```yml
version: '2.4' # Do not upgrade to 3.x yet, unless you plan to use swarm/docker stack: https://github.com/docker/compose/issues/4513

networks:
  default:
    external: false
    ipam:
      driver: default
      config:
        - subnet: "172.16.238.0/24"

services:

  # First create a directory that will hold the emulator traces and certificate to be imported
  # set hostDirectory=C:\emulator\bind-mount
  # mkdir %hostDirectory%

  cosmosdb:
    container_name: "azurecosmosemulator"
    hostname: "azurecosmosemulator"
    image: 'mcr.microsoft.com/cosmosdb/windows/azure-cosmos-emulator'
    platform: windows
    tty: true
    mem_limit: 3GB
    ports:
        - '8081:8081'
        - '8900:8900'
        - '8901:8901'
        - '8902:8902'
        - '10250:10250'
        - '10251:10251'
        - '10252:10252'
        - '10253:10253'
        - '10254:10254'
        - '10255:10255'
        - '10256:10256'
        - '10350:10350'
    networks:
      default:
        ipv4_address: 172.16.238.246
    volumes:
        - '${hostDirectory}:C:\CosmosDB.Emulator\bind-mount'
```

## <a name="next-steps"></a>后续步骤

在本文中，你已了解如何使用本地模拟器进行免费的本地开发。 你现在可以继续学习下面的文章：

* [导出要用于 Java、Python 和 Node.js 应用的 Azure Cosmos DB 模拟器证书](local-emulator-export-ssl-certificates.md)
* [使用命令行参数和 PowerShell 命令控制模拟器](emulator-command-line-parameters.md)
* [使用模拟器调试问题](troubleshoot-local-emulator.md)