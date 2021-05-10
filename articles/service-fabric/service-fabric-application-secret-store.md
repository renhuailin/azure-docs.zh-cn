---
title: Azure Service Fabric 中心机密服务
description: 本文介绍如何使用 Azure Service Fabric 中的中央机密服务
author: amenarde
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: anmenard
ms.openlocfilehash: 735f0b375d26d00dce43fd9d64131fa194958a73
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108131072"
---
# <a name="central-secret-service-in-azure-service-fabric"></a>Azure Service Fabric 中的中央机密服务 
中央机密服务 (CSS) 也称作中央机密存储，是一种旨在保护群集中的机密的 Service Fabric 系统服务。 CSS 简化对 SF 应用程序机密的管理，从而无需依赖加密参数。

中央机密服务是一种持久、复制的群集内机密缓存。存储在 CSS 中的机密会静态加密到客户提供的加密证书。 CSS 提供用于机密管理的客户端 API，可供作为群集或群集管理员用户访问的实体访问。 Service Fabric 运行时应用程序模型与 CSS 集成，允许将应用程序参数声明为 CSS 机密引用。 

在与[ Azure 部署的 Service Fabric 应用程序的托管标识](concepts-managed-identity.md)搭配时，CSS 还有助于预配声明为 [KeyVault 机密 URL](service-fabric-keyvault-references.md) 的应用程序机密。

中央机密服务并不是要替代 Azure Key Vault 等专用外部机密管理服务。 

  > [!NOTE] 
  > 在运行的版本早于 [7.1. CU3](service-fabric-versions.md#service-fabric-version-name-and-number-reference) 的 SF 群集上激活 CSS 时，如果为 Windows 身份验证配置群集，或者未正确声明 `EncryptionCertificateThumbprint` 或未安装相应证书，激活便会失败并使 CSS 永久处于不正常的运行状态。 无论是哪种情况，都建议将群集升级为 7.1 以上的 SF 运行时版本。 CU3，然后再继续。 
  
## <a name="enable-central-secrets-service"></a>启用中央机密服务
要启用中央机密服务，请更新群集配置，如下所述。 我们建议使用与群集证书不同的加密证书。 此证书必须安装在所有节点上。
```json
{ 
    "fabricSettings": [
        {
            "name":  "CentralSecretService",
            "parameters":  [
                {
                    "name":  "DeployedState",
                    "value":  "enabled"
                },
                {
                    "name" : "EncryptionCertificateThumbprint",
                    "value": "<thumbprint>"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "1"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                }
            ]
        }
    ]
}
```
  > [!NOTE] 
  > Service Fabric [版本 8.0](service-fabric-versions.md#service-fabric-version-name-and-number-reference) 中引入的配置设置“DeployedState”是启用或禁用 CSS 的首选机制。此功能在以前的版本中由配置设置“IsEnabled”提供，现已将其视为过时。

## <a name="central-secret-service-secret-model"></a>中央机密服务机密模型
中央机密服务 API 公开两种类型：机密资源和机密版本。 机密资源类型在概念上表示用于特定用途的单个秘密的一系列版本，示例包括：连接字符串、密码、终结点证书。 机密资源类型的对象包含与该机密相关联的元数据，尤其是种类、内容类型和说明。 机密版本类型表示其关联机密的特定实例，并存储机密明文（已加密）；继续看上面的示例，机密版本包含当前密码值以及在月底前有效的证书对象。续订这些机密后，应生成新的机密版本（并将其添加到 CSS）。

如果要将模型形式化，以下是在 CSS 实现中实现和强制执行的规则：

- 机密资源可能有零个或更多版本
- 每种机密版本都是特定机密资源的子级。一种版本可能只有一个父级
- 单个机密版本可以删除，且不影响同一机密的其他版本
- 删除机密资源会让其所有版本也删除  
- 机密版本的值保持不变
    
机密资源的 REST 管理 API 的完整集可参阅[此处](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-meshsecrets)，机密版本的相关内容可参阅[此处](https://docs.microsoft.com/rest/api/servicefabric/sfclient-index-meshsecretvalues)。

### <a name="declare-a-secret-resource"></a>声明机密资源
可以使用 REST API 创建机密资源。

  > [!NOTE] 
  > 如果群集使用没有 HttpGateway 证书的 Windows 身份验证，则会通过不安全的 HTTP 通道发送 REST 请求。 要为此通道启用 TLS，应更新群集定义，以指定 http 网关服务器证书。

若要使用 REST API 创建 `supersecret` 机密资源，请向 `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview` 发出 PUT 请求。 你将需要使用群集证书或管理员客户端证书进行身份验证，才能创建机密资源。
```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

### <a name="set-the-secret-value"></a>设置机密值
使用以下 REST API 脚本设置机密值。
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```

### <a name="examine-the-secret-value"></a>检查机密值
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```

## <a name="use-the-secret-in-your-application"></a>在应用程序中使用机密
应用程序可以通过将机密声明为环境变量，或者指定将机密明文序列化的路径来使用 CSS 中机密。 请按照以下步骤引用 CSS 机密：

1. 在 **settings.xml** 文件中添加包含以下代码片段的节。 请注意，此处的值采用 {`secretname:version`} 格式。
```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
```

2. 将该节导入到 **ApplicationManifest.xml** 中。
```xml
<ServiceManifestImport>
<ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
<ConfigOverrides />
<Policies>
 <ConfigPackagePolicies CodePackageRef="Code">
   <ConfigPackage Name="Config" SectionName="testsecrets" EnvironmentVariableName="SecretPath" />
   </ConfigPackagePolicies>
</Policies>
</ServiceManifestImport>
```
   
示例 1：将机密装载到容器。 使机密在容器中可用而要做出的唯一更改就是在 `<ConfigPackage>` 中指定 (`specify`) 一个装入点。
以下代码片段是修改后的 **ApplicationManifest.xml**。  
```xml
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="C:\secrets" EnvironmentVariableName="SecretPath" />
           <!-- Linux Container
            <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="/mnt/secrets" EnvironmentVariableName="SecretPath" />
           -->
         </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
```
可在容器中的装入点下使用机密。

示例 2：通过指定 `Type='SecretsStoreRef` 将机密绑定到进程环境变量。 以下示例代码片段演示如何将 `supersecret` 版本 `ver1` 绑定到 **ServiceManifest.xml** 中的环境变量 `MySuperSecret`。

```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
```

环境变量 `SecretPath` 将指向存储所有机密的目录。 `testsecrets` 节下列出的每个参数存储在单独的文件中。 现在，应用程序可以使用该机密，如下所示：
```C#
secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
```
   
## <a name="rotating-the-central-secret-service-encryption-certificate"></a>轮换中央机密服务加密证书
请务必注意，证书在过期后仍可用于解密。 此时，我们建议在轮换后继续预配过去的加密证书，以减少锁定的几率。 轮换 CSS 加密证书需要执行以下步骤：

1. 将新证书预配到群集的每个节点。 此时，请不要删除/继续预配以前的加密证书。
2. 启动群集配置升级，将 `EncryptionCertificateThumbprint` 的值更改为新证书的 SHA-1 指纹。
升级完成后，CSS 开始将其现有内容重新加密到新的加密证书。 之后添加到 CSS 的所有机密都将直接加密到新的加密证书。 由于新证书保护所有机密的收敛均异步进行，因此有必要将上一个加密证书安装在所有节点上，并提供给 CSS。

## <a name="removing-central-secret-service-from-your-cluster"></a>从群集中删除中央机密服务
要从群集中安全删除中央机密服务，需要进行两次升级。 第一次升级会在功能上禁用 CSS，而第二次升级会从群集定义中删除服务，其中包括永久删除其内容。 这种含两个阶段的过程可防止意外删除服务，并有助于确保在删除过程中没有孤立的 CSS 依赖项。 此功能在 SF 8.0 及以上版本中提供。

### <a name="step-1-update-css-deployedstate-to-removing"></a>步骤 1：将 CSS DeployedState 更新为正在删除
将群集定义从 `"IsEnabled" = "true"` 或者 `"DeployedState" = "enabled"` 升级为
```json
{
    "name":  "DeployedState",
    "value":  "removing"
}
```
中央机密服务进入部署的状态 `Removing` 后，它将拒绝所有传入的机密 API 调用，无论是直接 REST 调用还是通过激活包括 SecretStoreRefs 或 KeyVaultReferences 的服务。 群集中仍然依赖 CSS 的任何应用程序或组件在此时都将进入“警告”状态。 如果发生这种情况，应该回滚对部署状态 `Removing` 的升级；如果该升级已经成功，则应启动新的升级，以将 CSS 改回 DeployedState = `Enabled`。 如果中央机密服务在处于部署的状态 `Removing` 时收到请求，它将返回 HTTP 代码 401（未授权）并使自身处于“警告”运行状况状态。

### <a name="step-2-update-css-deployedstate-to-disabled"></a>步骤 2：将 CSS DeployedState 更新为禁用
将群集定义从 `"DeployedState" = "removing"` 升级为
```json
{
    "name":  "DeployedState",
    "value":  "disabled"
}
```
中央机密服务不应再在群集中运行，也不会出现在系统服务列表中。 CSS 的内容将永久丢失。 

## <a name="next-steps"></a>后续步骤

- 详细了解[应用程序和服务安全性](service-fabric-application-and-service-security.md)。
- 了解 [Service Fabric 应用程序的托管标识](concepts-managed-identity.md)。
- 通过 [KeyVaultReferences](service-fabric-keyvault-references.md) 展开 CSS 的功能
