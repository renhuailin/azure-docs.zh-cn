---
title: 连接到已启用 Azure Arc 的 SQL 托管实例
description: 连接到已启用 Azure Arc 的 SQL 托管实例
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 62442749ccff4a588daef57c7e3ecbc374ff5fde
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061725"
---
# <a name="connect-to-azure-arc-enabled-sql-managed-instance"></a>连接到已启用 Azure Arc 的 SQL 托管实例

本文介绍如何连接到已启用 Azure Arc 的 SQL 托管实例。 


## <a name="view-azure-arc-enabled-sql-managed-instances"></a>查看已启用 Azure Arc 的 SQL 托管实例

若要查看已启用 Azure Arc 的 SQL 托管实例和外部终结点，请使用以下命令：

```azurecli
az sql mi-arc list --k8s-namespace <namespace> --use-k8s -o table
```

输出应如下所示：

```console
Name       PrimaryEndpoint      Replicas    State
---------  -------------------  ----------  -------
sqldemo    10.240.0.107,1433    1/1         Ready
```

如果你使用 AKS、kubeadm 或 OpenShift 等，可以从此处复制外部 IP 和端口号，并使用偏好的工具连接到此 IP 和端口，以连接到 Azure Data Studio 或 SQL Server Management Studio 等 SQL Server/Azure SQL 实例。  但是，如果你使用快速入门 VM，请参阅以下有关如何从 Azure 外部连接到该 VM 的特殊信息。 

> [!NOTE]
> 你的企业策略可能会阻止访问该 IP 和端口，尤其是当此 VM 是在公有云中创建时。

## <a name="connect"></a>连接 

使用 Azure Data Studio、SQL Server Management Studio 或 SQLCMD 进行连接

打开 Azure Data Studio，并使用上述外部终结点 IP 地址和端口号连接到你的实例。 如果你使用 Azure VM，则需要使用公共 IP 地址，可以参考[有关 Azure 虚拟机部署的特别说明](#special-note-about-azure-virtual-machine-deployments)来识别此地址。

例如：

- 服务器：52.229.9.30,30913
- 用户名：sa
- 密码：预配时指定的 SQL 密码

> [!NOTE]
> 可以使用 Azure Data Studio [查看 SQL 托管实例仪表板](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards)。

> [!NOTE]
> 为了连接到使用 Kubernetes 清单创建的托管实例，需要以 base64 编码形式向 sqlcmd 提供用户名和密码。

若要使用 SQLCMD、Linux 或 Windows 进行连接，可以使用如下所示的命令。 根据提示输入 SQL 密码：

```bash
sqlcmd -S 52.229.9.30,30913 -U sa
```

## <a name="special-note-about-azure-virtual-machine-deployments"></a>有关 Azure 虚拟机部署的特别说明

如果你使用 Azure 虚拟机，则终结点 IP 地址将不显示公共 IP 地址。 若要查找外部 IP 地址，请使用以下命令：

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

然后，可以组合使用公共 IP 地址与端口来建立连接。

还可能需要通过网络安全网关 (NSG) 公开 SQL 实例的端口。 若要允许流量通过 NSG，需要添加一个规则，为此，可使用以下命令。

若要设置规则，需要知道 NSG 的名称。可使用以下命令找到该名称：

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

获取 NSG 的名称后，可使用以下命令添加防火墙规则。 此处的示例值针对端口 30913 创建一个 NSG 规则，并允许来自任何源 IP 地址的连接。  这不是一种最佳安全做法！  通过指定特定于你的客户端 IP 地址或 IP 地址范围（涵盖你的团队或组织的 IP 地址）的 -source-address-prefixes 值，可以更好地锁定内容。

请将下面的 `--destination-port-ranges` 参数值替换为从上述 `az sql mi-arc list` 命令获取的端口号。

```azurecli
az network nsg rule create -n db_port --destination-port-ranges 30913 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="next-steps"></a>后续步骤

- [查看 SQL 托管实例仪表板](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards)
- [在 Azure 门户中查看 SQL 托管实例](view-arc-data-services-inventory-in-azure-portal.md)
