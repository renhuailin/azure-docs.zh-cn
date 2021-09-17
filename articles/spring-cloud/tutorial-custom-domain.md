---
title: 教程：将现有自定义域映射到 Azure Spring Cloud
description: 如何将现有的自定义分布式名称服务 (DNS) 名称映射到 Azure Spring Cloud
author: karlerickson
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: bbead4e25df9646c08ec605694cfa7514b011522
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015398"
---
# <a name="tutorial-map-an-existing-custom-domain-to-azure-spring-cloud"></a>教程：将现有自定义域映射到 Azure Spring Cloud

本文适用于：✔️ Java ✔️ C#

域名服务 (DNS) 是将网络节点名称存储在整个网络中的一种技术。 本教程使用 CNAME 记录来映射域（例如 www.contoso.com）。 本教程将使用证书保护自定义域，并介绍如何强制实施传输层安全性 (TLS)（也称为安全套接字层 (SSL)）。

证书用于对 Web 流量进行加密。 可将这些 TLS/SSL 证书存储在 Azure Key Vault 中。

## <a name="prerequisites"></a>先决条件

* 一个已部署到 Azure Spring Cloud 的应用程序（请参阅[快速入门：使用 Azure 门户启动现有 Azure Spring Cloud 应用程序](./quickstart.md)，或使用现有应用）。
* 一个有权访问域提供商（例如 GoDaddy）的 DNS 注册表的域名。
* 来自第三方提供商的私有证书（即你的自签名证书）。 该证书必须与域匹配。
* 一个已部署的 [Azure Key Vault](../key-vault/general/overview.md) 实例

## <a name="keyvault-private-link-considerations"></a>Key Vault 专用链接注意事项

Azure Spring Cloud 管理 IP 尚不属于 Azure 受信任的 Microsoft 服务。 因此，若要允许 Azure Spring Cloud 从受专用终结点连接保护的密钥保管库加载证书，必须将以下 IP 添加到 Azure 密钥保管库防火墙：`20.53.123.160 52.143.241.210 40.65.234.114 52.142.20.14 20.54.40.121 40.80.210.49 52.253.84.152 20.49.137.168 40.74.8.134 51.143.48.243`

## <a name="import-certificate"></a>导入证书

### <a name="prepare-your-certificate-file-in-pfx-optional"></a>准备 PFX 格式的证书文件（可选）

Azure Key Vault 支持导入 PEM 和 PFX 格式的专用证书。 如果你从证书提供商获得的 PEM 文件在以下部分中不起作用：[将证书保存到 Key Vault 中](#save-certificate-in-key-vault)，按照此处步骤为 Azure Key Vault 生成 PFX。

#### <a name="merge-intermediate-certificates"></a>合并中间证书

如果证书颁发机构在证书链中提供了多个证书，则需按顺序合并证书。

若要执行此操作，请在文本编辑器中打开收到的所有证书。

创建名为 mergedcertificate.crt 的合并证书文件。 在文本编辑器中，将每个证书的内容复制到此文件。 证书的顺序应遵循证书链中的顺序，以你的证书开头，以根证书结尾， 如以下示例所示：

```crt
-----BEGIN CERTIFICATE-----
<your entire Base64 encoded SSL certificate>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 1>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded intermediate certificate 2>
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
<The entire Base64 encoded root certificate>
-----END CERTIFICATE-----
```

#### <a name="export-certificate-to-pfx"></a>将证书导出为 PFX

使用在生成证书请求时所用的私钥导出合并的 TLS/SSL 证书。

如果使用 OpenSSL 生成证书请求，则已创建私钥文件。 若要将证书导出为 PFX，请运行以下命令。 将占位符 _&lt;private-key-file>_ 和 _&lt;merged-certificate-file>_ 分别替换为私钥和合并证书文件的路径。

```bash
openssl pkcs12 -export -out myserver.pfx -inkey <private-key-file> -in <merged-certificate-file>
```

出现提示时，定义导出密码。 稍后将 TLS/SSL 证书上传到 Azure Key Vault 时要使用此密码。

如果使用 IIS 或 Certreq.exe 生成证书请求，请将证书安装到本地计算机，然后[将证书导出为 PFX](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754329(v=ws.11))。

### <a name="save-certificate-in-key-vault"></a>在 Key Vault 中保存证书

导入证书的过程要求 PEM 或 PFX 编码的文件位于磁盘上，并且你必须具有私钥。

#### <a name="portal"></a>[门户](#tab/Azure-portal)
若要将证书上传到密钥保管库，请执行以下操作：
1. 转到你的密钥保管库实例。
1. 在左侧导航窗格中，选择“证书”。
1. 在上部菜单中，选择“生成/导入”。
1. 在“创建证书”对话框中，在“证书创建方法”下，选择 `Import` 。
1. 在“上传证书文件”下，导航到证书所在位置并选择该证书。
1. 在“密码”下，如果要上传受密码保护的证书文件，请在此处提供该密码。 否则，请将其留空。 成功导入证书文件后，密钥保管库会删除该密码。
1. 选择“创建”。

    ![导入证书 1](./media/custom-dns-tutorial/import-certificate-a.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

```azurecli
az keyvault certificate import --file <path to .pfx file> --name <certificate name> --vault-name <key vault name> --password <export password>
```

---

### <a name="grant-azure-spring-cloud-access-to-your-key-vault"></a>授予 Azure Spring Cloud 对密钥保管库的访问权限

在导入证书之前，需要授予 Azure Spring Cloud 对密钥保管库的访问权限：

#### <a name="portal"></a>[门户](#tab/Azure-portal)
1. 转到你的密钥保管库实例。
1. 在左侧导航窗格中，选择“访问策略”。
1. 在上方菜单中，选择“添加访问策略”。
1. 填写信息，选择“添加”按钮，然后保存访问策略 。

| 机密权限 | 证书权限 | 选择主体 |
|--|--|--|
| 获取、列出 | 获取、列出 | Azure Spring Cloud 域管理 |

![导入证书 2](./media/custom-dns-tutorial/import-certificate-b.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

授予 Azure Spring Cloud 对密钥保管库的读取权限，并替换以下命令中的 \<key vault resource group> 和 \<key vault name> 。

```azurecli
az keyvault set-policy -g <key vault resource group> -n <key vault name>  --object-id 938df8e2-2b9d-40b1-940c-c75c33494239 --certificate-permissions get list --secret-permissions get list
```

---

### <a name="import-certificate-to-azure-spring-cloud"></a>将证书导入 Azure Spring Cloud

#### <a name="portal"></a>[门户](#tab/Azure-portal)
1. 转到你的服务实例。
1. 在你的应用的左侧导航窗格中，选择“TLS/SSL 设置”。
1. 然后选择“导入密钥保管库证书”。

    ![导入证书](./media/custom-dns-tutorial/import-certificate.png)

1. 成功导入证书后，会在“私钥证书”列表中看到它。

    ![私钥证书](./media/custom-dns-tutorial/key-certificates.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)

```azurecli
az spring-cloud certificate add --name <cert name> --vault-uri <key vault uri> --vault-certificate-name <key vault cert name>
```

显示导入证书的列表：

```azurecli
az spring-cloud certificate list --resource-group <resource group name> --service <service name>
```

---

> [!IMPORTANT]
> 若要使用此证书保护自定义域，仍需将此证书绑定到特定的域。 请遵循本部分中的步骤：[添加 SSL 绑定](#add-ssl-binding)。

## <a name="add-custom-domain"></a>添加自定义域
可以使用 CNAME 记录将自定义 DNS 名称映射到 Azure Spring Cloud。

> [!NOTE]
> 不支持 A 记录。

### <a name="create-the-cname-record"></a>创建 CNAME 记录

转到你的 DNS 提供商，添加一条 CNAME 记录以将域映射到 <service_name>.azuremicroservices.io。 此处的 <service_name> 是 Azure Spring Cloud 实例的名称。 我们支持通配符域和子域。
添加 CNAME 后，DNS 记录页将类似于以下示例：

![DNS 记录页](./media/custom-dns-tutorial/dns-records.png)

## <a name="map-your-custom-domain-to-azure-spring-cloud-app"></a>将自定义域映射到 Azure Spring Cloud 应用
如果你在 Azure Spring Cloud 中没有应用程序，请按照以下教程操作：[快速入门：使用 Azure 门户启动现有 Azure Spring Cloud 应用程序](./quickstart.md)。

#### <a name="portal"></a>[门户](#tab/Azure-portal)
转到应用程序页。

1. 选择“自定义域”。
2. 然后选择“添加自定义域”。

    ![自定义域](./media/custom-dns-tutorial/custom-domain.png)

3. 键入你为其添加了 CNAME 记录的完全限定的域名，例如 www.contoso.com。 请确保主机名记录类型设置为 CNAME (<service_name>.azuremicroservices.io)
4. 选择“验证”以启用“添加”按钮 。
5. 选择 **添加** 。

    ![添加自定义域](./media/custom-dns-tutorial/add-custom-domain.png)

一个应用可以有多个域，但一个域只能映射到一个应用。 成功将自定义域映射到应用后，会在自定义域表中看到该域。

![自定义域表](./media/custom-dns-tutorial/custom-domain-table.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```azurecli
az spring-cloud app custom-domain bind --domain-name <domain name> --app <app name> --resource-group <resource group name> --service <service name>
```

显示自定义域的列表：

```azurecli
az spring-cloud app custom-domain list --app <app name> --resource-group <resource group name> --service <service name>
```

---

> [!NOTE]
> 自定义域带有“不安全”标签意味着该域尚未绑定到 SSL 证书。 从浏览器向自定义域发出任何 HTTPS 请求都会收到错误或警告。

## <a name="add-ssl-binding"></a>添加 SSL 绑定

#### <a name="portal"></a>[门户](#tab/Azure-portal)
在自定义域表中，如上图所示选择“添加 SSL 绑定”。
1. 选择或导入你的证书。
1. 选择“保存”。

    ![添加 SSL 绑定 1](./media/custom-dns-tutorial/add-ssl-binding.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```azurecli
az spring-cloud app custom-domain update --domain-name <domain name> --certificate <cert name> --app <app name> --resource-group <resource group name> --service <service name>
```

---

成功添加 SSL 绑定后，域将处于安全状态：“正常运行”。

![添加 SSL 绑定 2](./media/custom-dns-tutorial/secured-domain-state.png)

## <a name="enforce-https"></a>实施 HTTPS

默认情况下，所有人仍可使用 HTTP 访问你的应用，但你可以将所有 HTTP 请求重定向到 HTTPS 端口。
#### <a name="portal"></a>[门户](#tab/Azure-portal)
在应用页的左侧导航窗格中，选择“自定义域”。 然后，将“仅限 HTTPS”设置为“True”。

![添加 SSL 绑定 3](./media/custom-dns-tutorial/enforce-http.png)

#### <a name="cli"></a>[CLI](#tab/Azure-CLI)
```azurecli
az spring-cloud app update -n <app name> --resource-group <resource group name> --service <service name> --https-only
```

---

该操作完成后，导航到指向你的应用的任一 HTTP URL。 请注意，HTTP URL 不起作用。

## <a name="see-also"></a>另请参阅

* [什么是 Azure 密钥保管库？](../key-vault/general/overview.md)
* [导入证书](../key-vault/certificates/certificate-scenarios.md#import-a-certificate)
* [使用 Azure CLI 启动 Spring Cloud 应用](./quickstart.md)
