---
title: Azure Service Fabric - 使用 Service Fabric 应用程序 KeyVault 引用
description: 本文介绍如何使用应用程序机密的 Service Fabric KeyVaultReference 支持。
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: a0e4ef0decae8cc9ab4dc5f8c69dfef854af81f3
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898590"
---
# <a name="keyvaultreference-support-for-azure-deployed-service-fabric-applications"></a>KeyVaultReference 对 Azure 部署 Service Fabric 应用程序的支持

构建云应用程序时，常见的难题是如何安全地将机密分发到您的应用程序。 例如，你可能想要将数据库密钥部署到你的应用程序，而不会在管道或操作员中公开该密钥。 Service Fabric KeyVaultReference 支持，只需通过引用存储在 Key Vault 中的机密 URL，就可以轻松地将机密部署到应用程序。 Service Fabric 将处理代表应用程序的托管标识提取该机密，并用机密激活应用程序。

> [!NOTE]
> KeyVaultReference 对 Service Fabric 应用程序的支持是 (从 Service Fabric 版本 7.2 CU5 开始的现成) 预览版。 建议你在使用此功能之前升级到此版本。

> [!NOTE]
> KeyVaultReference 对 Service Fabric 应用程序的支持仅支持 [版本控制](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) 的机密。 不支持无版本的机密。 Key Vault 需要与 service fabric 群集位于同一订阅中。 

## <a name="prerequisites"></a>必备条件

- Service Fabric 应用程序的托管标识

    Service Fabric KeyVaultReference 支持使用应用程序的托管标识代表应用程序提取机密，因此，必须通过部署应用程序并将其分配给托管标识。 可遵循[此文档](concepts-managed-identity.md)为应用程序启用托管标识。

- 中心机密存储 (CSS)。

    中心机密存储 (CSS) 是 Service Fabric 的已加密本地机密缓存。 此功能使用 CSS 来保护机密，并在从 Key Vault 中获取机密。 若要使用此功能，还需要启用此可选的系统服务。 遵循本 [文档](service-fabric-application-secret-store.md) 来启用和配置 CSS。

- 向应用程序的托管标识授予对 keyvault 的访问权限

    参考[此文档](how-to-grant-access-other-resources.md)来了解如何向托管标识授予对 keyvault 的访问权限。 另请注意，如果使用系统分配的托管标识，则仅在部署应用程序后创建托管标识。 这可能会创建争用条件，在此情况下，应用程序会尝试访问密钥，然后才能向标识授予对保管库的访问权限。 系统分配的标识的名称将为 `{cluster name}/{application name}/{service name}` 。
    
## <a name="use-keyvaultreferences-in-your-application"></a>在应用程序中使用 KeyVaultReferences
可以通过多种方式使用 KeyVaultReferences
- [作为环境变量](#as-an-environment-variable)
- [作为文件装载到容器中](#mounted-as-a-file-into-your-container)
- [作为容器存储库密码的引用](#as-a-reference-to-a-container-repository-password)

### <a name="as-an-environment-variable"></a>作为环境变量

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
</EnvironmentVariables>
```

```C#
string secret =  Environment.GetEnvironmentVariable("MySecret");
```

### <a name="mounted-as-a-file-into-your-container"></a>作为文件装载到容器中

- 将一个节添加到 settings.xml

    定义类型为 `KeyVaultReference`、值为 `<KeyVaultURL>` 的 `MySecret` 参数

    ```xml
    <Section Name="MySecrets">
        <Parameter Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
    </Section>
    ```

- 在 `<ConfigPackagePolicies>` 中引用 ApplicationManifest.xml 中的新节

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="MyServiceMI" ApplicationIdentityRef="MyApplicationMI" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="MySecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- 使用服务代码中的机密

    `<Section  Name=MySecrets>` 下面列出的每个参数将是 EnvironmentVariable SecretPath 指向的文件夹下的某个文件。 下面的 c # 代码段演示如何从应用程序中读取 MySecret。

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "MySecret"))) 
    {
        string secret =  sr.ReadToEnd();
    }
    ```
    > [!NOTE] 
    > 装入点控制将在其中装入包含机密值的文件的文件夹。

### <a name="as-a-reference-to-a-container-repository-password"></a>作为容器存储库密码的引用

```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="MyACRUser" Type="KeyVaultReference" Password="<KeyVaultURL>"/>
      </ContainerHostPolicies>
```

## <a name="next-steps"></a>后续步骤

* [Azure KeyVault 文档](../key-vault/index.yml)
* [了解中心密钥存储](service-fabric-application-secret-store.md)
* [了解 Service Fabric 应用程序的托管标识](concepts-managed-identity.md)
