---
title: 教程 - 测试 X.509 证书向 Azure IoT 中心进行设备身份验证的能力 | Microsoft Docs
description: 教程 - 测试 X.509 证书向 Azure IoT 中心进行的身份验证
author: v-gpettibone
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 8e5e7efcf5b09c8b8785e32f6fb4b459531d5567
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2021
ms.locfileid: "122444936"
---
# <a name="tutorial-testing-certificate-authentication"></a>教程：测试证书身份验证

可以使用以下 C# 代码示例来测试你的证书是否可以向 IoT 中心进行设备身份验证。 请注意，在运行测试代码之前，必须执行以下操作：

* 创建根 CA 或从属 CA 证书。
* 将 CA 证书上传到 IoT 中心。
* 证明你拥有 CA 证书。
* 将设备添加到 IoT 中心
* 使用与你的设备相同的设备 ID 创建设备证书。

>[!IMPORTANT]
>身份验证过程会检查设备是否与正确的 IoT 中心名称相关联。 

## <a name="code-example"></a>代码示例

以下代码示例显示如何创建一个 C# 应用程序来模拟向 IoT 中心注册的 X.509 设备。 此示例会将模拟设备提供的温度和湿度值发送到中心。 本教程只创建设备应用程序。 至于如何创建 IoT 中心服务应用程序（用于发送对此模拟设备发送的事件的响应），这是留给读者的练习。

1. 打开 Visual Studio，选择“创建新项目”，然后选择“控制台应用(.NET Framework)”项目模板。  选择“**下一步**”。

1. 在“配置新项目”中，将项目命名为 *SimulateX509Device*，然后选择“创建”。 

   ![在 Visual Studio 中创建 X.509 设备项目](./media/iot-hub-security-x509-get-started/create-device-project-vs2019.png)

1. 在解决方案资源管理器中，右键单击“SimulateX509Device”项目，然后选择“管理 NuGet 包”。 

1. 在“NuGet 包管理器”中，选择“浏览”，然后搜索并选择“Microsoft.Azure.Devices.Client”。   选择“安装”  。

   ![在 Visual Studio 中添加设备 SDK NuGet 包](./media/iot-hub-security-x509-get-started/device-sdk-nuget.png)

    此步骤将下载、安装 Azure IoT 设备 SDK NuGet 包及其依赖项并添加对它的引用。

    输入并运行以下代码：

```csharp
using System;
using Microsoft.Azure.Devices.Client;
using System.Security.Cryptography.X509Certificates;
using System.Threading.Tasks;
using System.Text;

namespace SimulateX509Device
{
    class Program
    {
        private static int MESSAGE_COUNT = 5;

        // Temperature and humidity variables.
        private const int TEMPERATURE_THRESHOLD = 30;
        private static float temperature;
        private static float humidity;
        private static Random rnd = new Random();

        // Set the device ID to the name (device identifier) of your device.
        private static String deviceId = "{your-device-id}";

        static async Task SendEvent(DeviceClient deviceClient)
        {
            string dataBuffer;
            Console.WriteLine("Device sending {0} messages to IoTHub...\n", MESSAGE_COUNT);

            // Iterate MESSAGE_COUNT times to set randomm termperature and humidity values.
            for (int count = 0; count < MESSAGE_COUNT; count++)
            {
                // Set random values for temperature and humidity.
                temperature = rnd.Next(20, 35);
                humidity = rnd.Next(60, 80);
                dataBuffer = string.Format("{{\"deviceId\":\"{0}\",\"messageId\":{1},\"temperature\":{2},\"humidity\":{3}}}", deviceId, count, temperature, humidity);
                Message eventMessage = new Message(Encoding.UTF8.GetBytes(dataBuffer));
                eventMessage.Properties.Add("temperatureAlert", (temperature > TEMPERATURE_THRESHOLD) ? "true" : "false");
                Console.WriteLine("\t{0}> Sending message: {1}, Data: [{2}]", DateTime.Now.ToLocalTime(), count, dataBuffer);

                // Send to IoT Hub.
                await deviceClient.SendEventAsync(eventMessage);
            }
        }
        static void Main(string[] args)
        {
            try
            {
                // Create an X.509 certificate object.
                var cert = new X509Certificate2(@"{full path to pfx certificate.pfx}", "{your certificate password}");

                // Create an authentication object using your X.509 certificate. 
                var auth = new DeviceAuthenticationWithX509Certificate("{your-device-id}", cert);

                // Create the device client.
                var deviceClient = DeviceClient.Create("{your-IoT-Hub-name}.azure-devices.net", auth, TransportType.Mqtt);

                if (deviceClient == null)
                {
                    Console.WriteLine("Failed to create DeviceClient!");
                }
                else
                {
                    Console.WriteLine("Successfully created DeviceClient!");
                    SendEvent(deviceClient).Wait();
                }

                Console.WriteLine("Exiting...\n");
            }
            catch (Exception ex)
            {
                Console.WriteLine("Error in sample: {0}", ex.Message);
            }
         }
    }
}
```