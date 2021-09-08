---
title: Microsoft 标识平台中的签名密钥滚动更新
description: 本文介绍 Azure Active Directory 的签名密钥滚动更新最佳实践
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/03/2021
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.openlocfilehash: f6073ac0da9163756be353c2ed695dfb20430160
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2021
ms.locfileid: "123469385"
---
# <a name="signing-key-rollover-in-the-microsoft-identity-platform"></a>Microsoft 标识平台中的签名密钥滚动更新
本文介绍了你需要了解的有关 Microsoft 标识平台用来为安全令牌签名的公钥的信息。 请务必注意，这些密钥会定期滚动更新，紧急情况下可立即滚动更新。 所有使用 Microsoft 标识平台的应用程序都应该能以编程方式处理密钥滚动更新过程。 继续阅读，了解密钥工作方式、如何评估应用程序的滚动更新的影响以及如何更新应用程序，或者在必要时建立定期手动滚动更新过程来处理密钥滚动更新。

## <a name="overview-of-signing-keys-in-the-microsoft-identity-platform"></a>Microsoft 标识平台中签名密钥的概述
Microsoft 标识平台使用基于行业标准构建的公钥加密，在它自己和使用它的应用程序之间建立信任关系。 实际上，它的工作原理如下所述：Microsoft 标识平台使用签名密钥，该密钥由公钥和私钥对组成。 当用户登录到使用 Microsoft 标识平台进行身份验证的应用程序时，Microsoft 标识平台会创建一个包含用户相关信息的安全令牌。 此令牌由 Microsoft 标识平台使用其私钥进行签名，并会发送回应用程序。 若要验证该令牌是否有效且来自 Microsoft 标识平台，应用程序必须使用由 Microsoft 标识平台公开的公钥（包含在租户的 [OpenID Connect 发现文档](https://openid.net/specs/openid-connect-discovery-1_0.html)或 SAML/WS-Fed [联合元数据文档](../azuread-dev/azure-ad-federation-metadata.md)中）来验证令牌的签名。

出于安全考虑，Microsoft 标识平台的签名密钥会定期更新，且紧急情况下，可立即滚动更新。 这些密钥滚动更新之间并没有已设置的或确定的时间，所以任何与 Microsoft 标识平台集成的应用程序都应该做好处理密钥滚动更新事件的准备，无论该事件可能发生的频率如何。 如果应用程序不处理突然刷新，并且尝试使用过期的密钥验证令牌上的签名，则应用程序将错误地拒绝令牌。  最佳做法是每 24 小时检查一次更新，如果遇到未使用应用程序缓存中的秘钥进行验证的令牌，密钥文档会进行受限制（最多每五分钟一次）的即时刷新。 

OpenID Connect 发现文档和联合元数据文档中始终有多个有效密钥可用。 应用程序应该做好使用文档中指定的任何以及所有密钥的准备，因为一个密钥可能很快会滚动更新，另一个密钥可能就会取而代之，依此类推。  由于我们支持新平台、新云或新的身份验证协议，目前的密钥数量随时间的推移，可能会根据 Microsoft 标识平台的内部体系结构发生变化。 JSON 响应中的密钥顺序和它们的公开顺序均不应被视为对应用有意义。 

只支持一个签名密钥或需要手动更新签名密钥的应用程序，在本质上不太安全可靠。  应将它们更新为使用[标准库](reference-v2-libraries.md)，以确保它们始终使用最新的签名密钥，这是应采取的最佳做法之一。 

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>如何评估应用程序是否会受到影响以及如何应对
应用程序如何处理密钥滚动更新取决于各种变量，例如应用程序类型或所使用的标识协议和库。 以下部分评估了最常见类型的应用程序是否受密钥滚动更新的影响，并提供有关如何更新应用程序以支持自动滚动更新或手动更新密钥的指南。

* [访问资源的本机客户端应用程序](#nativeclient)
* [访问资源的 Web 应用程序/API](#webclient)
* [保护资源的和使用 Azure 应用服务构建的 Web 应用程序/API](#appservices)
* [使用 .NET OWIN OpenID Connect、WS-Fed 或 WindowsAzureActiveDirectoryBearerAuthentication 中间件保护资源的 Web 应用程序/API](#owin)
* [使用 .NET Core OpenID Connect 或 JwtBearerAuthentication 中间件保护资源的 Web 应用程序/API](#owincore)
* [使用 Node.js passport-azure-ad 模块保护资源的 Web 应用程序/API](#passport)
* [保护资源的和使用 Visual Studio 2015 或更高版本创建的 Web 应用程序/API](#vs2015)
* [保护资源的和使用 Visual Studio 2013 创建的 Web 应用程序](#vs2013)
* 保护资源的和使用 Visual Studio 2013 创建的 Web API
* [保护资源的和使用 Visual Studio 2012 创建的 Web 应用程序](#vs2012)
* [使用任何其他库保护资源或手动实现任何受支持协议的 Web 应用程序/API](#other)

本指南 **不** 适用于：

* 从 Azure AD 应用程序库（包括自定义）添加的应用程序具有单独的签名密钥指南。 [详细信息。](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
* 通过应用程序代理发布的本地应用程序无需担心签名密钥。

### <a name="native-client-applications-accessing-resources"></a><a name="nativeclient"></a>访问资源的本机客户端应用程序
仅访问资源的应用程序（例如 Microsoft Graph、KeyVault、Outlook API 和其他 Microsoft API）通常只获取一个令牌并将其传递给资源所有者。 由于它们不保护任何资源且不检查令牌，因此不需要确保正确地为令牌签名。

本机客户端应用程序（不管是桌面还是移动应用程序）都属于此类别，因此不受滚动更新的影响。

### <a name="web-applications--apis-accessing-resources"></a><a name="webclient"></a>访问资源的 Web 应用程序/API
仅访问资源的应用程序（例如 Microsoft Graph、KeyVault、Outlook API 和其他 Microsoft API）通常只获取一个令牌并将其传递给资源所有者。 由于它们不保护任何资源且不检查令牌，因此不需要确保正确地为令牌签名。

使用“仅限应用”流（客户端凭据/客户端证书）来请求令牌的 Web 应用程序和 Web API 属于此类别，因而不受滚动更新的影响。

### <a name="web-applications--apis-protecting-resources-and-built-using-azure-app-services"></a><a name="appservices"></a>保护资源的和使用 Azure 应用服务构建的 Web 应用程序/API
Azure App Services 的身份验证/授权 (EasyAuth) 功能已包含自动处理密钥滚动更新所需要的逻辑。

### <a name="web-applications--apis-protecting-resources-using-net-owin-openid-connect-ws-fed-or-windowsazureactivedirectorybearerauthentication-middleware"></a><a name="owin"></a>使用 .NET OWIN OpenID Connect、WS-Fed 或 WindowsAzureActiveDirectoryBearerAuthentication 中间件保护资源的 Web 应用程序/API
如果应用程序使用 .NET OWIN OpenID Connect、WS-Fed 或 WindowsAzureActiveDirectoryBearerAuthentication 中间件，则它已包含必要的逻辑来自动处理密钥滚动更新。

可以通过查看应用程序的 Startup.cs 或 Startup.Auth.cs 文件中的以下代码片段，来确认应用程序是否正在使用上述任何中间件。

```csharp
app.UseOpenIdConnectAuthentication(
    new OpenIdConnectAuthenticationOptions
    {
        // ...
    });
```

```csharp
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
        // ...
    });
```

```csharp
app.UseWindowsAzureActiveDirectoryBearerAuthentication(
    new WindowsAzureActiveDirectoryBearerAuthenticationOptions
    {
        // ...
    });
```

### <a name="web-applications--apis-protecting-resources-using-net-core-openid-connect-or--jwtbearerauthentication-middleware"></a><a name="owincore"></a>使用 .NET Core OpenID Connect 或 JwtBearerAuthentication 中间件保护资源的 Web 应用程序/API
如果应用程序使用 .NET Core OWIN OpenID Connect 或 JwtBearerAuthentication 中间件，则它已包含自动处理密钥滚动更新所需要的逻辑。

可以通过查看应用程序的 Startup.cs 或 Startup.Auth.cs 中的以下代码片段，来确认应用程序是否正在使用上述任何中间件

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
     });
```

### <a name="web-applications--apis-protecting-resources-using-nodejs-passport-azure-ad-module"></a><a name="passport"></a>使用 Node.js passport-azure-ad 模块保护资源的 Web 应用程序/API
如果应用程序使用 Node.js passport-ad 模块，则它已包含必要的逻辑来自动处理密钥滚动更新。

可以通过搜索应用程序的 app.js 中的以下代码片段，来确认应用程序是否正在使用 passport-ad

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="web-applications--apis-protecting-resources-and-created-with-visual-studio-2015-or-later"></a><a name="vs2015"></a>保护资源的和使用 Visual Studio 2015 或更高版本创建的 Web 应用程序/API
如果应用程序通过 Visual Studio 2015 或更高版本中的 Web 应用程序模板构建，且从“更改身份验证”  菜单中选择了“工作或学校帐户”  ，则应用程序已包含自动处理密钥滚动更新所需要的逻辑。 此逻辑嵌入在 OWIN OpenID Connect 中间件中，可检索和缓存 OpenID Connect 发现文档中的密钥并定期刷新它们。

如果已手动将身份验证添加到解决方案，则应用程序可能不包含必要的密钥滚动更新逻辑。 需要自行编写该逻辑，或遵循[使用任何其他库或手动实现任何受支持协议的 Web 应用程序/API](#other) 中的步骤。

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>保护资源的和使用 Visual Studio 2013 创建的 Web 应用程序
如果应用程序通过 Visual Studio 2013 中的 Web 应用程序模板生成，并且在“更改身份验证”菜单中选择了“组织帐户”，则应用程序已包含自动处理密钥滚动更新所需的逻辑   。 此逻辑将组织的唯一标识符和签名密钥信息存储到与项目关联的两个数据库表中。 可以在项目的 Web.config 文件中找到数据库的连接字符串。

如果已手动将身份验证添加到解决方案，则应用程序可能不包含必要的密钥滚动更新逻辑。 需要自行编写该逻辑，或遵循 [使用任何其他库或手动实现任何受支持协议的 Web 应用程序/API](#other)中的步骤。

以下步骤帮助你验证该逻辑是否在应用程序中正常工作。

1. 在 Visual Studio 2013 中，打开解决方案，然后单击右侧窗口上的“服务器资源管理器”选项卡  。
2. 依次展开“数据连接”、“DefaultConnection”和“表”    。 找到“IssuingAuthorityKeys”表，右键单击它，然后单击“显示表数据”   。
3. 在 **IssuingAuthorityKeys** 表中至少有一行与密钥的指纹值相对应。 删除该表中的所有行。
4. 右键单击“Tenants”表，然后单击“显示表数据”   。
5. 在 **Tenants** 表中，至少有一行与唯一的目录租户标识符相对应。 删除该表中的所有行。 如果未同时删除“Tenants”和“IssuingAuthorityKeys”表中的行，则运行时会出现错误   。
6. 生成并运行应用程序。 登录到帐户后，可以停止应用程序。
7. 返回“服务器资源管理器”，查看“IssuingAuthorityKeys”和“Tenants”表中的值    。 可以看到系统已自动使用联合元数据文档中的相应信息对这两个表进行重新填充。

### <a name="web-apis-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>保护资源的和使用 Visual Studio 2013 创建的 Web API
如果在 Visual Studio 2013 中使用 Web API 模板创建了 Web API 应用程序，然后在“更改身份验证”菜单中选择了“组织帐户”，则应用程序中已包含必需的逻辑   。

如果是手动配置的身份验证，请参阅下面的说明，了解如何将 Web API 配置为自动更新其密钥信息。

以下代码片段演示如何从联合元数据文档获取最新密钥，并使用 [JWT 令牌处理程序](/previous-versions/dotnet/framework/windows-identity-foundation/json-web-token-handler) 来验证令牌。 该代码片段假设你使用自己的缓存机制来持久保存密钥（以便验证将来从 Microsoft 标识平台获取的令牌），无论是将它保存在数据库中、配置文件中，还是保存在其他位置。

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2012"></a><a name="vs2012"></a>保护资源的和使用 Visual Studio 2012 创建的 Web 应用程序
如果应用程序是在 Visual Studio 2012 中生成的，则你可能已使用标识和访问工具配置了应用程序。 还可能会用到[验证颁发者名称注册表 (VINR)](/previous-versions/dotnet/framework/windows-identity-foundation/validating-issuer-name-registry)。 VINR 负责维护受信任标识提供程序（Microsoft 标识平台）的相关信息以及用于验证其颁发的令牌的密钥。 使用 VINR 还可轻松地自动更新存储在 Web.config 文件中的密钥信息，具体方法是：下载与用户的目录关联的最新联合元数据文档，使用最新文档检查配置是否过期，并根据需要更新应用程序以使用新密钥。

如果是使用 Microsoft 提供的代码示例或演练文档创建的应用程序，则密钥滚动更新逻辑已包含在项目中。 你会注意到下面的代码已存在于项目中。 如果应用程序尚未包含该逻辑，请按照下面的步骤添加该逻辑，并验证该逻辑是否正常工作。

1. 在“解决方案资源管理器”中，添加对相应项目的 System.IdentityModel 程序集的引用   。
2. 打开 **Global.asax.cs** 文件并添加以下 using 指令：
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. 在 **Global.asax.cs** 文件中添加以下方法：
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. 在 Global.asax.cs 中的 Application_Start() 方法内调用 RefreshValidationSettings() 方法，如下所示：   
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

执行这些步骤后，系统使用联合元数据文档中的最新信息（包括最新密钥）更新应用程序的 Web.config。 每次在 IIS 中回收应用程序池时，都会进行此更新；默认情况下，IIS 设置为每 29 个小时回收一次应用程序。

遵循以下步骤验证密钥滚动更新逻辑是否正常工作。

1. 确认应用程序正在使用上面的代码后，打开 **Web.config** 文件并导航到 **\<issuerNameRegistry>** 块中，特别是要找到以下几行：
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. 在 \<add thumbprint=""> 设置中，将任一字符替换为不同的字符，以更改指纹值。 保存 **Web.config** 文件。
3. 生成并运行应用程序。 如果你能完成登录过程，则应用程序会通过从你的目录的联合元数据文档下载所需的信息来成功地更新密钥。 如果在登录时遇到问题，请阅读[使用 Microsoft 标识平台将登录名添加到 Web 应用程序](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect)一文，或下载并检查以下代码示例：[用于 Azure Active Directory 的多租户云应用程序](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b)。

### <a name="web-applications--apis-protecting-resources-using-any-other-libraries-or-manually-implementing-any-of-the-supported-protocols"></a><a name="other"></a>使用任何其他库保护资源或手动实现任何受支持协议的 Web 应用程序/API
如果正在使用其他某个库或手动实现任何受支持的协议，则需要检查该库或实现，以确保正在从 OpenID Connect 发现文档或联合元数据文档检索密钥。 进行此项检查的方法之一是在代码或库的代码中执行搜索，以找到对 OpenID 发现文档或联合元数据文档的任何调用。

如果密钥存储在某处或在应用程序中进行了硬编码，则可按照本指南文档末尾的说明执行手动滚动更新，手动检索密钥并进行相应更新。 强烈建议使用本文中所述的任何方法增强应用程序以支持自动滚动更新，从而避免将来在 Microsoft 标识平台增大其滚动更新频率或发生紧急带外滚动更新时出现中断和开销。

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>如何测试应用程序以确定它是否会受影响
可以下载脚本并遵循 [此 GitHub 存储库](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>如果应用程序不支持自动滚动更新，如何执行手动滚动更新
如果应用程序不支持自动滚动更新，则需要建立一个定期监视 Microsoft 标识平台签名密钥的过程，并手动执行相应滚动更新。 [此 GitHub 存储库](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)包含脚本和如何执行此操作的说明。
