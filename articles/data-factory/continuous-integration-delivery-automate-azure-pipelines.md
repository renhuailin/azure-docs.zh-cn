---
title: 自动执行持续集成
description: 了解如何在 Azure 数据工厂中通过 Azure Pipelines 管道发布自动进行持续集成。
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2c32b2e986e50fd2679b65b449eccdf5c7629a9a
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219234"
---
# <a name="automate-continuous-integration-using-azure-pipelines-releases"></a>使用 Azure Pipelines 发布自动进行持续集成

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

下面是有关设置 Azure Pipelines 发布以自动将数据工厂部署到多个环境的指导。

## <a name="requirements"></a>要求

-   一个已链接到 Visual Studio Team Foundation Server 或 Azure Repos 并使用[Azure 资源管理器服务终结点](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)的 Azure 订阅。

-   一个配置了 Azure Repos Git 集成的数据工厂。

-   一个 [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)，其中包含每个环境的机密。

## <a name="set-up-an-azure-pipelines-release"></a>设置 Azure Pipelines 发布

1.  在 [Azure DevOps](https://dev.azure.com/) 中，打开配置了数据工厂的项目。

1.  在页面左侧选择“管道”，然后选择“发布”。 

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image6.png" alt-text="依次选择“管道”、“发布”":::

1.  选择“新建管道”；如果有现有的管道，请依次选择“新建”、“新建发布管道”。  

1.  选择“空作业”模板。

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image13.png" alt-text="选择“空作业”":::

1.  在“阶段名称”框中，输入环境的名称。

1.  选择“添加项目”，然后选择配置了开发数据工厂的 git 存储库。 选择“默认分支”对应的存储库的[发布分支](source-control.md#configure-publishing-settings)。 默认情况下，此发布分支为 `adf_publish`。 对于“默认版本”，请选择“默认分支中的最新版本”。 

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image7.png" alt-text="添加项目":::

1.  添加 Azure 资源管理器部署任务：

    a.  在阶段视图中选择“查看阶段任务”。

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image14.png" alt-text="阶段视图":::

    b.  创建新任务。 搜索“ARM 模板部署”，然后选择“添加”。

    c.  在“部署任务”中，选择目标数据工厂的订阅、资源组和位置。 根据需要提供凭据。

    d.  在“操作”列表中，选择“创建或更新资源组”。 

    e.  选择“模板”框旁边的省略号按钮 ( **...** )。 浏览在配置的 git 存储库的发布分支中生成的 Azure 资源管理器模板。 在 adf_publish 分支的 &lt;FactoryName&gt; 文件夹中查找文件 `ARMTemplateForFactory.json`。

    f.  选择“模板参数”框旁边 的“…”，以便选择参数文件。 在 adf_publish 分支的 &gt;FactoryName&lt; 文件夹中查找文件 `ARMTemplateParametersForFactory.json`。

    g.  在“替代模板参数”框旁边 选择“...”，并输入目标数据工厂的所需参数值。 对于来自 Azure Key Vault 的凭据，请输入机密名称并以双引号将其括住。 例如，如果机密名称为 cred1，请为此值输入 **"$(cred1)"** 。

    h. 选择“增量”作为“部署模式”。 

    > [!WARNING]
    > 在完全部署模式下，资源组中已存在但尚未在新的资源管理器模板中指定的资源将被删除。 有关详细信息，请参阅 [Azure 资源管理部署模式](../azure-resource-manager/templates/deployment-modes.md)

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image9.png" alt-text="数据工厂生产部署":::

1.  保存发布管道。

1. 若要触发发布，请选择“创建发布”。 若要自动创建发布，请参阅 [Azure DevOps 发布触发器](/azure/devops/pipelines/release/triggers)

   :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image10.png" alt-text="选择“创建发布”":::

> [!IMPORTANT]
> 在 CI/CD 方案中，不同环境中的集成运行时 (IR) 类型必须相同。 例如，如果在开发环境中使用自承载 IR，则同一 IR 在测试和生产等其他环境中的类型也必须是自承载。 同样，如果跨多个阶段共享集成运行时，则必须在所有环境（例如开发、测试和生产）中将集成运行时配置为链接自承载。

## <a name="get-secrets-from-azure-key-vault"></a>获取 Azure Key Vault 中的机密

如果要在 Azure 资源管理器模板中传入机密，建议你将 Azure Key Vault 用于 Azure Pipelines 发布。

可通过两种方式来处理机密：

- 将机密添加到参数文件。 有关详细信息，请参阅[在部署过程中使用 Azure Key Vault 传递安全参数值](../azure-resource-manager/templates/key-vault-parameter.md)。

    创建上传到发布分支的参数文件的副本。 使用以下格式设置要从 Key Vault 获取的参数的值：

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    使用此方法时，会自动从密钥保管库拉取机密。

    参数文件也需位于 publish 分支中。

- 在上一部分所述的 Azure 资源管理器部署任务的前面添加一个 [Azure Key Vault 任务](/azure/devops/pipelines/tasks/deploy/azure-key-vault)：

    1.  在“任务”选项卡上创建一个新任务。 搜索并添加 **Azure Key Vault**。

    1.  在 Key Vault 任务中，选择在其中创建了密钥保管库的订阅。 根据需要提供凭据，然后选择该密钥保管库。

    :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image8.png" alt-text="添加 Key Vault 任务":::

### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>向 Azure Pipelines 代理授权

如果未设置正确的权限，Azure Key Vault 任务可能失败并出现“拒绝访问”错误。 请下载发布日志，并找到 .ps1 文件，其中包含用于向 Azure Pipelines 代理授予权限的命令。 可以直接运行该命令。 也可以从文件中复制主体 ID，然后在 Azure 门户中手动添加访问策略。 `Get` 和 `List` 是所需的最低权限。

## <a name="updating-active-triggers"></a>更新活动触发器

按[如何安装和配置 Azure PowerShell](/powershell/azure/install-Az-ps) 中的说明安装最新的 Azure PowerShell 模块。

>[!WARNING]
>如果不使用最新版的 PowerShell 和数据工厂模块，可能会在运行命令时遇到反序列化错误。 
>

如果尝试更新活动触发器，部署可能会失败。 若要更新活动触发器，需手动将其停止，并在部署后将其重启。 为此，可以使用 Azure PowerShell 任务：

1.  在此发布版本的“任务”选项卡上，添加一个 **Azure PowerShell** 任务。 选择任务版本最新的 Azure PowerShell 版本。 

1.  选择工厂所在的订阅。

1.  选择“脚本文件路径”作为脚本类型。 这需要将 PowerShell 脚本保存到存储库中。 以下 PowerShell 脚本可用于停止触发器：

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

部署后，可以完成类似的步骤（使用 `Start-AzDataFactoryV2Trigger` 函数）来重启触发器。

数据工厂团队已提供[部署前和部署后示例脚本](continuous-integration-delivery-sample-script.md)。 

## <a name="next-steps"></a>后续步骤

- [持续集成和交付概述](continuous-integration-delivery.md)
- [手动将资源管理器模板推广到每个环境](continuous-integration-delivery-manual-promotion.md)
- [将自定义参数用于资源管理器模板](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [链接的资源管理器模板](continuous-integration-delivery-linked-templates.md)
- [使用修补程序生产环境](continuous-integration-delivery-hotfix-environment.md)
- [部署前和部署后示例脚本](continuous-integration-delivery-sample-script.md)
