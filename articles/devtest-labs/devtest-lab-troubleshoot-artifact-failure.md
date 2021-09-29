---
title: 诊断 Azure 开发测试实验室虚拟机中的项目失败
description: 开发测试实验室提供了可用于诊断项目失败的信息。 本文介绍如何排查项目失败的问题。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: e7c343eee8c3484e12139c2bef963fc463ffc28d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128567442"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>对实验室中的项目失败进行诊断 
创建项目后，可检查其为成功还是失败。 Azure 开发测试实验室中的项目日志提供了可用于诊断项目失败的信息。 可通过几个选项查看 Windows VM 的项目日志信息：

* 在 Azure 门户中
* 在虚拟机中

> [!NOTE]
> 若要确保正确标识失败项目并对其进行解释，请务必确保项目结构正确。 有关如何正确构造项目的信息，请参阅[创建自定义项目](devtest-lab-artifact-author.md)。 若要查看结构正确的项目的示例，请查看此[测试参数类型](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes)项目。

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>使用 Azure 门户对项目失败进行故障排除

1. 在 Azure 门户中的资源列表中选择实验室。
2. 选择包含想要调查的项目的 Windows VM。
3. 在左侧面板中，在“常规”下选择“项目”。 随即会出现与该 VM 关联的项目的列表。 其中显示了项目的名称和状态。

   ![项目状态](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure-new.png)

4. 选择状态显示为“失败”的项目。 项目随即打开。 此时会显示一条扩展消息，其中包含有关项目失败的详细信息。

   ![项目错误消息](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>从虚拟机中对项目失败进行故障排除

1. 登录到包含要诊断的项目的 VM。
2. 转到 C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\1.9\Status，其中 1.9 为 Azure 自定义脚本扩展版本号。

   ![状态文件](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status-new.png)

3. 打开状态文件。

有关在 Linux VM 上查找日志文件的说明，请参阅以下文章：[将 Azure 自定义脚本扩展版本 2 与 Linux 虚拟机配合使用](../virtual-machines/extensions/custom-script-linux.md#troubleshooting)


## <a name="related-blog-posts"></a>相关的博客文章
* [使用开发测试实验室中的资源管理器模板将 VM 加入到现有 Active Directory 域中](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>后续步骤
* 了解如何[将 Git 存储库添加到实验室](devtest-lab-add-artifact-repo.md)。
