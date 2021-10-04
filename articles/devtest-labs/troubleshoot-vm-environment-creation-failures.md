---
title: 排查 VM 和环境故障
description: 了解如何在 Azure 开发测试实验室中排查虚拟机 (VM) 和环境创建失败问题。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: c0765254110302c6306cd37b9d55b8465510a586
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128561270"
---
# <a name="troubleshoot-virtual-machine-vm-and-environment-creation-failures-in-azure-devtest-labs"></a>在 Azure 开发测试实验室中排查虚拟机 (VM) 和环境创建失败问题
如果计算机名称无效或者你会违反实验室策略，则开发测试实验室会发出警告。 有时，实验室 VM 或环境状态旁边会显示红色 `X`，告知你出现错误。  本文提供了一些技巧，可用于查找基本问题，并可能有助于你在将来避免此问题。

## <a name="portal-notifications"></a>门户通知
如果你使用 Azure 门户，则首先需要查看通知面板。  通知面板（单击钟形图标后会显示在主命令栏上）会告诉你实验室 VM 或环境创建是否成功。  如果失败，则会看到与创建失败关联的错误消息。 详细信息通常会提供进一步的信息来帮助你解决问题。 在下面的示例中，虚拟机创建失败，因为核心用完了。 详细消息会告诉你如何解决此问题并请求提高核心配额。

![Azure 门户通知](./media/troubleshoot-vm-environment-creation-failures/portal-notification.png)

### <a name="vm-in-corruption-state"></a>处于损坏状态的 VM
如果在实验室中看到你的 VM 的状态为“已损坏”，则表明基础 VM 可能已从“虚拟机”(Virtual Machine) 页面中删除（用户可以从“虚拟机”(Virtual Machines) 页面而不是“开发测试实验室”页面导航到该页面）。   通过从实验室中删除 VM 来清理开发测试实验室中的实验室。 然后，在实验室中重新创建你的 VM。 

![处于已损坏状态的 VM](./media/troubleshoot-vm-environment-creation-failures/vm-corrupted-state.png)



## <a name="activity-logs"></a>活动日志
如果你要调查尝试创建 VM 或环境后的某个时间出现的故障，请查看活动日志。 本部分展示了如何查找 VM 和环境的日志。

## <a name="activity-logs-for-virtual-machines"></a>虚拟机的活动日志

1. 在你的实验室的主页上，选择 VM 以启动“虚拟机”页面。
2. 在“虚拟机”页的左侧菜单的“监视”部分，选择“活动日志”以查看与 VM 关联的所有日志。
3. 在活动日志项中，选择失败的操作。 通常，失败的操作名为“`Write Virtualmachines`”。
4. 在右侧窗格中，切换到“JSON”选项卡。日志的 JSON 视图中会显示详细信息。

    ![VM 的活动日志](./media/troubleshoot-vm-environment-creation-failures/vm-activity-log.png)
5. 查看 JSON 日志，直到找到 `statusMessage` 属性。 它提供主要错误消息和进一步的详细信息（如果适用）。 下面的 JSON 是在本文前面部分看到的“超出了核心配额”错误的示例。

    ```json
    "properties": {
        "statusCode": "Conflict",
        "statusMessage": "{\"status\":\"Failed\",\"error\":{\"code\":\"ResourceDeploymentFailure\",\"message\":\"The resource operation completed with terminal provisioning state 'Failed'.\",\"details\":[{\"code\":\"OperationNotAllowed\",\"message\":\"Operation results in exceeding quota limits of Core. Maximum allowed: 100, Current in use: 100, Additional requested: 8. Please read more about quota increase at https://aka.ms/corequotaincrease.\"}]}}",
    },
    ```

## <a name="activity-log-for-an-environment"></a>环境的活动日志

若要查看创建环境时的活动日志，请执行以下步骤：

1. 在实验室主页的左侧菜单上，选择“配置和策略”。
2. 在“配置和策略”页面的菜单上，选择“活动日志”。
3. 在日志的活动列表中查找故障，然后选择它。
4. 在右侧窗格中，切换到“JSON”选项卡，然后查找“statusMessage”。

    ![环境活动日志](./media/troubleshoot-vm-environment-creation-failures/envirionment-activity-log.png)

## <a name="resource-manager-template-deployment-logs"></a>资源管理器模板部署日志
如果你的环境或虚拟机是通过自动化创建的，则还有最后一个可在其中查看错误信息的位置。 那就是 Azure 资源管理器模板部署日志。 通过自动化创建实验室资源时，资源通常是通过 Azure 资源管理器模板部署创建的。 有关创建开发测试实验室资源的示例 Azure 资源管理器模板，请参阅 [https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)。

若要查看实验室模板部署日志，请执行以下步骤：

1. 启动实验室所在的资源组的页面。
2.  在左侧菜单的“设置”下选择“部署”。
3. 查找状态为“失败”的部署，然后选择它。
4. 在“部署”页上，为失败的操作选择“操作详细信息”链接。
5. “操作详细信息”窗口会显示失败操作的详细信息。

## <a name="next-steps"></a>后续步骤
请参阅[排查项目故障](devtest-lab-troubleshoot-artifact-failure.md)
