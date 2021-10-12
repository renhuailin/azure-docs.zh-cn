---
title: 快速入门 - 使用 X.509 证书证明将组注册到 Azure 设备预配服务
description: 本快速入门介绍如何以编程方式注册使用中间或根 CA X.509 证书证明的设备组。
author: anastasia-ms
ms.author: v-stharr
ms.date: 08/17/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
zone_pivot_groups: iot-dps-set2
ms.openlocfilehash: 1b668c4e15db7ff35adfa6078eeaac441e8377ea
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129293696"
---
# <a name="quickstart-enroll-a-group-of-devices-to-the-device-provisioning-service-using-x509-certificate-attestation"></a>快速入门：使用 X.509 证书证明将设备组注册到设备预配服务 

:::zone pivot="programming-language-csharp,programming-language-nodejs, programming-language-python"

本快速入门介绍如何以编程方式创建使用中间或根 CA X.509 证书的[注册组](concepts-service.md#enrollment-group)。 该注册组是使用 [Microsoft Azure IoT SDK](../iot-hub/iot-hub-devguide-sdks.md) 和一个示例应用程序创建的。 注册组可以控制对设备的预配服务的访问，此类设备在其证书链中共享常用签名证书。 若要了解详细信息，请参阅[使用 X.509 证书控制设备对预配服务的访问](./concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates)。 若要详细了解如何将基于 X.509 证书的公钥基础结构 (PKI) 与 Azure IoT 中心和设备预配服务配合使用，请参阅 [X.509 CA 证书安全概述](../iot-hub/iot-hub-x509ca-overview.md)。

:::zone-end

:::zone pivot="programming-language-java"

本快速入门介绍如何以编程方式创建使用中间或根 CA X.509 证书的单个注册和[注册组](concepts-service.md#enrollment-group)。 该注册组是使用 [Microsoft Azure IoT SDK](../iot-hub/iot-hub-devguide-sdks.md) 和一个示例应用程序创建的。 注册组可以控制对设备的预配服务的访问，此类设备在其证书链中共享常用签名证书。 若要了解详细信息，请参阅[使用 X.509 证书控制设备对预配服务的访问](./concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates)。 若要详细了解如何将基于 X.509 证书的公钥基础结构 (PKI) 与 Azure IoT 中心和设备预配服务配合使用，请参阅 [X.509 CA 证书安全概述](../iot-hub/iot-hub-x509ca-overview.md)。

:::zone-end

## <a name="prerequisites"></a>先决条件

* 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。

* 完成[通过 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)中的步骤。

:::zone pivot="programming-language-csharp"

* 安装 [Visual Studio 2019](https://www.visualstudio.com/vs/)。

* 在基于 Windows 的计算机上安装 [.NET Core 3.1 SDK 或更高版本](https://dotnet.microsoft.com/download)。 可使用以下命令来检查你的版本。

    ```bash
    dotnet --info
    ```

:::zone-end

:::zone pivot="programming-language-nodejs"

* 在计算机上安装 [Node.js v4.0 或更高版本](https://nodejs.org)。

:::zone-end

:::zone pivot="programming-language-python"

* 安装 [Python 2.x 或 3.x](https://www.python.org/downloads/)，并将 Python 添加到特定于你的平台的环境变量。

    > [!IMPORTANT]
    > 本文仅适用于已弃用的 V1 Python SDK。 V2 中尚不提供用于 IoT 中心设备预配服务的设备和服务客户端。 该团队目前正在努力使 V2 具有功能奇偶一致性。

* 安装 [Pip](https://pip.pypa.io/en/stable/installing/)（如果它尚未包含在你的 Python 发行版中）。

:::zone-end

:::zone pivot="programming-language-java"

* [Java SE 开发工具包 8](/azure/developer/java/fundamentals/java-support-on-azure)。 本快速入门将在下面安装 [Java 服务 SDK](https://azure.github.io/azure-iot-sdk-java/master/service/)。 它可以在 Windows 和 Linux 上运行。 本快速入门使用 Windows。

* [Maven 3](https://maven.apache.org/download.cgi)。

:::zone-end

* [安装最新版本的 Git](https://git-scm.com/download/)。 确保将 Git 添加到可供命令窗口访问的环境变量。 请参阅[软件自由保护组织提供的 Git 客户端工具](https://git-scm.com/download/)，了解要安装的最新版 `git` 工具，其中包括 Git Bash，这是一个命令行应用，可以用来与本地 Git 存储库交互。

>[!NOTE]
>本文使用 Windows 开发计算机，不过，本文中的步骤在 Windows 和 Linux 计算机上均适用。

## <a name="prepare-test-certificates"></a>准备测试证书

对于本快速入门，你必须有一个包含中间或根 CA X.509 证书公共部分的 .pem 或 .cer 文件 。 此证书必须上传到预配服务，并由该服务进行验证。

:::zone pivot="programming-language-csharp,programming-language-nodejs, programming-language-python"

### <a name="clone-the-azure-iot-c-sdk"></a>克隆 Azure IoT C SDK

[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) 包含的测试工具可以帮助你创建 X.509 证书链、从该链上传根证书或中间证书，以及通过服务执行所有权证明操作，对证书进行验证。

如果已克隆最新版本的 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 存储库，请跳到[下一部分](#create-the-test-certificate)。

1. 打开 Web 浏览器，转到 [Azure IoT C SDK 发布页](https://github.com/Azure/azure-iot-sdk-c/releases/latest)。

2. 复制最新版 Azure IoT C SDK 的标记名称。

3. 打开命令提示符或 Git Bash shell。 运行以下命令以克隆最新版本的 [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub 存储库。 （请将 `<release-tag>` 替换为在上一步骤中复制的标记）。

    ```cmd/sh
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    此操作可能需要几分钟才能完成。

4. 现在，测试工具应位于克隆的存储库的 azure-iot-sdk-c/tools/CACertificates 中。

:::zone-end

:::zone pivot="programming-language-java"

<a id="javasample"></a>

### <a name="clone-the-azure-iot-java-sdk"></a>克隆 Azure IoT Java SDK

[Azure IoT Java SDK](https://github.com/Azure/azure-iot-sdk-java) 包含可以帮助你创建 X.509 证书链、从该链上传根证书或中间证书，以及通过服务执行所有权证明操作以验证证书的测试工具。

1. 打开命令提示符。

2. 使用 [Java 服务 SDK](https://azure.github.io/azure-iot-sdk-java/master/service/) 克隆设备注册代码示例的 GitHub 存储库：

    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

:::zone-end

### <a name="create-the-test-certificate"></a>创建测试证书

若要创建测试证书，请执行以下操作：

:::zone pivot="programming-language-csharp,programming-language-nodejs, programming-language-python"

若要创建证书，请按照[管理用于示例和教程的测试 CA 证书](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)中的步骤进行操作。

>[!TIP]
>除了使用 C SDK 中的工具之外，*用于 .NET 的 Microsoft Azure IoT SDK* 中的 [组证书验证示例](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample)演示了如何使用现有的 X.509 中间或根 CA 证书采用 C# 执行所有权证明操作。

:::zone-end

:::zone pivot="programming-language-java"

1. 在命令窗口中，转到文件夹“azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator”。

2. 若要生成工具，请运行以下命令：

    ```cmd\sh
    mvn clean install
    ```

3. 若要运行工具，请使用以下命令：

    ```cmd\sh
    cd target
    java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
    ```

4. 出现提示时，可以选择性地为证书输入“公用名称”  。

5. 该工具将在本地生成客户端证书、客户端证书私钥和根证书。复制根证书，因为需要使用它来修改示例代码   。

6. 关闭命令窗口，或者在系统提示输入“验证码”时输入   。

:::zone-end

### <a name="add-and-verify-your-test-certificate"></a>添加并验证测试证书

若要将证书添加到设备预配服务并验证该证书，请执行以下操作：

1. 创建证书后，登录到 [Azure 门户](https://portal.azure.com)。

2. 在门户页面的左侧菜单中，选择“所有资源”。

3. 选择你的设备预配服务。

4. 在“设置”菜单中，选择“证书”。

5. 在顶部菜单中，选择“+ 添加:”。

6. 键入证书名称，上传在上一部分创建的 .pem 文件。

7. 选择“在上传时将证书状态设置为已验证”。

8. 选择“保存”  。

:::image type="content" source="./media/quick-enroll-device-x509/add-certificate.png" alt-text="添加要验证的证书。":::

## <a name="get-the-connection-string-for-your-provisioning-service"></a>获取适用于预配服务的连接字符串

对于本快速入门中的示例，需要复制适用于你的预配服务的连接字符串。

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 在门户页面的左侧菜单中，选择“所有资源”。

3. 选择你的设备预配服务。

4. 在“设置”菜单中，选择“共享访问策略” 。

5. 选择要使用的访问策略。

6. 在“访问策略”面板中，复制并保存主密钥连接字符串。

    ![从门户获取预配服务连接字符串](media/quick-enroll-device-x509/get-service-connection-string.png)

## <a name="create-the-enrollment-group-sample"></a>创建注册组示例

:::zone pivot="programming-language-csharp"

本部分介绍如何创建一个 .NET Core 控制台应用程序，用于将注册组添加到预配服务。

>[!TIP]
>进行一些修改后，可以按照这些步骤创建一个用于添加注册组的 [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot) 控制台应用程序。 若要详细了解如何使用 IoT Core 进行开发，请参阅 [Windows IoT Core developer documentation](/windows/iot-core/)（Windows IoT Core 开发人员文档）。

1. 打开 Visual Studio，选择“新建项目”。

2. 在“创建新项目”中，选择“控制台应用程序”。

3. 选择“下一步”  。

4. 对于“项目名称”，键入“CreateEnrollmentGroup”。

5. 选择“下一步”。 保留默认的“目标框架”。

6. 选择“创建”。

7. 解决方案打开之后，在“解决方案资源管理器”窗格中，右键单击“CreateEnrollmentGroup”项目，然后选择“管理 NuGet 包”  。

8. 在“NuGet 包管理器”中选择“浏览”。  

9. 键入并选择“Microsoft.Azure.Devices.Provisioning.Service”。

10. 选择“安装”。

    ![“NuGet 包管理器”窗口](media//quick-enroll-device-x509/add-nuget.png)

    此步骤会下载、安装 [Azure IoT 预配服务客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) NuGet 包及其依赖项并添加对它的引用。

11. 在 `Program.cs` 顶部的其他 `using` 语句之后添加以下 `using` 语句：

    ```csharp
    using System.Security.Cryptography.X509Certificates;
    using System.Threading.Tasks;
    using Microsoft.Azure.Devices.Provisioning.Service;
    ```

12. 将以下字段添加到 `Program` 类，并按所列内容进行更改。  

    ```csharp
    private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
    private static string EnrollmentGroupId = "enrollmentgrouptest";
    private static string X509RootCertPath = @"{Path to a .cer or .pem file for a verified root CA or intermediate CA X.509 certificate}";
    ```

13. 将 `ProvisioningServiceConnectionString` 占位符值替换为在上一部分复制的预配服务连接字符串。

14. 将 `X509RootCertPath` 占位符值替换为 .pem 或 .cer 文件的路径。 此文件代表中间或根 CA X.509 证书的公用部分，而该证书此前已通过预配服务上传和验证。

15. 可以选择性地更改 `EnrollmentGroupId` 值。 字符串只能包含小写字符和连字符。

    > [!IMPORTANT]
    > 在生产代码中，请注意以下安全注意事项：
    >
    > * 为预配服务管理员硬编码连接字符串不符合安全最佳做法。 与硬编码相反，连接字符串应采用安全方式进行存储，例如存储在安全配置文件或注册表中。
    > * 确保只上传签名证书的公用部分。 不要将包含私钥的 .pfx (PKCS12) 或 .pem 文件上传到预配服务。

16. 将以下方法添加到 `Program` 类。 此代码创建一个注册组条目，然后调用 `ProvisioningServiceClient` 中的 `CreateOrUpdateEnrollmentGroupAsync` 方法，将注册组添加到预配服务。

    ```csharp
    public static async Task RunSample()
    {
        Console.WriteLine("Starting sample...");
    
        using (ProvisioningServiceClient provisioningServiceClient =
                ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
        {
            #region Create a new enrollmentGroup config
            Console.WriteLine("\nCreating a new enrollmentGroup...");
            var certificate = new X509Certificate2(X509RootCertPath);
            Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);
            EnrollmentGroup enrollmentGroup =
                    new EnrollmentGroup(
                            EnrollmentGroupId,
                            attestation)
                    {
                        ProvisioningStatus = ProvisioningStatus.Enabled
                    };
            Console.WriteLine(enrollmentGroup);
            #endregion
    
            #region Create the enrollmentGroup
            Console.WriteLine("\nAdding new enrollmentGroup...");
            EnrollmentGroup enrollmentGroupResult =
                await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);
            Console.WriteLine("\nEnrollmentGroup created with success.");
            Console.WriteLine(enrollmentGroupResult);
            #endregion
    
        }
    }
    ```

17. 最后，将 `Main` 方法替换为以下行：

    ```csharp
    static async Task Main(string[] args)
    {
        await RunSample();
        Console.WriteLine("\nHit <Enter> to exit ...");
        Console.ReadLine();
    }
    ```

18. 生成解决方案。

:::zone-end

:::zone pivot="programming-language-nodejs"

本部分介绍如何创建一个 Node.js 脚本，用于将注册组添加到预配服务。

1. 在工作文件夹的命令窗口中，运行以下命令：

     ```cmd\sh
     npm install azure-iot-provisioning-service
     ```  

2. 使用文本编辑器，在工作文件夹中创建 **create_enrollment_group.js** 文件。 将以下代码添加到文件并进行保存：

    ```javascript
        'use strict';
        var fs = require('fs');
    
        var provisioningServiceClient = require('azure-iot-provisioning-service').ProvisioningServiceClient;
    
        var serviceClient = provisioningServiceClient.fromConnectionString(process.argv[2]);
    
        var enrollment = {
          enrollmentGroupId: 'first',
          attestation: {
            type: 'x509',
            x509: {
              signingCertificates: {
                primary: {
                  certificate: fs.readFileSync(process.argv[3], 'utf-8').toString()
                }
              }
            }
          },
          provisioningStatus: 'disabled'
        };
    
        serviceClient.createOrUpdateEnrollmentGroup(enrollment, function(err, enrollmentResponse) {
          if (err) {
            console.log('error creating the group enrollment: ' + err);
          } else {
            console.log("enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
            enrollmentResponse.provisioningStatus = 'enabled';
            serviceClient.createOrUpdateEnrollmentGroup(enrollmentResponse, function(err, enrollmentResponse) {
              if (err) {
                console.log('error updating the group enrollment: ' + err);
              } else {
                console.log("updated enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
              }
            });
          }
        });
    ```

:::zone-end

:::zone pivot="programming-language-python"

本部分介绍如何创建一个 Python 脚本，用于将注册组添加到预配服务。

1. 使用文本编辑器，新建一个 *EnrollmentGroup.py* 文件。

2. 将以下 Python 代码复制到文件中（请将 `{dpsConnectionString}` 替换为前面复制的连接字符串，将证书占位符替换为在[准备测试证书](#prepare-test-certificates)中创建的证书，将 `{registrationid}` 替换为仅包含小写字母数字和连字符的唯一 `registrationid`）：

    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import EnrollmentGroup, AttestationMechanism
    
    CONNECTION_STRING = "{dpsConnectionString}"
    
    SIGNING_CERT = """-----BEGIN CERTIFICATE-----
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
    -----END CERTIFICATE-----"""
    
    GROUP_ID = "{registrationid}"

    def main():
        print ( "Initiating enrollment group creation..." )

        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)
        att = AttestationMechanism.create_with_x509_signing_certs(SIGNING_CERT)
        eg = EnrollmentGroup.create(GROUP_ID, att)

        eg = psc.create_or_update(eg)
    
        print ( "Enrollment group created." )

    if __name__ == '__main__':
        main()
    ```

3. 保存并关闭 **EnrollmentGroup.py** 文件。

:::zone-end

:::zone pivot="programming-language-java"

<a id="runjavasample"></a>

1. 在 Azure IoT Java SDK 中，转到示例文件夹“azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample”。

2. 在所选的编辑器中打开文件“/src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentGroupSample.java”。

3. 将 `[Provisioning Connection String]` 替换为在[获取适用于预配服务的连接字符串](#get-the-connection-string-for-your-provisioning-service)中复制的连接字符串。

4. 将 `PUBLIC_KEY_CERTIFICATE_STRING` 值替换为在上一部分生成的根证书的值。 请确保替换整个示例值，包括“-----BEGIN CERTIFICATE-----”和“-----END CERTIFICATE-----”行 。

5. 若要从示例代码内配置预配服务，请继续执行下一步。 如果你不想配置该服务，请确保注释掉或删除 ServiceEnrollmentGroupSample.java 文件中的以下语句：

    ```Java
    enrollmentGroup.setIotHubHostName(IOTHUB_HOST_NAME);                // Optional parameter.
    enrollmentGroup.setProvisioningStatus(ProvisioningStatus.ENABLED);  // Optional parameter.
    ```

6. 此步骤说明如何在示例代码中配置预配服务。

    1. 转到 [Azure 门户](https://portal.azure.com)。

    2. 在门户页面的左侧菜单中，选择“所有资源”。

    3. 选择你的设备预配服务。

    4. 在“概述”面板中，复制“服务终结点”的主机名。  在源代码示例中，将 `[Host name]` 替换为复制的主机名。

        ```Java
        private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
        ```

7. 研究示例代码。 此代码用于创建、更新、查询和删除 X.509 设备的组注册。 若要验证是否已在门户中成功注册，请暂时性地注释掉 _ServiceEnrollmentGroupSample.java_ 文件末尾的以下代码行：

    ```Java
    // ************************************** Delete info of enrollmentGroup ***************************************
    System.out.println("\nDelete the enrollmentGroup...");
    provisioningServiceClient.deleteEnrollmentGroup(enrollmentGroupId);
    ```

8. 保存 _ServiceEnrollmentGroupSample.java_ 文件。

:::zone-end

## <a name="run-the-enrollment-group-sample"></a>运行注册组示例

:::zone pivot="programming-language-csharp"

1. 运行 Visual Studio 中的示例，创建注册组。 此时会显示一个命令窗口，其中显示了确认消息。

2. 成功创建后，该命令窗口将显示新注册组的属性。

:::zone-end

:::zone pivot="programming-language-nodejs"

1. 打开命令提示符并运行以下命令（在命令参数的两侧包括引号，并将 `<connection string>` 替换为在上一部分复制的连接字符串，将 `<certificate .pem file>` 替换为 `.pem` 文件的路径）：

    ```cmd\sh
    node create_enrollment_group.js "<connection string>" "<certificate .pem file>"
    ```

2. 成功创建后，该命令窗口将显示新注册组的属性。

:::zone-end

:::zone pivot="programming-language-python"

1. 以管理员模式打开命令提示符，并运行以下命令来安装 [azure-iot-provisioning-device-client](https://pypi.org/project/azure-iot-provisioning-device-client)。

    ```cmd/sh
    pip install azure-iothub-provisioningserviceclient    
    ```

2. 在命令提示符下运行脚本：

    ```cmd/sh
    python EnrollmentGroup.py
    ```

3. 成功创建后，该命令窗口将显示新注册组的属性。

:::zone-end

:::zone pivot="programming-language-java"

1. 以管理员模式打开命令窗口，转到文件夹“azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample”。

2. 在命令提示符下使用以下命令：

    ```cmd\sh
    mvn install -DskipTests
    ```

    此命令将 Maven 包 [`com.microsoft.azure.sdk.iot.provisioning.service`](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) 下载到计算机。 此包包括示例代码需要生成的适用于 Java 服务 SDK 的二进制文件。 如果在上一部分运行了 _X.509 证书生成器_ 工具，则此包已下载到计算机上。

3. 在命令提示符下运行脚本：

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-group-sample-{version}-with-deps.jar
    ```

4. 成功创建后，该命令窗口将显示新注册组的属性。

:::zone-end

若要验证注册组是否已创建，请执行以下操作：

1. 在 Azure 门户中，选择你的设备预配服务。

2. 在“设置”菜单中，选择“管理注册” 。

3. 选择“注册组”。 此时应会看到一个对应于示例中使用的注册 ID 的新注册项。

:::zone pivot="programming-language-csharp"

:::image type="content" source="./media/quick-enroll-device-x509/verify-enrollment-csharp.png" alt-text="在门户中验证 C# 组的注册。":::

:::zone-end

:::zone pivot="programming-language-nodejs"

![门户中的注册属性](media/quick-enroll-device-x509/verify-enrollment-nodejs.png)

:::zone-end

:::zone pivot="programming-language-python"

:::image type="content" source="./media/quick-enroll-device-x509/verify-enrollment-python.png" alt-text="在门户中验证 Python 组的注册。":::

:::zone-end

:::zone pivot="programming-language-java"

:::image type="content" source="./media/quick-enroll-device-x509/verify-enrollment-java.png" alt-text="在门户中验证 Java 组的注册。":::

:::zone-end

:::zone pivot="programming-language-java"

## <a name="modifications-to-enroll-a-single-x509-device"></a>注册单个 X.509 设备所需的修改

若要注册单个 X.509 设备，请修改[使用 Java 服务 SDK 将 TPM 设备注册到 IoT 中心设备预配服务](quick-enroll-device-tpm.md)一文中使用的单个注册  示例代码，如下所示：

1.  将 X.509 客户端证书的“公用名称”复制到剪贴板。 如果希望使用 [上一示例代码部分](#javasample)所示的  X.509 证书生成器工具，请输入证书的“公用名称”，或者使用默认的  **microsoftriotcore**。 将该“公用名称”用作  *REGISTRATION_ID* 变量的值。

    ```Java
    // Use common name of your X.509 client certificate
    private static final String REGISTRATION_ID = "[RegistrationId]";
    ```

2. 将变量 *TPM_ENDORSEMENT_KEY* 重命名为 *PUBLIC_KEY_CERTIFICATE_STRING*。 复制你的客户端证书或者   “X.509 证书生成器”工具的输出中的“客户端证书”，作为 *PUBLIC_KEY_CERTIFICATE_STRING* 变量的值。

    ```Java
    // Rename the variable *TPM_ENDORSEMENT_KEY* as *PUBLIC_KEY_CERTIFICATE_STRING*
    private static final String PUBLIC_KEY_CERTIFICATE_STRING =
            "-----BEGIN CERTIFICATE-----\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX\n" +
            "-----END CERTIFICATE-----\n";
    ```

3. 在 **main** 函数中，将 `Attestation attestation = new TpmAttestation(TPM_ENDORSEMENT_KEY);` 行替换为以下内容，以便使用 X.509 客户端证书：

    ```Java
    Attestation attestation = X509Attestation.createFromClientCertificates(PUBLIC_KEY_CERTIFICATE_STRING);
    ```

4. 使用[创建单个注册示例](quick-enroll-device-tpm.md)部分中的步骤保存、生成并运行单个注册示例文件。

:::zone-end

## <a name="clean-up-resources"></a>清理资源

如果你打算继续学习 [Azure IoT 中心设备预配服务教程](./tutorial-set-up-cloud.md)，请不要清理本快速入门中创建的资源。 否则，请使用以下步骤删除本快速入门创建的所有资源。

1. 关闭计算机上的示例输出窗口。

2. 在 Azure 门户的左侧菜单中，选择“所有资源”。

3. 选择你的设备预配服务。

4. 在“设置”菜单中，选择“管理注册” 。

5. 选择“注册组”选项卡。

6. 选中在本快速入门中注册的设备的“注册 ID”旁边的复选框。

7. 在页面顶部，选择“删除”。

8. 从 Azure 门户上的设备预配服务中，选择“证书”。

9. 选择为本快速入门上传的证书。

10. 在“证书详细信息”顶部，选择“删除” 。  

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已使用 Azure IoT 中心设备预配服务为 X.509 中间或根 CA 证书创建了一个注册组。 若要深入了解设备预配，请继续学习本教程有关如何在 Azure 门户中进行设备预配服务设置的内容。

> [!div class="nextstepaction"]
> [Azure IoT 中心设备预配服务教程](./tutorial-set-up-cloud.md)

:::zone pivot="programming-language-nodejs"

> [!div class="nextstepaction"]
>[Node.js 设备预配示例](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/device/samples)。

:::zone-end