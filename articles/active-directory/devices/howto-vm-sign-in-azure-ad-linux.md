---
title: 使用 Azure Active Directory（预览版）登录到 Azure 中的 Linux 虚拟机
description: 使用 Azure AD 登录到运行 Linux 的 Azure VM
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 07/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sandeo
ms.custom: references_regions, devx-track-azurecli, subject-rbac-steps
ms.collection: M365-identity-device-management
ms.openlocfilehash: 996c82b428c01ce9f598fbf8e35e2fb664ef8763
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601950"
---
# <a name="preview-login-to-a-linux-virtual-machine-in-azure-with-azure-active-directory-using-ssh-certificate-based-authentication"></a>预览版：通过基于 SSH 证书的身份验证使用 Azure Active Directory 登录到 Azure 中的 Linux 虚拟机

若要改进 Azure 中 Linux 虚拟机 (VM) 的安全性，可以与 Azure Active Directory (Azure AD) 身份验证集成。 现在可以将 Azure AD 用作核心身份验证平台和证书颁发机构，使用基于 AD 和 SSH 证书的身份验证通过 SSH 登录到 Linux VM。 通过此功能，组织可集中控制和强制实施 Azure 基于角色的访问控制 (RBAC) 和条件访问策略，用于管理对 VM 的访问。 本文介绍如何创建和配置 Linux VM，以及如何通过基于 SSH 证书的身份验证使用 Azure AD 登录。

> [!IMPORTANT]
> 此功能目前以公共预览版提供。 [使用设备代码流的上一版本将在 2021 年 8 月 15 日弃用](../../virtual-machines/linux/login-using-aad.md)。 若要从旧版本迁移到此版本，请参阅[从上一预览版迁移](#migration-from-previous-preview)部分。
> 此预览版没有附带服务级别协议，建议不要用于生产工作负载。 某些功能可能不受支持或者受限。 请在测试后就丢弃的测试性虚拟机上使用此功能。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

使用 Azure AD 通过基于 SSH 证书的身份验证登录到 Azure 中的 Linux VM 有许多安全优势，包括：

- 使用 Azure AD 凭据登录到 Azure Linux VM。
- 获取基于 SSH 密钥的身份验证，无需向用户分发 SSH 密钥，或在部署的任何 Azure Linux VM 上预配 SSH 公钥。 相比需要担心过时 SSH 公钥蔓延而可能导致未经授权的访问，这种体验要简单得多。
- 减少对本地管理员帐户、凭据盗用和弱凭据的依赖。
- 针对 Azure AD 配置的密码复杂性和密码生存期策略也有助于维护 Linux VM 安全性。
- 借助 Azure 基于角色的访问控制，可指定能够以常规用户或管理员特权用户身份登录到 VM 的人员。 当用户加入或离开你的团队时，你可以更新 VM 的 Azure RBAC 策略，根据需要授予访问权限。 员工在离开你的组织时，其用户帐户会被禁用或从 Azure AD 中删除，然后他们就再也不能访问你的资源。
- 借助条件访问，可配置策略以要求执行多重身份验证和/或要求用于 SSH 的客户端设备是受管理设备（例如：符合标准的设备或已建立混合 Azure AD 联接），然后才能通过 SSH 登录 Linux VM。 
- 使用 Azure 部署和审核策略以要求使用 Azure AD 登录 Linux VM，并在 VM 上标记使用未经批准的本地帐户。
- 使用 Azure Active Directory 登录 Linux VM 也适用于使用联合身份验证服务的客户。

## <a name="supported-linux-distributions-and-azure-regions"></a>受支持的 Linux 发行版和 Azure 区域

下面是目前（此功能部署在受支持区域时的预览期间）受支持的 Linux 发行版：

| 分发 | 版本 |
| --- | --- |
| CentOS | CentOS 7、CentOS 8.3 |
| Debian | Debian 9、Debian 10 |
| OpenSUSE | openSUSE Leap 42.3 |
| RedHat Enterprise Linux 7 | RHEL 7.4 至 RHEL 7.10、RHEL 8.3 |
| SUSE Linux Enterprise Server | SLES 12 |
| Ubuntu Server | Ubuntu Server 16.04 至 Ubuntu Server 20.04 |

下面是目前（此功能的预览期间）受支持的 Azure 区域：

- Azure 全球

> [!Note]
> 到 2021 年 6 月，Azure 政府和 Azure 中国将支持此功能的预览版。
 
不支持在 Azure Kubernetes 服务 (AKS) 群集上使用此扩展。 有关详细信息，请参阅 [AKS 的支持策略](../../aks/support-policies.md)。

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.22.1 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

## <a name="requirements-for-login-with-azure-ad-using-ssh-certificate-based-authentication"></a>使用基于 SSH 证书的身份验证通过 Azure AD 登录的要求

若要使用基于 SSH 证书的身份验证通过 Azure AD 登录 Azure 中的 Linux VM，需要确保满足以下网络、虚拟机和客户端（ssh 客户端）要求。

### <a name="network"></a>网络

VM 网络配置必须允许通过 TCP 端口 443 对以下终结点进行出站访问：

Azure 全球

- `https://packages.microsoft.com` - 用于包安装和升级。
- `http://169.254.169.254` - Azure 实例元数据服务终结点。
- `https://login.microsoftonline.com` - 用于基于 PAM（可插入身份验证模块）的身份验证流。
- `https://pas.windows.net` - 用于 Azure RBAC 流。

Azure 政府

- `https://packages.microsoft.com` - 用于包安装和升级。
- `http://169.254.169.254` - Azure 实例元数据服务终结点。
- `https://login.microsoftonline.us` - 用于基于 PAM（可插入身份验证模块）的身份验证流。
- `https://pasff.usgovcloudapi.net` - 用于 Azure RBAC 流。

Azure 中国

- `https://packages.microsoft.com` - 用于包安装和升级。
- `http://169.254.169.254` - Azure 实例元数据服务终结点。
- `https://login.chinacloudapi.cn` - 用于基于 PAM（可插入身份验证模块）的身份验证流。
- `https://pas.chinacloudapi.cn` - 用于 Azure RBAC 流。

### <a name="virtual-machine"></a>虚拟机

确保 VM 配置了以下功能：

- 系统分配的托管标识。 使用 Azure 门户创建 VM 并选择 Azure AD 登录选项时，会自动选择此选项。 还可使用 Azure CLI 在新的或现有的 VM 上启用系统分配的托管标识。
- aadsshlogin 和 aadsshlogin-selinux（视情况而定）。 这些包与 AADSSHLoginForLinux VM 扩展一起安装。 使用 Azure 门户创建 VM 并启用 Azure AD 登录（“管理”选项卡）或通过 Azure CLI 时，将安装扩展。

### <a name="client"></a>客户端

确保客户端满足以下要求：

- SSH 客户端必须支持使用基于 OpenSSH 的证书进行身份验证。 可以配合使用 Az CLI（2.21.1 或更高版本）和 OpenSSH（包含在 Windows 10 版本 1803 或更高版本）或使用 Azure Cloud Shell 来满足此要求。 
- 适用于 Az CLI 的 SSH 扩展。 可使用 `az extension add --name ssh` 安装。 使用 Azure Cloud Shell 时，无需安装此扩展，因为它已预安装。
- 如果使用 Az CLI 或支持 OpenSSH 证书的 Azure Cloud Shell 以外的任何其他 SSH 客户端，则仍需使用具有 SSH 扩展的 Az CLI 检索临时 SSH 证书以及可选的配置文件，并将配置文件用于 SSH 客户端。
- 客户端到 VM 公共或专用 IP 的 TCP 连接（也可以使用 ProxyCommand 或 SSH 转发到已连接的计算机）。

## <a name="enabling-azure-ad-login-in-for-linux-vm-in-azure"></a>为 Azure 中的 Linux VM 启用 Azure AD 登录

若要对 Azure 中的 Linux VM 使用 Azure AD 登录，需要首先为 Linux VM 启用 Azure AD 登录选项，为有权登录到 VM 的用户配置 Azure 角色分配，然后使用支持 OpensSSH 的 SSH 客户端（如 Az CLI 或 Az Cloud Shell）通过 SSH 登录到 Linux VM。 可以通过多种方式为 Linux VM 启用 Azure AD 登录，例如可以使用：

- 创建 Linux VM 时的 Azure 门户体验
- 创建 Windows VM 时的 Azure Cloud Shell 体验或现有 Linux VM 的 Azure Cloud Shell 体验

### <a name="using-azure-portal-create-vm-experience-to-enable-azure-ad-login"></a>使用 Azure 门户创建 VM 体验以启用 Azure AD 登录

可以使用 Azure 门户为上述任何受支持的 Linux 分发版启用 Azure AD 登录。

例如，若要在 Azure 中使用 Azure AD 登录创建 Ubuntu Server 18.04 LTS VM：

1. 使用有权创建 VM 的帐户登录到 Azure 门户，然后选择“+ 创建资源”。
1. 在“常用”视图中，单击“Ubuntu Server 18.04 LTS”下的“创建”。
1. 在“管理”选项卡上， 
   1. 选中该复选框以启用“使用 Azure Active Directory（预览版）登录”。
   1. 确保选中“系统分配的托管标识”。
1. 完成创建虚拟机的其余体验。 在此预览版中，必须使用用户名和密码/SSH 公钥创建管理员帐户。
 
### <a name="using-the-azure-cloud-shell-experience-to-enable-azure-ad-login"></a>使用 Azure Cloud Shell 体验以启用 Azure AD 登录

Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的操作步骤。 Cloud Shell 中预安装并配置了常用的 Azure 工具以供你的帐户使用。 只需要选择“复制”按钮来复制代码，将其粘贴到 Cloud Shell 中，然后按 Enter 键来运行它。 可通过多种方式打开 Cloud Shell：

- 选择代码块右上角的“试用”。
- 在浏览器中打开 Cloud Shell。
- 选择 Azure 门户右上角菜单上的“Cloud Shell”按钮。

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.22.1 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅安装 Azure CLI 一文。

1. 使用 [az group create](/cli/azure/group#az_group_create) 创建资源组。
1. 使用受支持区域中受支持的发行版通过 [az vm create](/cli/azure/vm#az_vm_create&preserve-view=true) 创建 VM。
1. 使用 [az vm 扩展集](/cli/azure/vm/extension?view=azure-cli-latest#az_vm_extension_set&preserve-view=true)安装 Azure AD 登录 VM 扩展。

以下示例使用“Ubuntu 18.04 LTS”将名为“myVM”的 VM 部署到“southcentralus”区域中名为“AzureADLinuxVMPreview”的资源组中。 然后，安装“Azure AD 登录 VM 扩展”，以便为 Linux VM 启用 Azure AD 登录。 VM 扩展是小型应用程序，可在 Azure 虚拟机上提供部署后配置和自动化任务。

可根据需要自定义该示例以支持测试要求。

```azurecli-interactive
az group create --name AzureADLinuxVMPreview --location southcentralus

az vm create \
    --resource-group AzureADLinuxVMPreview \
    --name myVM \   
    --image UbuntuLTS \
    --assign-identity \
    --admin-username azureuser \
    --generate-ssh-keys

az vm extension set \
    --publisher Microsoft.Azure.ActiveDirectory \
    --name AADSSHLoginForLinux \
    --resource-group AzureADLinuxVMPreview \
    --vm-name myVM
```

创建 VM 和支持资源需要几分钟时间。

可使用运行中的 VM 代理在现有（受支持的分发版）Linux VM 上安装 AADSSHLoginForLinux 扩展，以启用 Azure AD 身份验证。 如果将此扩展部署到以前创建的 VM，请确保至少为该计算机分配了 1 GB 内存，否则无法安装该扩展。

在 VM 上成功安装扩展后，会显示 provisioningState 为 Succeeded。 VM 必须有一个正在运行的 [VM 代理](../../virtual-machines/extensions/agent-linux.md)来安装扩展。

## <a name="configure-role-assignments-for-the-vm"></a>为 VM 配置角色分配

现在已创建 VM，你需要配置 Azure RBAC 策略来确定可登录到 VM 的人员。 使用两个 Azure 角色来授权 VM 登录：

- **虚拟机管理员登录**：分配了此角色的用户可以使用管理员权限登录到 Azure 虚拟机。
- **虚拟机用户登录名**：分配了此角色的用户可以使用常规用户权限登录到 Azure 虚拟机。
 
若要允许用户通过 SSH 登录到 VM，必须分配“虚拟机管理员登录”或“虚拟机用户登录”角色。 分配了 VM“所有者”或“参与者”角色的 Azure 用户不会自动获得通过 SSH 使用 Azure AD 登录到 VM 的权限。 这是为了在控制虚拟机的用户群与可访问虚拟机的用户群之间形成经过审核的分隔。 

可以通过多种方式为 VM 配置角色分配，例如，可以使用：

- Azure AD 门户体验
- Azure Cloud Shell 体验

> [!Note]
> “虚拟机管理员登录”和“虚拟机用户登录”角色使用 dataActions，因此无法在管理组范围内进行分配。 目前只能在订阅、资源组或资源范围中分配这些角色。 建议在订阅或资源级别而不是在单个 VM 级别分配角色，以避免每个订阅的 [Azure 角色分配限制](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit)达到上限的风险。

### <a name="using-azure-ad-portal-experience"></a>使用 Azure AD 门户体验

若要为启用 Azure AD 的 Linux VM 配置角色分配，请执行以下操作：

1. 选择“访问控制 (IAM)”。

1. 选择“添加” > “添加角色分配”，打开“添加角色分配”页面 。

1. 分配以下角色。 有关详细步骤，请参阅[使用 Azure 门户分配 Azure 角色](../../role-based-access-control/role-assignments-portal.md)。
    
    | 设置 | 值 |
    | --- | --- |
    | 角色 | 虚拟机管理员登录或虚拟机用户登录 |
    | 将访问权限分配到 | 用户、组、服务主体或托管标识 |

    ![Azure 门户中的“添加角色分配”页。](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

片刻之后，会在所选范围内为安全主体分配角色。
 
### <a name="using-the-azure-cloud-shell-experience"></a>使用 Azure Cloud Shell 体验

以下示例使用 [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) 为当前的 Azure 用户分配登录到 VM 所需的“虚拟机管理员登录名”角色。 当前 Azure 帐户的用户名是使用 [az account show](/cli/azure/account#az_account_show) 获得的，而 scope 则设置为在前面的步骤中使用 [az vm show](/cli/azure/vm#az_vm_show) 创建的 VM。 也可在资源组或订阅级别设置范围，这种情况下会应用正常的 Azure RBAC 继承权限。

```azurecli-interactive
username=$(az account show --query user.name --output tsv)
vm=$(az vm show --resource-group AzureADLinuxVMPreview --name myVM --query id -o tsv)

az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee $username \
    --scope $vm
```

> [!NOTE]
> 如果 Azure AD 域和登录用户名域不匹配，则必须使用 `--assignee-object-id` 指定用户帐户的对象 ID，而不是仅指定 `--assignee` 的用户名。 可以使用 [az ad user list](/cli/azure/ad/user#az_ad_user_list) 获取用户帐户的对象 ID。

有关如何使用 Azure RBAC 管理对 Azure 订阅资源的访问的详细信息，请参阅文章[分配 Azure 角色的步骤](../../role-based-access-control/role-assignments-steps.md)：

## <a name="install-ssh-extension-for-az-cli"></a>安装用于 Az CLI 的 SSH 扩展

如果使用 Azure Cloud Shell，则无需进行其他设置，因为 Az CLI 的最低所需版本和 Az CLI 的 SSH 扩展均已包含在 Cloud Shell 环境中。

运行以下命令以添加用于 Az CLI 的 SSH 扩展

```azurecli
az extension add --name ssh
```

扩展所需的最低版本为 0.1.4。 使用以下命令检查已安装的 SSH 扩展版本。

```azurecli
az extension show --name ssh
```

## <a name="using-conditional-access"></a>使用条件访问

可以强制实施条件访问策略，例如要求用户执行 MFA，要求运行 SSH 客户端的设备符合要求/已建立混合 Azure AD 联接，在授权访问 Azure 中使用 Azure AD 登录启用的 Linux VM 之前，检查用户和登录是否为低风险。 

若要应用条件访问策略，必须从云应用或操作分配选项中选择“Azure Linux VM 登录”应用，然后在满足要求进行多重身份验证和/或要求设备符合标准/已建立混合 Azure AD 联接的要求后，使用用户和/或登录风险作为条件，使用访问控制作为授予访问权限。

> [!NOTE]
> 要求设备符合标准或在运行 SSH 客户端的客户端设备上建立混合 Azure AD 联接的条件访问策略实施仅适用于在 Windows 和 macOS 上运行的 Az CLI。 在 Linux 或 Azure Cloud Shell 上使用 Az CLI 时不受支持。

## <a name="login-using-azure-ad-user-account-to-ssh-into-the-linux-vm"></a>使用 Azure AD 用户帐户登录以通过 SSH 登录到 Linux VM

### <a name="using-az-cli"></a>使用 Az CLI

首先执行 az login，然后执行 az ssh vm。

```azurecli
az login 
```

此命令将启动浏览器窗口，用户可以使用 Azure AD 帐户登录。 

以下示例自动解析 VM 的适当 IP 地址。

```azurecli
az ssh vm -n myVM -g AzureADLinuxVMPreview
```

根据系统提示，在登录页上输入 Azure AD 登录凭据，执行 MFA，和/或检查设备情况。 仅当 az CLI 会话未满足任何所需的条件访问条件时，才会出现提示。 关闭浏览器窗口，返回到 SSH 提示，将自动连接到 VM。

现在，你已使用分配的角色权限（例如“VM 用户”或“VM 管理员”）登录到 Azure Linux 虚拟机。 如果用户帐户分配了“虚拟机管理员登录”角色，则可使用 sudo 运行需要 root 特权的命令。

### <a name="using-az-cloud-shell"></a>使用 Az Cloud Shell

可以使用 Az Cloud Shell 连接到 VM，无需在本地将任何内容安装到客户端计算机。 单击 Azure 门户右上角的 shell 图标，可启动 Cloud Shell。
 
Az Cloud Shell 会自动连接到已登录用户的上下文中的会话。 在 Linux Azure AD 登录预览期间，必须再次运行 az login，并通过交互式登录流。

```azurecli
az login
```

然后，可以运行普通 az ssh vm 命令，使用 VM 的名称、资源组或 IP 地址进行连接。

```azurecli
az ssh vm -n myVM -g AzureADLinuxVMPreview
```

> [!NOTE]
> 使用 Az Cloud Shell 时，不支持实施要求设备符合标准或建立混合 Azure AD 联接的条件访问策略。

### <a name="login-using-azure-ad-service-principal-to-ssh-into-the-linux-vm"></a>使用 Azure AD 服务主体通过 SSH 登录到 Linux VM

Azure CLI 支持使用服务主体而不是用户帐户进行身份验证。 由于服务主体帐户不关联到任何特定用户，因此客户可以使用它们通过 SSH 登录到 VM，为可能有的任何自动化方案提供支持。 必须为服务主体分配 VM 管理员或 VM 用户权限。 可在订阅或资源组级别分配权限。 

以下示例在资源组级别向服务主体分配 VM 管理员权限。 替换服务主体“对象 ID”、“订阅 ID”和“资源组名称”字段。

```azurecli
az role assignment create \
    --role "Virtual Machine Administrator Login" \
    --assignee-object-id <service-principal-objectid> \
    --assignee-principal-type ServicePrincipal \
    --scope “/subscriptions/<subscription-id>/resourceGroups/<resourcegroup-name>"
```

用以下示例通过服务主体对 Azure CLI 进行身份验证。 若要了解有关使用服务主体登录的详细信息，请参阅[使用服务主体登录到 Azure CLI](/cli/azure/authenticate-azure-cli#sign-in-with-a-service-principal) 一文。

```azurecli
az login --service-principal -u <sp-app-id> -p <password-or-cert> --tenant <tenant-id>
```

使用服务主体完成身份验证后，请运行普通 Az CLI SSH 命令连接到 VM。

```azurecli
az ssh vm -n myVM -g AzureADLinuxVMPreview
```

### <a name="exporting-ssh-configuration-for-use-with-ssh-clients-that-support-openssh"></a>导出 SSH 配置以用于支持 OpenSSH 的 SSH 客户端

使用 Azure AD 登录到 Azure Linux VM 支持导出 OpenSSH 证书和配置，从而允许使用支持基于 OpenSSH 的证书的任何 SSH 客户端登录 Azure AD。 以下示例导出分配给 VM 的所有 IP 地址的配置。

```azurecli
az ssh config --file ~/.ssh/config -n myVM -g AzureADLinuxVMPreview
```

或者，也可通过只指定 IP 地址来导出配置。 将示例中的 IP 地址替换为 VM 的公共或专用 IP 地址（必须自行连接专用 IP）。 键入 `az ssh config -h` 获取有关此命令的帮助。

```azurecli
az ssh config --file ~/.ssh/config --ip 10.11.123.456
```

然后，可以通过正常使用 OpenSSH 连接到 VM。 可以通过使用 OpenSSH 的任何 SSH 客户端进行连接。

## <a name="sudo-and-azure-ad-login"></a>Sudo 和 Azure AD 登录

用户一旦将 VM 管理员角色通过 SSH 成功分配到 Linux VM，他们就能够运行 sudo，无需进行其他交互或身份验证。 分配了 VM 用户角色的用户将无法运行 sudo。

## <a name="virtual-machine-scale-set-support"></a>虚拟机规模集支持

支持虚拟机规模集，但启用和连接到虚拟机规模集 VM 的步骤略有不同。

首先，创建虚拟机规模集或选择已存在的规模集。 为虚拟机规模集启用系统分配的托管标识。

```azurecli
az vmss identity assign --vmss-name myVMSS --resource-group AzureADLinuxVMPreview
```

在虚拟机规模集上安装 Azure AD 扩展。

```azurecli
az vmss extension set --publisher Microsoft.Azure.ActiveDirectory --name Azure ADSSHLoginForLinux --resource-group AzureADLinuxVMPreview --vmss-name myVMSS
```

虚拟机规模集通常没有公共 IP 地址，因此必须从另一台能够访问其 Azure 虚拟网络的计算机连接到它们。 此示例演示如何使用虚拟机规模集 VM 的专用 IP 从同一虚拟网络中的计算机进行连接。 

```azurecli
az ssh vm --ip 10.11.123.456
```

> [!NOTE]
> 不能使用 `--resource-group` 和 `--name` 交换机自动确定虚拟机规模集 VM 的 IP 地址。

## <a name="migration-from-previous-preview"></a>从先前的预览版迁移

对于使用基于设备代码流的面向 Linux 的早期版本 Azure AD 登录的客户，请完成以下步骤。

1. 在 VM 上卸载 AADLoginForLinux 扩展。
   1. 使用 Azure CLI：`az vm extension delete -g MyResourceGroup --vm-name MyVm -n AADLoginForLinux`
1. 在 VM 上启用系统分配的托管标识。
   1. 使用 Azure CLI：`az vm identity assign -g myResourceGroup -n myVm`
1. 在 VM 上安装 AADSSHLoginForLinux 扩展
   1. 使用 Azure CLI：
      ```azurecli
      az vm extension set \
                --publisher Microsoft.Azure.ActiveDirectory \
                --name AADSSHLoginForLinux \
                --resource-group myResourceGroup \
                --vm-name myVM
      ```
## <a name="using-azure-policy-to-ensure-standards-and-assess-compliance"></a>使用 Azure Policy 来确保符合标准并评估合规性

使用 Azure Policy 确保为新的和现有的 Linux 虚拟机启用 Azure AD 登录，并在 Azure Policy 符合性仪表板上大规模评估环境的符合性。 通过此功能，可以使用多个级别的强制：可以标记环境中未启用 Azure AD 登录的新的和现有的 Linux VM。 还可以使用 Azure Policy 在未启用 Azure AD 登录的新 Linux VM 上部署 Azure AD 扩展，以及将现有 Linux VM 恢复为相同标准。 除这些功能以外，还可以使用 Azure Policy 来检测并标记在其计算机上创建了未批准的本地帐户的 Linux VM。 若要了解详细信息，请参阅 [Azure Policy](../../governance/policy/overview.md)。

## <a name="troubleshoot-sign-in-issues"></a>排查登录问题

尝试使用 Azure AD 凭据进行 SSH 登录时，会出现一些常见的错误，其中包括：未分配 Azure 角色、系统重复提示登录。 请根据以下部分的说明来纠正这些问题。

### <a name="could-not-retrieve-token-from-local-cache"></a>无法从本地缓存中检索令牌

必须再次运行 az login，并通过交互式登录流。 查看[使用 Az Cloud Shell](#using-az-cloud-shell) 部分。

### <a name="access-denied-azure-role-not-assigned"></a>拒绝访问：未分配 Azure 角色

如果在 SSH 提示符窗口中看到以下错误，请验证是否已为授予用户虚拟机管理员登录名或虚拟机用户登录名角色的 VM 配置 Azure RBAC 策略。 如果在 Azure 角色分配方面遇到问题，请参阅[排查 Azure RBAC 的问题](../../role-based-access-control/troubleshooting.md#azure-role-assignments-limit)文章。

### <a name="extension-install-errors"></a>扩展安装错误

将 AADSSHLoginForLinux VM 扩展安装到现有计算机失败，并出现以下已知错误代码之一：

#### <a name="non-zero-exit-code-22"></a>非零退出代码：22

AADSSHLoginForLinux VM 扩展的状态在门户中显示为“正在转换”。

原因 1：此失败是由于所需系统分配的托管标识导致的。

解决方案 1：执行以下操作：

1. 卸载失败的扩展。
1. 在 Azure VM 上启用系统分配的托管标识。
1. 再次运行扩展安装命令。

#### <a name="non-zero-exit-code-23"></a>非零退出代码：23

AADSSHLoginForLinux VM 扩展的状态在门户中显示为“正在转换”。

原因 1：此失败是由于仍然安装了较旧的 AADLoginForLinux VM 扩展导致的。

解决方案 1：执行以下操作：

1. 从 VM 中卸载较旧的 AADLoginForLinux VM 扩展。 新 AADSSHLoginForLinux VM 扩展的状态将在门户中更改为“预配成功”。

#### <a name="az-ssh-vm-fails-with-keyerror-access_token"></a>Az ssh vm 失败，并发生 KeyError：“access_token”。

原因 1：使用的 Azure CLI 客户端的版本已过时。

解决方案 1：将 Azure CLI 客户端升级到 2.21.0 或更高版本。

#### <a name="ssh-connection-closed"></a>SSH 连接已关闭

用户使用 az login 成功登录后，使用 `az ssh vm -ip <addres>` 或 `az ssh vm --name <vm_name> -g <resource_group>` 连接到 VM 失败，并出现消息“连接被 <ip_address> 端口 22 关闭”。

原因 1：用户未被分配到此 VM 范围内的虚拟机管理员/用户登录 Azure RBAC 角色。

解决方案 1：将用户添加到此 VM 范围内的虚拟机管理员/用户登录 Azure RBAC 角色。

原因 2：用户在所需的 Azure RBAC 角色中，但已在 VM 上禁用系统分配的托管标识。

解决方案 2：执行以下操作：

1. 在 VM 上启用系统分配的托管标识。
1. 等待几分钟时间尝试使用 `az ssh vm --ip <ip_address>` 连接。

### <a name="virtual-machine-scale-set-connection-issues"></a>虚拟机规模集连接问题

如果虚拟机规模集实例正在运行旧模型，则虚拟机规模集 VM 连接可能会失败。 将虚拟机规模集实例升级到最新模型可能会解决问题，尤其是在安装 Azure AD 登录名扩展后一直未进行升级时。 升级实例会将标准虚拟机规模集配置应用于单个实例。

## <a name="preview-feedback"></a>预览功能反馈

在 [Azure AD 反馈论坛](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)上分享此预览功能的反馈或报告使用时存在的问题。

## <a name="next-steps"></a>后续步骤
