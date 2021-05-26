---
ms.openlocfilehash: 673fd1efde5c60600a0f802637088068a063b2c4
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110384860"
---
1. 检索本地计算机的 IP 地址。 使用 Cosmos DB SDK（.NET、Java）配置直接模式设置时，需要此步骤。

   ```bash
   ipaddr="`ifconfig | grep "inet " | grep -Fv 127.0.0.1 | awk '{print $2}' | head -n 1`"
   ```

1. 从注册表中拉取 Docker 映像。

   ```bash
   docker pull mcr.microsoft.com/cosmosdb/linux/azure-cosmos-emulator
   ```

1. 运行具有以下配置的 Docker 映像：

   ```bash
   docker run -p 8081:8081 -p 10251:10251 -p 10252:10252 -p 10253:10253 -p 10254:10254  -m 3g --cpus=2.0 --name=test-linux-emulator -e AZURE_COSMOS_EMULATOR_PARTITION_COUNT=10 -e AZURE_COSMOS_EMULATOR_ENABLE_DATA_PERSISTENCE=true -e AZURE_COSMOS_EMULATOR_IP_ADDRESS_OVERRIDE=$ipaddr -it mcr.microsoft.com/cosmosdb/linux/azure-cosmos-emulator
   ```