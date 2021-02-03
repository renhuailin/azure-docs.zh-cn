---
author: MikeRayMSFT
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.topic: include
ms.date: 01/15/2021
ms.author: mikeray
ms.openlocfilehash: 6c8dbeea83cba306cfb788cf447236088045ffc9
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2021
ms.locfileid: "99494000"
---
本部分介绍如何 (SCC) 应用安全上下文约束。 对于预览版本，它们放宽了安全约束。 

1.  (SCC) 下载自定义安全上下文约束。 使用下列其中一个： 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - [原始](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/arc-data-scc.yaml)
   - `curl`
   
      以下命令将下载 yaml：

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

   > [!NOTE]
   > RedHat OpenShift 4.5 或更高版本，将更改如何将 SCC 应用到服务帐户。
   > 在此处和下面的命令中使用相同的命名空间 `azdata arc dc create` 。 示例为 `arc` 。 
   > 
   > 如果使用 RedHat OpenShift 4.5 或更高版本，请运行： 
   >
   >```console
   >oc create rolebinding arc-data-rbac --clusterrole=system:openshift:scc:arc-data-scc --serviceaccount=arc:default
   >```
