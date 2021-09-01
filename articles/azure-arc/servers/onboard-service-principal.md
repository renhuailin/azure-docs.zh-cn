---
title: 将混合计算机大规模连接到 Azure
description: 本文介绍如何使用已启用 Azure Arc 的服务器通过服务主体将计算机连接到 Azure。
ms.date: 08/17/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fb78440bf03970616d3d608dcea2de1dc86681bd
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323304"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>将混合计算机大规模连接到 Azure

用户可以根据自己的需求，使用多个灵活的选项，为环境中的多个 Windows 或 Linux 计算机启用已启用 Azure Arc 的服务器。 使用我们提供的模板脚本，可以自动完成每个安装步骤，包括与 Azure Arc 建立连接。但是，必须使用在目标计算机和 Azure 中拥有提升权限的帐户以交互方式执行此脚本。

如果要将计算机连接到已启用 Azure Arc 的服务器，可以使用 Azure Active Directory [服务主体](../../active-directory/develop/app-objects-and-service-principals.md)，而不要使用特权身份[以交互方式连接计算机](onboard-portal.md)。 服务主体是一种特殊的受限管理标识，它只被授予了使用 `azcmagent` 命令将计算机连接到 Azure 所需的最低权限。 这比使用较高特权的帐户（例如租户管理员）更安全，并且可以遵循我们的访问控制安全性最佳做法。 服务主体只会在加入期间使用，不会用于任何其他目的。  

安装和配置 Connected Machine 代理的安装方法要求你在计算机上拥有管理员权限。 在 Linux 上，需使用 root 帐户；在 Windows 上，需要以“本地管理员组”的成员身份使用这些方法。

在开始之前，请务必查看[先决条件](agent-overview.md#prerequisites)，并验证你的订阅和资源是否符合要求。 有关支持的区域和其他相关注意事项的信息，请参阅[支持的 Azure 区域](overview.md#supported-regions)。 另请查看我们的《[大规模规划指南](plan-at-scale-deployment.md)》，以了解设计和部署标准，以及我们的管理和监视建议。  

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>创建服务主体以用于大规模加入

可以在 [Azure PowerShell](/powershell/azure/install-az-ps) 中使用 [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) cmdlet 创建服务主体。 或者，可以按照[使用 Azure 门户创建服务主体](../../active-directory/develop/howto-create-service-principal-portal.md)中列出的步骤完成此任务。

> [!NOTE]
> 创建服务主体之前，用户的帐户必须是要用于加入的订阅中的“所有者”或“用户访问管理员”角色的成员。  如果没有足够的权限配置角色分配，则可能会创建服务主体，但它将无法加入计算机。
>

如果要使用 PowerShell 创建服务主体，请执行以下步骤。

1. 运行以下命令。 必须在变量中存储 [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) cmdlet 的输出，否则无法检索需要在后续步骤中使用的密码。

    ```azurepowershell-interactive
    $sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
    $sp
    ```

    ```output
    Secret                : System.Security.SecureString
    ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
    ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
    ObjectType            : ServicePrincipal
    DisplayName           : Hybrid-RP
    Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
    Type                  :
    ```

2. 若要检索 `$sp` 变量中存储的密码，请运行以下命令：

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. 在输出中，找到并复制 **password** 字段下的密码值。 另外，还请找到并复制 **ApplicationId** 字段下的值。 请在安全的位置保存这些值，供稍后使用。 如果忘记或丢失了服务主体密码，可以使用 [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) cmdlet 重置它。

以下属性中的值将与传递给 `azcmagent` 的参数配合使用：

* **ApplicationId** 属性中的值用作 `--service-principal-id` 参数值
* **password** 属性中的值用作连接代理时所用的 `--service-principal-secret` 参数。

> [!NOTE]
> 请确保使用服务主体 **ApplicationId** 属性，而不是 **Id** 属性。
>

“Azure Connected Machine 加入”角色只包含加入计算机时所需的权限。 可以分配服务主体权限，以允许其范围包含资源组或订阅。 如果要添加角色分配，请参阅“[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)”或“[使用 Azure CLI 分配 Azure 角色](../../role-based-access-control/role-assignments-cli.md)”。

## <a name="generate-the-installation-script-from-the-azure-portal"></a>从 Azure 门户生成安装脚本

Azure 门户中提供了用于自动下载和安装以及与 Azure Arc 建立连接的脚本。 如果要完成该过程，请执行以下步骤：

1. 在浏览器中转到 [Azure 门户](https://portal.azure.com)。

1. 在“服务器 - Azure Arc”页上，选择左上角的“添加” 。

1. 在“选择方法”页上，选择“添加多个服务器”磁贴，然后选择“生成脚本”  。

1. 在“生成脚本”页上，选择你要在 Azure 中管理的计算机所在的订阅和资源组。 选择要将计算机元数据存储到的 Azure 位置。 此位置可以与资源组的位置相同或不同。

1. 在“先决条件”页上查看信息，然后选择“下一页: 资源详细信息”。

1. 在“资源详细信息”页上，提供以下内容：

    1. 在“资源组”下拉列表中，选择要从中管理计算机的资源组。
    1. 在“区域”下拉列表中，选择用于存储服务器元数据的 Azure 区域。
    1. 在“操作系统”下拉列表中，选择脚本配置为要在其上运行的操作系统。
    1. 如果计算机是通过代理服务器连接到 Internet 进行通信的，请指定计算机用来与代理服务器通信的代理服务器 IP 地址或名称以及端口号。 使用此配置，代理使用 HTTP 协议通过代理服务器进行通信。 按格式 `http://<proxyURL>:<proxyport>` 输入值。
    1. 选择“下一步: 身份验证”。

1. 在“身份验证”页上的“服务主体”下拉列表下，选择“Arc-for-servers”。    然后，选择“下一步: 标签”。

1. 在“标记”页上，查看建议的默认“物理位置标记”并输入值，或指定一个或多个“自定义标记”以支持你的标准  。

1. 在完成时选择“下一步:下载并运行脚本。

1. 在“下载并运行脚本”页上查看摘要信息，然后选择“下载” 。 如果仍需进行更改，请选择“上一页”。

对于 Windows，系统会提示用户将 `OnboardingScript.ps1` 保存到计算机，对于 Linux，则提示将 `OnboardingScript.sh` 保存到计算机。

## <a name="install-the-agent-and-connect-to-azure"></a>安装代理并连接到 Azure

使用先前创建的脚本模板，可以使用组织首选的自动化工具在多台混合 Linux 和 Windows 计算机上安装和配置 Connected Machine 代理。 此脚本执行“[从 Azure 门户将混合计算机连接到 Azure](onboard-portal.md)”文章中所述的类似步骤。 不同之处在于，最后一步是通过 `azcmagent` 命令使用服务主体与 Azure Arc 建立连接。

下面是配置用于服务主体的 `azcmagent` 命令时需要指定的设置。

* `service-principal-id`：唯一标识符 (GUID)，表示服务主体的应用程序 ID。
* `service-principal-secret` | 服务主体密码。
* `tenant-id`：表示 Azure AD 专用实例的唯一标识符 (GUID)。
* `subscription-id`：计算机要属于的 Azure 订阅的订阅 ID (GUID)。
* `resource-group`：连接的计算机要属于的资源组的名称。
* `location`：请参阅[支持的 Azure 区域](overview.md#supported-regions)。 此位置可以与资源组的位置相同或不同。
* `resource-name`：（可选）用于本地计算机的 Azure 资源表示。 如果未指定此值，将使用计算机主机名。

若要详细了解 `azcmagent` 命令行工具，请查看 [Azcmagent 参考](./manage-agent.md)。

>[!NOTE]
>仅支持从 64 位版本的 Windows PowerShell 中运行 Windows PowerShell 脚本。
>

安装代理并将其配置为连接到启用了 Azure Arc 的服务器后，请转到 Azure 门户，验证是否已成功连接服务器。 在 [Azure 门户](https://aka.ms/hybridmachineportal)中查看计算机。

![服务器连接成功](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>后续步骤

- 在 [Connected Machine 代理故障排除指南](troubleshoot-agent-onboard.md)中可以找到故障排除信息。

- 查看[规划和部署指南](plan-at-scale-deployment.md)，以便对按任意规模部署启用了 Azure Arc 的服务器进行规划，并实现集中管理和监视。

- 了解如何使用 [Azure Policy](../../governance/policy/overview.md) 管理计算机，例如，进行 VM [来宾配置](../../governance/policy/concepts/guest-configuration.md)、验证计算机是否向预期的 Log Analytics 工作区报告、使用 [VM 见解](../../azure-monitor/vm/vminsights-enable-policy.md)启用监视，等等。
