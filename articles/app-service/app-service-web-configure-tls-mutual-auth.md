---
title: 配置 TLS 相互身份验证
description: 了解如何在 TLS 上对客户端证书进行身份验证。 Azure App Service 使客户端证书可用于应用程序代码进行验证。
ms.assetid: cd1d15d3-2d9e-4502-9f11-a306dac4453a
ms.topic: article
ms.date: 12/11/2020
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: 6b58b73235bba53bb174ebb17a63ad76cf71bcf1
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2021
ms.locfileid: "110787794"
---
# <a name="configure-tls-mutual-authentication-for-azure-app-service"></a>为 Azure 应用服务配置 TLS 相互身份验证

通过为 Azure 应用服务应用启用不同类型的身份验证可以限制对网站的访问。 若要实现此目的，一种方法是通过 TLS/SSL 发送客户端请求时请求客户端证书，然后验证该证书。 此机制称为 TLS 相互身份验证或客户端证书身份验证。 本文介绍如何将应用设置为使用客户端证书身份验证。

> [!NOTE]
> 如果通过 HTTP 而不是 HTTPS 访问站点，不会收到任何客户端证书。 因此，如果应用程序需要客户端证书，则你不应允许通过 HTTP 对应用程序发出请求。
>

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="enable-client-certificates"></a>启用客户端证书

若要将应用设置为需要客户端证书，请执行以下操作：

1. 从应用的管理页的左侧导航中，选择“配置” > “常规设置”。

1. 将“客户端证书模式”设置为“需要”。 单击页顶部的“保存”。 

若要使用 Azure CLI 执行相同操作，请在 [Cloud Shell](https://shell.azure.com) 中运行以下命令：

```azurecli-interactive
az webapp update --set clientCertEnabled=true --name <app-name> --resource-group <group-name>
```

## <a name="exclude-paths-from-requiring-authentication"></a>使路径不要求身份验证

为应用程序启用相互身份验证时，应用根目录下的所有路径都需要客户端证书才能进行访问。 若要针对特定路径去除此要求，请在应用程序配置中定义排除路径。

1. 从应用的管理页的左侧导航中，选择“配置” > “常规设置”。

1. 在“客户端排除路径”旁边，单击编辑图标。

1. 单击“新建路径”，指定路径或用 `,` 或 `;` 分隔的路径列表，然后单击“确定”。

1. 单击页顶部的“保存”。 

在下面的屏幕截图中，任何以 `/public` 开头的应用路径都不会请求客户端证书。 路径匹配不区分大小写。

![证书排除路径][exclusion-paths]

## <a name="access-client-certificate"></a>访问客户端证书

在应用服务中，请求的 TLS 终止发生在前端负载均衡器上。 在[已启用客户端证书](#enable-client-certificates)的情况下将请求转发到应用代码时，应用服务会注入包含客户端证书的 `X-ARR-ClientCert` 请求标头。 应用服务不会对此客户端证书执行任何操作，而只会将它转发到你的应用。 应用代码负责验证客户端证书。

对于 ASP.NET，可以通过 **HttpRequest.ClientCertificate** 属性提供客户端证书。

对于其他应用程序堆栈（Node.js、PHP 等），可以通过 `X-ARR-ClientCert` 请求标头中的 base64 编码值在应用中提供客户端证书。

## <a name="aspnet-5-aspnet-core-31-sample"></a>ASP.NET 5+、ASP.NET Core 3.1 示例

对于 ASP.NET Core，将提供中间件来分析转发的证书。 提供单独的中间件来使用转发的协议标头。 若要接受转发的证书，两者必须同时存在。 可在 [CertificateAuthentication 选项](/aspnet/core/security/authentication/certauth)中放置自定义证书验证逻辑。

```csharp
public class Startup
{
    public Startup(IConfiguration configuration)
    {
        Configuration = configuration;
    }

    public IConfiguration Configuration { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllersWithViews();
        // Configure the application to use the protocol and client ip address forwared by the frontend load balancer
        services.Configure<ForwardedHeadersOptions>(options =>
        {
            options.ForwardedHeaders =
                ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        });       
        
        // Configure the application to client certificate forwarded the frontend load balancer
        services.AddCertificateForwarding(options => { options.CertificateHeader = "X-ARR-ClientCert"; });

        // Add certificate authentication so when authorization is performed the user will be created from the certificate
        services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate();
    }

    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }
        else
        {
            app.UseExceptionHandler("/Home/Error");
            app.UseHsts();
        }
        
        app.UseForwardedHeaders();
        app.UseCertificateForwarding();
        app.UseHttpsRedirection();

        app.UseAuthentication()
        app.UseAuthorization();

        app.UseStaticFiles();

        app.UseRouting();
        
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllerRoute(
                name: "default",
                pattern: "{controller=Home}/{action=Index}/{id?}");
        });
    }
}
```

## <a name="aspnet-webforms-sample"></a>ASP.NET WebForms 示例

```csharp
    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class Cert : System.Web.UI.Page
        {
            public string certHeader = "";
            public string errorString = "";
            private X509Certificate2 certificate = null;
            public string certThumbprint = "";
            public string certSubject = "";
            public string certIssuer = "";
            public string certSignatureAlg = "";
            public string certIssueDate = "";
            public string certExpiryDate = "";
            public bool isValidCert = false;

            //
            // Read the certificate from the header into an X509Certificate2 object
            // Display properties of the certificate on the page
            //
            protected void Page_Load(object sender, EventArgs e)
            {
                NameValueCollection headers = base.Request.Headers;
                certHeader = headers["X-ARR-ClientCert"];
                if (!String.IsNullOrEmpty(certHeader))
                {
                    try
                    {
                        byte[] clientCertBytes = Convert.FromBase64String(certHeader);
                        certificate = new X509Certificate2(clientCertBytes);
                        certSubject = certificate.Subject;
                        certIssuer = certificate.Issuer;
                        certThumbprint = certificate.Thumbprint;
                        certSignatureAlg = certificate.SignatureAlgorithm.FriendlyName;
                        certIssueDate = certificate.NotBefore.ToShortDateString() + " " + certificate.NotBefore.ToShortTimeString();
                        certExpiryDate = certificate.NotAfter.ToShortDateString() + " " + certificate.NotAfter.ToShortTimeString();
                    }
                    catch (Exception ex)
                    {
                        errorString = ex.ToString();
                    }
                    finally 
                    {
                        isValidCert = IsValidClientCertificate();
                        if (!isValidCert) Response.StatusCode = 403;
                        else Response.StatusCode = 200;
                    }
                }
                else
                {
                    certHeader = "";
                }
            }

            //
            // This is a SAMPLE verification routine. Depending on your application logic and security requirements, 
            // you should modify this method
            //
            private bool IsValidClientCertificate()
            {
                // In this example we will only accept the certificate as a valid certificate if all the conditions below are met:
                // 1. The certificate is not expired and is active for the current time on server.
                // 2. The subject name of the certificate has the common name nildevecc
                // 3. The issuer name of the certificate has the common name nildevecc and organization name Microsoft Corp
                // 4. The thumbprint of the certificate is 30757A2E831977D8BD9C8496E4C99AB26CB9622B
                //
                // This example does NOT test that this certificate is chained to a Trusted Root Authority (or revoked) on the server 
                // and it allows for self signed certificates
                //

                if (certificate == null || !String.IsNullOrEmpty(errorString)) return false;

                // 1. Check time validity of certificate
                if (DateTime.Compare(DateTime.Now, certificate.NotBefore) < 0 || DateTime.Compare(DateTime.Now, certificate.NotAfter) > 0) return false;

                // 2. Check subject name of certificate
                bool foundSubject = false;
                string[] certSubjectData = certificate.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certSubjectData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundSubject = true;
                        break;
                    }
                }
                if (!foundSubject) return false;

                // 3. Check issuer name of certificate
                bool foundIssuerCN = false, foundIssuerO = false;
                string[] certIssuerData = certificate.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certIssuerData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundIssuerCN = true;
                        if (foundIssuerO) break;
                    }

                    if (String.Compare(s.Trim(), "O=Microsoft Corp") == 0)
                    {
                        foundIssuerO = true;
                        if (foundIssuerCN) break;
                    }
                }

                if (!foundIssuerCN || !foundIssuerO) return false;

                // 4. Check thumprint of certificate
                if (String.Compare(certificate.Thumbprint.Trim().ToUpper(), "30757A2E831977D8BD9C8496E4C99AB26CB9622B") != 0) return false;

                return true;
            }
        }
    }
```

## <a name="nodejs-sample"></a>Node.js 示例

以下 Node.js 示例代码获取 `X-ARR-ClientCert` 标头，并使用 [node-forge](https://github.com/digitalbazaar/forge) 将 base64 编码的 PEM 字符串转换为证书对象，然后验证该对象：

```javascript
import { NextFunction, Request, Response } from 'express';
import { pki, md, asn1 } from 'node-forge';

export class AuthorizationHandler {
    public static authorizeClientCertificate(req: Request, res: Response, next: NextFunction): void {
        try {
            // Get header
            const header = req.get('X-ARR-ClientCert');
            if (!header) throw new Error('UNAUTHORIZED');

            // Convert from PEM to pki.CERT
            const pem = `-----BEGIN CERTIFICATE-----${header}-----END CERTIFICATE-----`;
            const incomingCert: pki.Certificate = pki.certificateFromPem(pem);

            // Validate certificate thumbprint
            const fingerPrint = md.sha1.create().update(asn1.toDer(pki.certificateToAsn1(incomingCert)).getBytes()).digest().toHex();
            if (fingerPrint.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            // Validate time validity
            const currentDate = new Date();
            if (currentDate < incomingCert.validity.notBefore || currentDate > incomingCert.validity.notAfter) throw new Error('UNAUTHORIZED');

            // Validate issuer
            if (incomingCert.issuer.hash.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            // Validate subject
            if (incomingCert.subject.hash.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            next();
        } catch (e) {
            if (e instanceof Error && e.message === 'UNAUTHORIZED') {
                res.status(401).send();
            } else {
                next(e);
            }
        }
    }
}
```

## <a name="java-sample"></a>Java 示例

以下 Java 类将证书从 `X-ARR-ClientCert` 编码为 `X509Certificate` 实例。 `certificateIsValid()` 验证证书的指纹是否与构造函数中提供的指纹匹配，并且该证书尚未过期。


```java
import java.io.ByteArrayInputStream;
import java.security.NoSuchAlgorithmException;
import java.security.cert.*;
import java.security.MessageDigest;

import sun.security.provider.X509Factory;

import javax.xml.bind.DatatypeConverter;
import java.util.Base64;
import java.util.Date;

public class ClientCertValidator { 

    private String thumbprint;
    private X509Certificate certificate;

    /**
     * Constructor.
     * @param certificate The certificate from the "X-ARR-ClientCert" HTTP header
     * @param thumbprint The thumbprint to check against
     * @throws CertificateException If the certificate factory cannot be created.
     */
    public ClientCertValidator(String certificate, String thumbprint) throws CertificateException {
        certificate = certificate
                .replaceAll(X509Factory.BEGIN_CERT, "")
                .replaceAll(X509Factory.END_CERT, "");
        CertificateFactory cf = CertificateFactory.getInstance("X.509");
        byte [] base64Bytes = Base64.getDecoder().decode(certificate);
        X509Certificate X509cert =  (X509Certificate) cf.generateCertificate(new ByteArrayInputStream(base64Bytes));

        this.setCertificate(X509cert);
        this.setThumbprint(thumbprint);
    }

    /**
     * Check that the certificate's thumbprint matches the one given in the constructor, and that the
     * certificate has not expired.
     * @return True if the certificate's thumbprint matches and has not expired. False otherwise.
     */
    public boolean certificateIsValid() throws NoSuchAlgorithmException, CertificateEncodingException {
        return certificateHasNotExpired() && thumbprintIsValid();
    }

    /**
     * Check certificate's timestamp.
     * @return Returns true if the certificate has not expired. Returns false if it has expired.
     */
    private boolean certificateHasNotExpired() {
        Date currentTime = new java.util.Date();
        try {
            this.getCertificate().checkValidity(currentTime);
        } catch (CertificateExpiredException | CertificateNotYetValidException e) {
            return false;
        }
        return true;
    }

    /**
     * Check the certificate's thumbprint matches the given one.
     * @return Returns true if the thumbprints match. False otherwise.
     */
    private boolean thumbprintIsValid() throws NoSuchAlgorithmException, CertificateEncodingException {
        MessageDigest md = MessageDigest.getInstance("SHA-1");
        byte[] der = this.getCertificate().getEncoded();
        md.update(der);
        byte[] digest = md.digest();
        String digestHex = DatatypeConverter.printHexBinary(digest);
        return digestHex.toLowerCase().equals(this.getThumbprint().toLowerCase());
    }

    // Getters and setters

    public void setThumbprint(String thumbprint) {
        this.thumbprint = thumbprint;
    }

    public String getThumbprint() {
        return this.thumbprint;
    }

    public X509Certificate getCertificate() {
        return certificate;
    }

    public void setCertificate(X509Certificate certificate) {
        this.certificate = certificate;
    }
}
```

[exclusion-paths]: ./media/app-service-web-configure-tls-mutual-auth/exclusion-paths.png
