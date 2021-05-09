---
title: 如何停止监视 Azure 和 Red Hat OpenShift v4 群集 | Microsoft Docs
description: 本文介绍了如何通过“容器见解”停止监视 Azure Red Hat OpenShift 和 Red Hat OpenShift 版本 4 群集。
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 09ca05a25ce9bb02b8a3d515acf060e2e9e7e8c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731793"
---
# <a name="how-to-stop-monitoring-your-azure-and-red-hat-openshift-v4-cluster"></a>如何停止监视 Azure 和 Red Hat OpenShift v4 群集

启用 Azure Red Hat OpenShift 和 Red Hat OpenShift 版本 4.x 群集的监视后，如果用户决定不再想要对其进行监视，可以使用“容器见解”停止监视群集。 本文介绍了如何完成此任务。  

## <a name="how-to-stop-monitoring-using-helm"></a>如何使用 Helm 停止监视

1. 若要首先确定群集上安装的容器 insights helm 图表版本，请运行以下 helm 命令。

    ```
    helm list
    ```

    输出如下所示：

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon-containers-release-1* 表示用于“容器见解”的 helm 图表版本。

2. 若要删除图表版本，请运行以下 helm 命令。

    `helm delete <releaseName>`

    示例：

    `helm delete azmon-containers-release-1`

    允许该命令后，将从群集中删除发布。 用户可通过再次运行命令 `helm list` 来验证。

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

配置更改可能需要几分钟才能完成。 由于 Helm 会在用户删除发布后对其进行跟踪，因此可以审核群集的历史记录，甚至还可以使用 `helm rollback` 撤消删除发布。

## <a name="next-steps"></a>后续步骤

如果创建的“日志分析工作区”仅用于支持监视群集，且不需要再使用，则需要手动删除它。 如果不熟悉如何删除工作区，请参阅[删除 Azure 日志分析工作区](../logs/delete-workspace.md)。