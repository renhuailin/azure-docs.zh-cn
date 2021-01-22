---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 01/15/2021
ms.author: mikeray
ms.openlocfilehash: 17a8c9580a8e69213c7f34e8ec889f7e46c6d17d
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696052"
---
本部分介绍如何 (SCC) 应用安全上下文约束。 对于预览版本，它们放宽了安全约束。 

1.  (SCC) 下载自定义安全上下文约束。 使用下列其中一个： 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   -  ([原始](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml)) 
   - `curl` 以下命令将下载 yaml：

      ```console
      curl https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml -o arc-data-scc.yaml
      ```

1. 创建 SCC。

   ```console
   oc create -f arc-data-scc.yaml
   ```

1. 将 SCC 应用到服务帐户。

   > [!NOTE]
   > 在此处和下面的命令中使用相同的命名空间 `azdata arc dc create` 。 示例为 `arc` 。

   ```console
   oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
   ```