---
title: 部署和配置 Azure 防火墙高级版的企业 CA 证书
description: 了解如何部署和配置 Azure 防火墙高级版的企业 CA 证书。
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 07/15/2021
ms.author: victorh
ms.openlocfilehash: 067daef5782697357d08edc0a6c09bea8af085f6
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114441057"
---
# <a name="deploy-and-configure-enterprise-ca-certificates-for-azure-firewall"></a>部署和配置 Azure 防火墙的企业 CA 证书


Azure 防火墙高级版包含一项 TLS 检查功能，此功能需要证书身份验证链。 对于生产部署，应使用企业 PKI 生成用于 Azure 防火墙高级版的证书。 本文介绍如何创建和管理 Azure 防火墙高级版的中间 CA 证书。

有关 Azure 防火墙高级版使用的证书的详细信息，请参阅 [Azure 防火墙高级版证书](premium-certificates.md)。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

若要使用企业 CA 生成用于 Azure 防火墙高级版的证书，必须具有以下资源： 

- 一个 Active Directory 林 
- 一个启用了 Web 注册的 Active Directory 证书服务根 CA 
- 一个具有高级层防火墙策略的 Azure 防火墙高级版 
- 一个 Azure 密钥保管库 
- 一个对密钥保管库访问策略中定义的证书和机密具有读取权限的托管标识 

## <a name="request-and-export-a-certificate"></a>申请和导出证书

1. 访问根 CA 上的 Web 注册站点（通常是 `https://<servername>/certsrv`），并选择“申请证书”。
1. 选择“高级证书申请”。
1. 选择“创建申请并将其提交到此 CA”。
1. 使用从属证书颁发机构模板填写表单。
1. 提交申请并安装证书。
1. 假设此申请是使用 Internet Explorer 从 Windows Server 发出的，请打开“Internet 选项”。
1. 导航到“内容”选项卡，然后选择“证书” 。
1. 选择刚颁发的证书，然后选择“导出”。
1. 选择“下一步”开始启动向导。 选择“是，导出私钥”，然后选择“下一步”。
1. 默认会选择 .pfx 文件格式。 取消选中“包括证书路径中的所有证书(如果可能)”。 如果导出整个证书链，则导入到 Azure 防火墙的过程将失败。
1. 分配并确认用于保护密钥的密码，然后选择“下一步”。
1. 选择文件名和导出位置，然后选择“下一步”。
1. 选择“完成”，并将导出的证书移动到一个安全的位置。

## <a name="add-the-certificate-to-a-firewall-policy"></a>将证书添加到防火墙策略中

1. 在 Azure 门户中，导航到密钥保管库的“证书”页，然后选择“生成/导入”。
1. 选择“导入”作为创建方法，为证书命名，选择导出的 .pfx 文件，输入密码，然后选择“创建” 。
1. 导航到防火墙策略的“TLS 检查”页，并选择托管标识、Key Vault 和证书。 
1. 选择“保存”。
   :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/tls-inspection.png" alt-text="TLS 检查":::

## <a name="validate-tls-inspection"></a>验证 TLS 检查

1. 通过对目标 URL 或所选 FQDN 的 TLS 检查来创建应用程序规则。  例如：`*bing.com`。
1. 在规则的源范围内已加入域的计算机中，导航到目标，并选择浏览器地址栏旁边的锁定符号。 证书应显示它是由企业 CA 颁发的，而不是由公共 CA 颁发的。
1. 显示证书的同时会显示更多详细信息，包括证书路径。
   :::image type="content" source="media/premium-deploy-certificates-enterprise-ca/certificate-details.png" alt-text="证书详细信息":::
1. 在 Log Analytics 中，运行以下 KQL 查询以返回已接受 TLS 检查的所有请求：
   ```
   AzureDiagnostics 
   | where ResourceType == "AZUREFIREWALLS" 
   | where Category == "AzureFirewallApplicationRule" 
   | where msg_s contains "Url:" 
   | sort by TimeGenerated desc
   ```
   结果显示已检查流量的完整 URL：:::image type="content" source="media/premium-deploy-certificates-enterprise-ca/kql-query.png" alt-text="KQL 查询":::

## <a name="next-steps"></a>后续步骤

[Azure 门户中的 Azure 防火墙高级版](premium-portal.md)
