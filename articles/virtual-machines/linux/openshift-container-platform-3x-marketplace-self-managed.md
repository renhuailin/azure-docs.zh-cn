---
title: 在 Azure 中部署 OpenShift Container Platform 3.11 自托管市场产品/服务
description: 在 Azure 中部署 OpenShift Container Platform 3.11 自托管市场产品/服务。
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 43c1785a80738741478e93289fd9ff239dca7719
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697793"
---
# <a name="configure-prerequisites"></a>配置先决条件

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: 灵活规模集 

在使用市场产品/服务在 Azure 中部署自托管 OpenShift Container Platform 3.11 群集之前，必须先配置几个先决条件。  有关如何创建 SSH 密钥（不含密码）、Azure 密钥保管库、密钥保管库机密和服务主体的说明，请阅读 [OpenShift 先决条件](./openshift-container-platform-3x-prerequisites.md)一文。

 
## <a name="deploy-using-the-marketplace-offer"></a>使用市场产品/服务进行部署

使用 Azure 市场产品/服务是将自托管 OpenShift Container Platform 3.11 群集部署到 Azure 中的最简单方式。

此选项虽然最简单，但自定义功能有限。 市场产品/服务部署 OpenShift Container Platform 3.11.82，并包括以下配置选项：

- **主节点**：三 (3) 个包含可配置实例类型的主节点。
- **Infra 节点**：三 (3) 个包含可配置实例类型的 Infra 节点。
- **节点**：节点数目（1 到 9 个）和实例类型可配置。
- **磁盘类型**：使用托管磁盘。
- **网络**：支持新的或现有的网络以及自定义 CIDR 范围。
- **CNS**：可以启用 CNS。
- **指标**：可以启用 Hawkular 指标。
- **日志记录**：可以启用 EFK 日志记录。
- **Azure 云提供商**：默认已启用，可以禁用。

在 Azure 门户的左上方，单击“创建资源”，在搜索框中输入“openshift container platform”，然后按 Enter。

   ![新资源搜索](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

“结果”页面将打开，列表中出现“Red Hat OpenShift Container Platform 3.11 自托管”。 

   ![新资源搜索结果](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

单击此产品/服务可查看它的详细信息。 若要部署此产品/服务，请单击“创建”。 此时将出现用于输入必需参数的 UI。 第一个屏幕是“基本信息”边栏选项卡。

   ![产品/服务标题页](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**基础知识**

若要获取有关任何输入参数的帮助，请将鼠标悬停在参数名称旁边的“i”上。

输入输入参数的值，然后单击“确定”。

| 输入参数 | 参数说明 |
|-----------------------|-----------------|
| VM 管理员用户名 | 要在所有 VM 实例上创建的管理员用户 |
| 管理员用户的 SSH 公钥 | 用于登录到 VM 的 SSH 公钥（不能包含密码） |
| 订阅 | 要将群集部署到的 Azure 订阅 |
| 资源组 | 为群集资源创建新的资源组或选择现有的空资源组 |
| 位置 | 要将群集部署到的 Azure 区域 |

   ![产品/服务“基本信息”边栏选项卡](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**基础结构设置**

输入输入参数的值，然后单击“确定”。

| 输入参数 | 参数说明 |
|-----------------------|-----------------|
| OCP 群集名称前缀 | 群集前缀，用于配置所有节点的主机名。 介于 1 到 20 个字符之间 |
| 主节点大小 | 接受默认 VM 大小，或单击“更改大小”以选择其他 VM 大小。  为工作负载选择相应的 VM 大小 |
| 基础结构节点大小 | 接受默认 VM 大小，或单击“更改大小”以选择其他 VM 大小。  为工作负载选择相应的 VM 大小 |
| 应用程序节点数 | 接受默认 VM 大小，或单击“更改大小”以选择其他 VM 大小。  为工作负载选择相应的 VM 大小 |
| 应用程序节点大小 | 接受默认 VM 大小，或单击“更改大小”以选择其他 VM 大小。  为工作负载选择相应的 VM 大小 |
| 堡垒主机大小 | 接受默认 VM 大小，或单击“更改大小”以选择其他 VM 大小。  为工作负载选择相应的 VM 大小 |
| 新虚拟网络或现有虚拟网络 | 创建新的 vNet（默认）或使用现有的 vNet |
| 选择默认 CIDR 设置或自定义 IP 范围 (CIDR) | 接受默认的 CIDR 范围，或选择“自定义 IP 范围”，然后输入自定义 CIDR 信息。  默认设置将创建 CIDR 为 10.0.0.0/14 的 vNet、CIDR 为 10.1.0.0/16 的主子网、CIDR 为 10.2.0.0/16 的基础结构子网，以及 CIDR 为 10.3.0.0/16 的计算子网和 CNS 子网 |
| 密钥保管库资源组名称 | 包含密钥保管库的资源组的名称 |
| Key Vault 名称 | 包含具有 SSH 私钥的机密的密钥保管库的名称。  只允许使用字母数字字符和短划线，并且长度必须介于 3 到 24 个字符之间 |
| 机密名称 | 包含 SSH 私钥的机密的名称。  只允许使用字母数字字符和短划线 |

   ![产品/服务基础结构边栏选项卡](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**更改大小**

若要选择其他 VM 大小，请单击“更改大小”。  VM 选择窗口随即打开。  选择所需的 VM 大小，然后单击“选择”。

   ![选择 VM 大小](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**现有虚拟网络**

| 输入参数 | 参数说明 |
|-----------------------|-----------------|
| 现有虚拟网络名称 | 现有 vNet 的名称 |
| 主节点的子网名称 | 主节点的现有子网的名称。  需要至少包含 16 个 IP 地址，并遵循 RFC 1918 |
| 基础结构节点的子网名称 | 基础结构节点的现有子网的名称。  需要至少包含 32 个 IP 地址，并遵循 RFC 1918 |
| 计算节点和 CNS 节点的子网名称 | 计算节点和 CNS 节点的现有子网的名称。  需要至少包含 32 个 IP 地址，并遵循 RFC 1918 |
| 现有虚拟网络的资源组 | 包含现有 vNet 的资源组的名称 |

   ![产品/服务基础结构的现有 vNet](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**自定义 IP 范围**

| 输入参数 | 参数说明 |
|-----------------------|-----------------|
| 虚拟网络的地址范围 | VNet 的自定义 CIDR |
| 包含主节点的子网的地址范围 | 主子网的自定义 CIDR |
| 包含基础结构节点的子网的地址范围 | 基础结构子网的自定义 CIDR |
| 包含计算节点和 CNS 节点的子网的地址范围 | 计算节点和 CNS 节点的自定义 CIDR |

   ![产品/服务基础结构自定义 IP 范围](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift 容器平台 3.11**

输入输入参数的值，然后单击“确定”

| 输入参数 | 参数说明 |
|-----------------------|-----------------|
| OpenShift 管理员用户密码 | 初始 OpenShift 用户的密码。  此用户也将是群集管理员 |
| 确认 OpenShift 管理员用户密码 | 重新键入 OpenShift 管理员用户密码 |
| Red Hat 订阅管理器用户名 | 用户名，用于访问 Red Hat 订阅或组织 ID。  此凭据用于将 RHEL 实例注册到订阅，且不会由 Microsoft 或 Red Hat 存储 |
| Red Hat 订阅管理器用户密码 | 密码，用于访问 Red Hat 订阅或激活密钥。  此凭据用于将 RHEL 实例注册到订阅，且不会由 Microsoft 或 Red Hat 存储 |
| Red Hat 订阅管理器 OpenShift 池 ID | 包含 OpenShift Container Platform 权利的池 ID。 确保有足够的 OpenShift Container Platform 权利用于安装群集 |
| 代理/主节点的 Red Hat 订阅管理器 OpenShift 池 ID | 包含代理/主节点的 OpenShift Container Platform 权利的池 ID。 确保有足够的 OpenShift Container Platform 权利用于安装群集。 如果不使用代理/主池 ID，请输入应用程序节点的池 ID |
| 配置 Azure 云提供商 | 将 OpenShift 配置为使用 Azure 云提供商。 如果将 Azure 磁盘附加用于永久性卷则需要此操作。  默认值为“是” |
| Azure AD 服务主体客户端 ID GUID | Azure AD 服务主体客户端 ID GUID（也称为 AppID）。 仅在将 Azure 云提供商设置为“是”时需要 |
| Azure AD 服务主体客户端 ID 机密 | Azure AD 服务主体客户端 ID 机密。 仅在将 Azure 云提供商设置为“是”时需要 |
 
   ![产品/服务 OpenShift 边栏选项卡](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**其他设置**

“其他设置”边栏选项卡允许对 glusterfs 存储、日志记录、指标和路由器子域进行 CNS 配置。  默认设置不安装这其中的任何选项，而是将 nip.io 用作路由器子域以进行测试。 启用 CNS 后，将安装三个额外的计算节点，其中包含将托管 glusterfs Pod 的三个附加的磁盘。  

输入输入参数的值，然后单击“确定”

| 输入参数 | 参数说明 |
|-----------------------|-----------------|
| 配置容器本机存储 (CNS) | 在 OpenShift 群集中安装 CNS，并将其作为存储启用。 如果已禁用 Azure 提供程序，则为默认值 |
| 配置群集日志记录 | 将 EFK 日志记录功能安装到群集中。  适当调整基础结构节点的大小以托管 EFK Pod |
| 配置群集的指标 | 将 Hawkular 指标安装到 OpenShift 群集中。  适当调整基础结构节点的大小以托管 Hawkular 指标 Pod |
| 默认路由器子域 | 选择“nipio”进行测试，或自定义输入自己用于生产的子域 |
 
   ![产品/服务其他边栏选项卡](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**其他设置 - 额外参数**

| 输入参数 | 参数说明 |
|-----------------------|-----------------|
| (CNS) 节点大小 | 接受默认节点大小，或选择“更改大小”选择新 VM 大小 |
| 输入自定义子域 | 自定义路由域，用于通过 OpenShift 群集上的路由器公开应用程序。  请确保创建相应的通配符 DNS 条目] |
 
   ![产品/服务其他 CNS 安装](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**摘要**

验证在此阶段进行，以检查核心配额是否足以部署为群集选择的 VM 总数。  查看已输入的所有参数。  如果输入都可接受，单击“确定”继续。

   ![产品/服务“摘要”边栏选项卡](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**购买**

确认“购买”页上的联系人信息，单击“购买”以接受使用条款，开始 OpenShift Container Platform 的部署。

   ![产品/服务“购买”边栏选项卡](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>连接到 OpenShift 群集

部署完成后，从部署输出部分检索连接。 使用 OpenShift 控制台 URL 通过浏览器连接到 OpenShift 控制台。 也可以通过 SSH 连接到堡垒主机。 在以下示例中，管理员用户名为 clusteradmin，守护主机的公共 IP DNS FQDN 为 bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com：

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>清理资源

如果不再需要资源组、OpenShift 群集和所有相关的资源，可以使用 [az group delete](/cli/azure/group) 命令将其删除。

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>后续步骤

- [部署后任务](./openshift-container-platform-3x-post-deployment.md)
- [在 Azure 中排查 OpenShift 部署问题](./openshift-container-platform-3x-troubleshooting.md)
- [OpenShift 容器平台入门](https://docs.openshift.com)
- 
