---
title: 快速入门 - 使用 TPM 证明将单个设备注册到 Azure 设备预配服务
description: 快速入门 - 使用 TPM 证明将单个设备注册到 Azure IoT 中心设备预配服务 (DPS)。
author: anastasia-ms
ms.author: v-stharr
ms.date: 08/20/2021
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc
zone_pivot_groups: iot-dps-set2
ms.openlocfilehash: d13a4b8224fc5ebe3eccf5f569af0efaed9b4101
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129293713"
---
# <a name="quickstart-enroll-individual-device-to-iot-hub-device-provisioning-service-using-tpm-attestation"></a>快速入门：使用 TPM 证明将单个设备注册到 IoT 中心设备预配服务

本文介绍如何使用 [Microsoft Azure IoT SDK](../iot-hub/iot-hub-devguide-sdks.md) 和示例应用程序，在 Azure IoT 中心设备预配服务中以编程方式为 TPM 设备创建单个注册。 可以通过该单个注册项，选择性地将模拟的 TPM 设备注册到预配服务。 

本文使用 Windows 开发计算机，不过，这些步骤在 Windows 和 Linux 计算机上均适用。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

* 完成[通过 Azure 门户设置 IoT 中心设备预配服务](./quick-setup-auto-provision.md)中的步骤。

:::zone pivot="programming-language-csharp"

* 安装 [Visual Studio 2019](https://www.visualstudio.com/vs/)。

* 在基于 Windows 的计算机上安装 [.NET Core 3.1 SDK 或更高版本](https://dotnet.microsoft.com/download)。 可使用以下命令来检查你的版本。

    ```bash
    dotnet --info
    ```

* （可选）如需在本快速入门末尾注册模拟设备，请执行[创建和预配模拟的 TPM 设备](quick-create-simulated-device-tpm.md)中的过程，一直到获取设备的认可密钥那一步。 保存认可密钥和注册 ID，并视需要记下设备 ID。

:::zone-end

:::zone pivot="programming-language-nodejs"

* 安装 [Node.js v4.0+](https://nodejs.org)。

* （可选）创建认可密钥。 按照[创建和预配模拟设备](quick-create-simulated-device-tpm.md)中的步骤操作，直到获得该密钥。

:::zone-end

:::zone pivot="programming-language-python"

* 下载并安装 [Python 2.x 或 3.x](https://www.python.org/downloads/)。 请确保根据安装程序的要求，使用 32 位或 64 位安装。 在安装过程中出现提示时，请确保将 Python 添加到特定于平台的环境变量中。 

* 安装 [Pip](https://pip.pypa.io/en/stable/installing/)（如果 Python 分发版中未附带）。

> [!IMPORTANT]
> 本文仅适用于已弃用的 V1 Python SDK。 V2 中尚不提供用于 IoT 中心设备预配服务的设备和服务客户端。 该团队目前正在努力使 V2 具有功能奇偶一致性。

:::zone-end

:::zone pivot="programming-language-java"

* 安装 [Java SE 开发工具包 8](/azure/developer/java/fundamentals/java-support-on-azure)。 本快速入门将在下面安装 [Java 服务 SDK](https://azure.github.io/azure-iot-sdk-java/master/service/)。 它可以在 Windows 和 Linux 上运行。 本快速入门使用 Windows。

* 安装 [Maven 3](https://maven.apache.org/download.cgi)。

* 安装 [Git](https://git-scm.com/download/) 并确保将路径添加到环境变量 `PATH`。


:::zone-end

> [!NOTE]
> 请勿执行使用 Azure 门户创建单个注册的步骤。


:::zone pivot="programming-language-python"

<a id="prepareenvironment"></a>

## <a name="prepare-the-development-environment"></a>准备开发环境

* 若要下载并安装 [Python 预配服务 SDK](https://github.com/Azure/azure-iot-sdk-python/tree/v1-deprecated/provisioning_service_client)，请选择以下选项之一：
    
    - 生成并编译 **Azure IoT Python SDK**。 按照[这些说明](https://github.com/Azure/azure-iot-sdk-python/blob/v1-deprecated/doc/python-devbox-setup.md)生成 Python 包。 如果使用 Windows OS，则另请安装 [Visual C++ 可再发行组件包](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)，以便使用 Python 中的本机 DLL。
    
    - [安装或升级 *pip*（Python 包管理系统）](https://pip.pypa.io/en/stable/installing/)，然后通过以下命令安装该包：
        
        ```cmd/sh
        pip install azure-iothub-provisioningserviceclient
        ```

### <a name="copy-symmetric-and-tpm-enrollment-keys"></a>复制对称密钥和 TPM 注册密钥

本快速入门演示了对称密钥和 TPM 注册。 需要创建自己的测试密钥或使用提供的测试密钥：
 
# <a name="symmetric-key"></a>[对称密钥](#tab/symmetrickey)

对于模拟的对称密钥设备注册，需要设备的主密钥和辅助密钥。 如果你没有有效的对称密钥，可以在本例中使用以下测试密钥：

主对称密钥

```
UmorGiEVPNIQuaWGXXbe8v9gWayS7XtOZmNMo6DEaEXP65GvhuK3OeRf8RVZ9BymBCHxNg3oRTey0pUHUwwYKQ==
```

辅助对称密钥

```
Zx8/eE7PUBmnouB1qlNQxI7fcQ2HbJX+y96F1uCVQvDj88jFL+q6L9YWLLi4jqTmkRPOulHlSbSv2uFgj4vKtw==
```

# <a name="tpm"></a>[TPM](#tab/tpm)

对于 TPM 注册，需要设备的认可密钥。 如果已按照[创建和预配模拟的设备](quick-create-simulated-device-tpm.md)快速入门教程创建模拟的 TPM 设备，请使用为该设备创建的密钥。 否则，可以使用 SDK 随附的以下认可密钥：

```
AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAtW6MOyCu/Nih47atIIoZtlYkhLeCTiSrtRN3q6hqgOllA979No4BOcDWF90OyzJvjQknMfXS/Dx/IJIBnORgCg1YX/j4EEtO7Ase29Xd63HjvG8M94+u2XINu79rkTxeueqW7gPeRZQPnl1xYmqawYcyzJS6GKWKdoIdS+UWu6bJr58V3xwvOQI4NibXKD7htvz07jLItWTFhsWnTdZbJ7PnmfCa2vbRH/9pZIow+CcAL9mNTNNN4FdzYwapNVO+6SY/W4XU0Q+dLMCKYarqVNH5GzAWDfKT8nKzg69yQejJM8oeUWag/8odWOfbszA+iFjw3wVNrA5n8grUieRkPQ==
```

---

:::zone-end


:::zone pivot="programming-language-java"

<a id="setupdevbox"></a>

## <a name="prepare-the-development-environment"></a>准备开发环境 

### <a name="set-up-environment-variables"></a>设置环境变量

若要设置环境变量，请执行以下操作：

1. `PATH` 变量应包括 *jdk1.8.x\bin* 目录的完整路径。 如果这是计算机的首次 Java 安装，则请创建名为 `JAVA_HOME` 的新的环境变量，将其指向 *jdk1.8.x* 目录的完整路径。 在 Windows 计算机上，该目录位于 *C:\\Program Files\\Java\\* 文件夹中。可以通过在 Windows 计算机的“控制面板”上搜索“编辑系统环境变量”来创建或编辑环境变量。  

    可以在命令窗口中运行以下命令，查看 Java 是否已成功安装在计算机上：

    ```cmd\sh
    java -version
    ```

2. 编辑环境变量 `PATH`，使之指向 Maven 解压缩时所在文件夹中的 apache-maven-3.x.x\\bin 文件夹。 可以在命令窗口中运行以下命令，确认 Maven 已成功安装：

    ```cmd\sh
    mvn --version
    ```

3. 确保在计算机上安装 [git](https://git-scm.com/download/) 并将其添加到环境变量 `PATH`。

### <a name="clone-git-repository-for-azure-iot-java-sdk"></a>克隆适用于 Azure IoT Java SDK 的 Git 存储库

若要克隆 Azure IoT Java SDK，请执行以下操作：

1. 打开命令提示符。 

2. 使用 [Java 服务 SDK](https://azure.github.io/azure-iot-sdk-java/master/service/) 克隆设备注册代码示例的 GitHub 存储库：

    ```cmd\sh
    git clone https://github.com/Azure/azure-iot-sdk-java.git --recursive
    ```

:::zone-end

## <a name="get-the-connection-string-for-your-provisioning-service"></a>获取适用于预配服务的连接字符串

对于本快速入门中的示例，需要复制适用于预配服务的连接字符串。

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在门户页面的左侧菜单中，选择“所有资源”。

3. 选择设备预配服务。

4. 在“设置”菜单中，选择“共享访问策略”。 

5. 选择要使用的访问策略。

6. 在“访问策略”面板中，复制并保存主密钥连接字符串。

    :::image type="content" source="./media/quick-enroll-device-tpm/get-service-connection-string.png" alt-text="从门户获取预配服务连接字符串。":::

## <a name="create-the-individual-enrollment-sample"></a>创建单个注册示例

:::zone pivot="programming-language-csharp"

本部分介绍如何创建一个 .NET Core 控制台应用，以便向预配服务添加 TPM 设备的单个注册。 进行一些修改后，还可以按这些步骤创建 [Windows IoT Core](https://developer.microsoft.com/en-us/windows/iot) 控制台应用，以便添加单个注册。 若要详细了解如何使用 IoT Core 进行开发，请参阅 [Windows IoT Core 开发人员文档](/windows/iot-core/)。

1. 打开 Visual Studio，选择“新建项目”。

2. 在“创建新项目”中，选择“控制台应用程序”*。

3. 选择“下一步”  。

4. 对于“项目名称”，键入 CreateEnrollmentGroup。

5. 选择“下一步”。 保留默认的“目标框架”。

6. 选择“创建”  。

7. 解决方案打开之后，在“解决方案资源管理器”窗格中，右键单击“CreateEnrollmentGroup”项目，然后选择“管理 NuGet 包”  。

8. 在“NuGet 包管理器”中选择“浏览”。  

9. 键入并选择 Microsoft.Azure.Devices.Provisioning.Service。

10. 选择“安装”。

    ![“NuGet 包管理器”窗口](media//quick-enroll-device-tpm/add-nuget.png)

    此步骤会下载、安装 [Azure IoT 预配服务客户端 SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) NuGet 包及其依赖项并添加对它的引用。

11. 在 `Program.cs` 顶部的其他 `using` 语句之后添加以下 `using` 语句：
  
    ```csharp
    using System.Threading.Tasks;
    using Microsoft.Azure.Devices.Provisioning.Service;
    ```

12. 将以下字段添加到 `Program` 类，并按所列内容进行更改。  

    ```csharp
    private static string ProvisioningConnectionString = "{ProvisioningServiceConnectionString}";
    private const string RegistrationId = "sample-registrationid-csharp";
    private const string TpmEndorsementKey =
        "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
        "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
        "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
        "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
        "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
        
    // Optional parameters
    private const string OptionalDeviceId = "myCSharpDevice";
    private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
    ```

13. 将 `ProvisioningServiceConnectionString` 占位符值替换为上一部分中复制的预配服务连接字符串。

14. 如果将此快速入门与[创建和预配模拟的 TPM 设备](quick-create-simulated-device-tpm.md)快速入门结合使用来预配模拟设备，请将认可密钥和注册 ID 替换为在该快速入门中记下的值。 可以将设备 ID 替换为在该快速入门中建议的值，可以使用自己的值，也可以使用此示例中的默认值。

15. 将以下方法添加到 `Program` 类。  此代码创建单个注册条目，然后调用 `ProvisioningServiceClient` 中的 `CreateOrUpdateIndividualEnrollmentAsync` 方法，将单个注册添加到预配服务。

    ```csharp
    public static async Task RunSample()
    {
        Console.WriteLine("Starting sample...");
    
        using (ProvisioningServiceClient provisioningServiceClient =
                ProvisioningServiceClient.CreateFromConnectionString(ProvisioningConnectionString))
        {
            #region Create a new individualEnrollment config
            Console.WriteLine("\nCreating a new individualEnrollment...");
            Attestation attestation = new TpmAttestation(TpmEndorsementKey);
            IndividualEnrollment individualEnrollment =
                    new IndividualEnrollment(
                            RegistrationId,
                            attestation);
    
            // The following parameters are optional. Remove them if you don't need them.
            individualEnrollment.DeviceId = OptionalDeviceId;
            individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;
            #endregion
    
            #region Create the individualEnrollment
            Console.WriteLine("\nAdding new individualEnrollment...");
            IndividualEnrollment individualEnrollmentResult =
                await provisioningServiceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);
            Console.WriteLine("\nIndividualEnrollment created with success.");
            Console.WriteLine(individualEnrollmentResult);
            #endregion
        
        }
    }
    ```

16. 最后，将 `Main` 方法替换为以下行：

    ```csharp
    static async Task Main(string[] args)
    {
        await RunSample();
        Console.WriteLine("\nHit <Enter> to exit ...");
        Console.ReadLine();
    }
    ```

17. 生成解决方案。

:::zone-end

:::zone pivot="programming-language-nodejs"

1. 在工作文件夹的命令窗口中，运行以下命令：
  
    ```cmd\sh
    npm install azure-iot-provisioning-service
    ```  

2. 使用文本编辑器，在工作文件夹中创建 _create_individual_enrollment.js_ 文件。 将以下代码添加到该文件：

    ```Java
    'use strict';

    var provisioningServiceClient = require('azure-iot-provisioning-service').ProvisioningServiceClient;

    var serviceClient = provisioningServiceClient.fromConnectionString(process.argv[2]);
    var endorsementKey = process.argv[3];

    var enrollment = {
      registrationId: 'first',
      attestation: {
        type: 'tpm',
        tpm: {
          endorsementKey: endorsementKey
        }
      }
    };

    serviceClient.createOrUpdateIndividualEnrollment(enrollment, function(err, enrollmentResponse) {
      if (err) {
        console.log('error creating the individual enrollment: ' + err);
      } else {
        console.log("enrollment record returned: " + JSON.stringify(enrollmentResponse, null, 2));
      }
    });
    ```

3. 保存文件。

:::zone-end

:::zone pivot="programming-language-python"

1. 使用文本编辑器，新建一个 Enrollment.py 文件。

2. 在 Enrollment.py 文件的开头添加以下 `import` 语句和变量（将 `{dpsConnectionString}` 替换为之前复制的连接字符串，将证书占位符替换为在[复制对称密钥和 TPM 注册密钥](#copy-symmetric-and-tpm-enrollment-keys)中创建的证书，并将 `{registrationid}` 替换为仅包含小写字母数字和连字符的唯一 `registrationid`）：


    # <a name="symmetric-key"></a>[对称密钥](#tab/symmetrickey)

    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import IndividualEnrollment, AttestationMechanism
    from provisioningserviceclient.protocol.models import SymmetricKeyAttestation

    CONNECTION_STRING = "Enter your DPS connection string"
    PRIMARY_KEY = "Add a valid key"
    SECONDARY_KEY = "Add a valid key"
    REGISTRATION_ID = "Enter a registration ID"
    ```

    # <a name="tpm"></a>[TPM](#tab/tpm)
   
    ```python
    from provisioningserviceclient import ProvisioningServiceClient
    from provisioningserviceclient.models import IndividualEnrollment, AttestationMechanism

    CONNECTION_STRING = "Enter your DPS connection string"
    ENDORSEMENT_KEY = "Enter the endorsement key for your device"
    REGISTRATION_ID = "Enter a registration ID"
    ```

    ---

3. 添加以下 Python 代码，以实现单个注册的创建：

     # <a name="symmetric-key"></a>[对称密钥](#tab/symmetrickey)

    ```python
    def main():
        print ( "Starting individual enrollment..." )
    
        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)
    
        symAtt = SymmetricKeyAttestation(primary_key=PRIMARY_KEY, secondary_key=SECONDARY_KEY)
        att = AttestationMechanism(type="symmetricKey", symmetric_key=symAtt)
        ie = IndividualEnrollment.create(REGISTRATION_ID, att)
    
        ie = psc.create_or_update(ie)
    
        print ( "Individual enrollment successful." )
    
    if __name__ == '__main__':
        main()
    ```

    # <a name="tpm"></a>[TPM](#tab/tpm)

    ```python
    def main():
        print ( "Starting individual enrollment..." )
    
        psc = ProvisioningServiceClient.create_from_connection_string(CONNECTION_STRING)
    
        att = AttestationMechanism.create_with_tpm(ENDORSEMENT_KEY)
        ie = IndividualEnrollment.create(REGISTRATION_ID, att)
    
        ie = psc.create_or_update(ie)
    
        print ( "Individual enrollment successful." )
    
    if __name__ == '__main__':
        main()
    ```

    ---

4. 保存并关闭 Enrollment.py 文件。

:::zone-end

:::zone pivot="programming-language-java"

1. 在下载的源代码中，导航到示例文件夹 *_azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-sample_* 。 打开文件 /src/main/java/samples/com/microsoft/azure/sdk/iot/ServiceEnrollmentSample.java。

2. 将 `[Provisioning Connection String]` 替换为在[获取适用于预配服务的连接字符串](#get-the-connection-string-for-your-provisioning-service)中复制的连接字符串。

    ```Java
    private static final String PROVISIONING_CONNECTION_STRING = "[Provisioning Connection String]";
    ```

   2. 添加 TPM 设备详细信息：
       1. 获取 TPM 设备模拟的“注册 ID”和“TPM 认可密钥”，   只需执行[模拟 TPM 设备](quick-create-simulated-device-tpm.md#simulatetpm)部分之前的步骤即可。
       2. 使用前面步骤的输出中的“注册 ID”  和“认可密钥”  ，替换示例代码文件 **_ServiceEnrollmentSample.java_** 中的 `[RegistrationId]` 和 `[TPM Endorsement Key]`：
    
           ```Java
           private static final String REGISTRATION_ID = "[RegistrationId]";
           private static final String TPM_ENDORSEMENT_KEY = "[TPM Endorsement Key]";
           ```

3. 若要从示例代码内部配置预配服务，请继续执行下一步。 如果不想配置该服务，请确保注释掉或删除 ServiceEnrollmentSample.java 文件中的以下语句：

    ```Java
    / / The following parameters are optional. Remove it if you don't need.
    individualEnrollment.setDeviceId(DEVICE_ID);
    individualEnrollment.setIotHubHostName(IOTHUB_HOST_NAME);
    individualEnrollment.setProvisioningStatus(PROVISIONING_STATUS);
    ```

4. 此步骤演示如何在示例代码中配置预配服务。

    1. 转到 [Azure 门户](https://portal.azure.com)。

    2. 在门户页面的左侧菜单中，选择“所有资源”。

    3. 选择设备预配服务。

    4. 在“概览”面板中，复制“服务终结点”的主机名。  在源代码示例中，将 `[Host name]` 替换为复制的主机名。

    ```Java
    private static final String IOTHUB_HOST_NAME = "[Host name].azure-devices.net";
    ```

5. 研究示例代码。 此代码用于创建、更新、查询和删除单个 TPM 设备注册。 若要验证是否已在门户中成功注册，请暂时性地注释掉 _ServiceEnrollmentSample.java_ 文件末尾的以下代码行：

    ```Java
    // *********************************** Delete info of individualEnrollment ************************************
    System.out.println("\nDelete the individualEnrollment...");
    provisioningServiceClient.deleteIndividualEnrollment(REGISTRATION_ID);
    ```

6. 保存 _ServiceEnrollmentSample.java_ 文件。

:::zone-end

## <a name="run-the-individual-enrollment-sample"></a>运行单个注册示例

:::zone pivot="programming-language-csharp"

1. 运行 Visual Studio 中的示例，创建注册。 命令窗口会出现，并显示确认消息。

2. 成功创建以后，命令窗口会显示新注册的属性。

:::zone-end

:::zone pivot="programming-language-nodejs"

若要运行示例，需要在上一部分中复制的预配服务连接字符串以及设备的认可密钥。 如果已按照[创建和预配模拟的设备](quick-create-simulated-device-tpm.md)快速入门教程创建模拟的 TPM 设备，请使用为该设备创建的密钥。 否则，若要创建示例单个注册，可以使用 [Node.js 服务 SDK](https://github.com/Azure/azure-iot-sdk-node) 随附的以下许可密钥：

```bash
AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUScTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3dyKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKRdln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFeWlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==
```

1. 若要为 TPM 设备创建单个注册，请运行以下命令（包括命令参数的引号）：

     ```cmd\sh
     node create_individual_enrollment.js "<the connection string for your provisioning service>" "<endorsement key>"
     ```

2. 成功创建以后，命令窗口会显示新注册的属性。

:::zone-end

:::zone pivot="programming-language-python"

1. 打开一个命令提示符，并运行以下脚本。

    ```cmd/sh
    python Enrollment.py
    ```

2. 成功创建以后，命令窗口会显示新注册的属性。

:::zone-end

:::zone pivot="programming-language-java"

1. 在管理员模式下打开命令窗口，然后转到文件夹 _azure-iot-sdk-java/provisioning/provisioning-samples/service-enrollment-group-sample_。

2. 在命令提示符中，使用此命令：

    ```cmd\sh
    mvn install -DskipTests
    ```

    此命令将 Maven 包 [`com.microsoft.azure.sdk.iot.provisioning.service`](https://mvnrepository.com/artifact/com.microsoft.azure.sdk.iot.provisioning/provisioning-service-client) 下载到计算机。 此包包括示例代码需要生成的适用于 Java 服务 SDK 的二进制文件。 如果在上一部分运行了 _X.509 证书生成器_ 工具，则此包已下载到计算机上。

3. 在命令提示符中，运行脚本：

    ```cmd\sh
    cd target
    java -jar ./service-enrollment-group-sample-{version}-with-deps.jar
    ```

4. 成功创建以后，命令窗口会显示新注册的属性。


:::zone-end

若要验证注册组是否已创建，请执行以下操作：

1. 在 Azure 门户中，选择设备预配服务。

2. 在“设置”菜单中，选择“管理注册” 。

3. 选择“单个注册”。 此时会看到一个新的注册条目，对应于示例中使用的注册 ID。

:::zone pivot="programming-language-csharp"

:::image type="content" source="./media/quick-enroll-device-tpm/verify-enrollment-csharp.png" alt-text="在门户中验证 C# 单个设备注册。":::

:::zone-end

:::zone pivot="programming-language-nodejs"

:::image type="content" source="./media/quick-enroll-device-tpm/verify-enrollment-nodejs.png" alt-text="在门户中验证 Node.js 单个设备注册。":::

:::zone-end

:::zone pivot="programming-language-python"

:::image type="content" source="./media/quick-enroll-device-tpm/verify-enrollment-python.png" alt-text="在门户中验证 Python 单个设备注册。":::

:::zone-end

:::zone pivot="programming-language-java"

:::image type="content" source="./media/quick-enroll-device-tpm/verify-enrollment-java.png" alt-text="在门户中验证 Java 单个设备注册。":::

:::zone-end


## <a name="clean-up-resources"></a>清理资源

如果计划浏览 [Azure IoT 中心设备预配服务教程](./tutorial-set-up-cloud.md)，请不要清理本快速入门中创建的资源。 否则，请使用以下步骤删除本快速入门创建的所有资源。

1. 关闭计算机上的示例输出窗口。

2. 在 Azure 门户的左侧菜单中，选择“所有资源”。

3. 选择设备预配服务。

4. 在“设置”菜单中，选择“管理注册” 。

5. 选择“单个注册”选项卡。

6. 选中在本快速入门中注册的设备的“注册 ID”旁边的复选框。

7. 在页面顶部，选择“删除”。

8. 从 Azure 门户中的设备预配服务，选择“证书”。

9. 选择为本快速入门上传的证书。

10. 在“证书详细信息”顶部，选择“删除” 。  

11. 如果已按照[创建和预配模拟的 TPM 设备](quick-create-simulated-device-tpm.md)中的步骤创建模拟的 TPM 设备，请执行以下步骤：

    1. 关闭 TPM 模拟器窗口以及模拟设备的示例输出窗口。

    2. 在 Azure 门户中，导航到预配了设备的 IoT 中心。 

    3. 在“资源管理器”下的菜单中，选择“IoT 设备” 。

    4. 选中在本快速入门中注册的设备的“设备 ID”旁边的复选框。

    5. 在窗格顶部，选择“删除”。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你以编程方式为 TPM 设备创建了单个注册条目。 此外，你已根据需要在计算机上创建了一个 TPM 模拟设备，并已使用 Azure IoT 中心设备预配服务将其预配到 IoT 中心。 若要深入了解设备预配，请继续学习本教程有关如何在 Azure 门户中进行设备预配服务设置的内容。

> [!div class="nextstepaction"]
> [Azure IoT 中心设备预配服务教程](./tutorial-set-up-cloud.md)