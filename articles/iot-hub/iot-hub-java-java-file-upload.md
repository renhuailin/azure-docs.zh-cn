---
title: 使用 Java 将文件从设备上传到 Azure IoT 中心 | Microsoft Docs
description: 如何使用用于 Java 的 Azure IoT 设备 SDK 从设备将文件上传到云中。 上传的文件存储在 Azure 存储 Blob 容器中。
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: conceptual
ms.date: 07/18/2021
ms.custom:
- amqp
- mqtt
- devx-track-java
ms.openlocfilehash: f375c8cf49d3bdc230e589f6a816eb9615468946
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458939"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-java"></a>通过 IoT 中心将设备中的文件上传到云 (Java)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

本教程介绍如何使用 Java 使用 IoT 中心的文件上传功能。 有关文件上传过程的概述，请参阅[使用 IoT 中心上传文件](iot-hub-devguide-file-upload.md)。

[将遥测数据从设备发送到 IoT 中心](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-java)快速入门和[使用 IoT 中心发送云到设备的消息](iot-hub-java-java-c2d.md)教程介绍了 IoT 中心提供的基本的设备到云和云到设备的消息传送功能。 [使用 IoT 中心配置消息路由](tutorial-routing.md)教程介绍了一种在 Azure Blob 存储中可靠存储设备到云消息的方法。 但是，在某些情况下，无法轻松地将设备发送的数据映射为 IoT 中心接受的相对较小的设备到云消息。 例如：

* 包含图像的大型文件
* 视频
* 以高频率采样的振动数据
* 某种形式的预处理数据。

通常使用 [Azure 数据工厂](../data-factory/introduction.md)或 [Hadoop](../hdinsight/index.yml) 堆栈等工具在云中批处理这些文件。 但是，需要从设备上传文件时，仍可以使用 IoT 中心的安全性和可靠性。 此示例介绍了操作方法。 此外，GitHub 中有两个示例，位于 [https://github.com/Azure/azure-iot-sdk-java/tree/main/device/iot-device-samples/file-upload-sample/src/main/java/samples/com/microsoft/azure/sdk/iot](https://github.com/Azure/azure-iot-sdk-java/tree/main/device/iot-device-samples/file-upload-sample/src/main/java/samples/com/microsoft/azure/sdk/iot)。

> [!NOTE]
> IoT 中心通过 Azure IoT 设备 SDK 来支持许多设备平台和语言（包括 C、.NET 和 Javascript）。 有关如何将设备连接到 Azure IoT 中心的分步说明，请参阅 [Azure IoT 开发人员中心](https://azure.microsoft.com/develop/iot)。

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>先决条件

* [Java SE 开发工具包 8](/java/azure/jdk/)。 请确保在“长期支持”下选择“Java 8”以获取 JDK 8 的下载。

* [Maven 3](https://maven.apache.org/download.cgi)

* 有效的 Azure 帐户。 （如果没有帐户，只需几分钟即可创建一个[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。）

* 确保已在防火墙中打开端口 8883。 本文中的设备示例使用 MQTT 协议，该协议通过端口 8883 进行通信。 在某些公司和教育网络环境中，此端口可能被阻止。 有关解决此问题的更多信息和方法，请参阅[连接到 IoT 中心(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)。

## <a name="create-an-iot-hub"></a>创建 IoT 中心

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>在 IoT 中心内注册新设备

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-include-associate-storage.md)]

## <a name="create-a-project-using-maven"></a>使用 Maven 创建项目

为项目创建目录，并在该目录中启动 shell。 在命令行上，执行以下命令

```cmd/sh
mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
```

这会生成与 *artifactId* 同名的目录和一个标准项目结构：

```
  my-app
  |-- pom.xml
   -- src
      -- main
         -- java
            -- com
               -- mycompany
                  -- app
                     --App.Java
```

使用文本编辑器，将 pom.xml 文件替换为以下内容：

```xml

<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.mycompany.app</groupId>
  <artifactId>my-app</artifactId>
  <version>1.0-SNAPSHOT</version>

  <name>my-app</name>
  <!-- FIXME change it to the project's website -->
  <url>http://www.example.com</url>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <maven.compiler.source>1.7</maven.compiler.source>
    <maven.compiler.target>1.7</maven.compiler.target>
  </properties>

  <dependencies>
      <dependency>
      <groupId>com.microsoft.azure.sdk.iot</groupId>
      <artifactId>iot-device-client</artifactId>
      <version>1.30.1</version>
    </dependency>
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-log4j12</artifactId>
      <version>1.7.29</version>
    </dependency>    
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.11</version>
      <scope>test</scope>
    </dependency>
  </dependencies>

  <build>
    <pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
      <plugins>
        <plugin>
          <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.3</version>
            <configuration>
              <source>1.7</source>
              <target>1.7</target>
            </configuration>
        </plugin>
        <plugin>
          <artifactId>maven-shade-plugin</artifactId>
          <version>2.4</version>
          <executions>
              <execution>
                  <phase>package</phase>
                  <goals>
                    <goal>shade</goal>
                  </goals>
                  <configuration>
                      <filters>
                          <filter>
                              <artifact>*:*</artifact>
                              <excludes>
                                  <exclude>META-INF/*.SF</exclude>
                                  <exclude>META-INF/*.RSA</exclude>
                              </excludes>
                          </filter>
                      </filters>
                      <shadedArtifactAttached>true</shadedArtifactAttached>
                      <shadedClassifierName>with-deps</shadedClassifierName>
                  </configuration>
              </execution>
          </executions>
        </plugin>
      </plugins>
    </pluginManagement>
  </build>
</project>

```

## <a name="upload-a-file-from-a-device-app"></a>从设备应用上传文件

将要上传的文件复制到项目树中的 `my-app` 文件夹。 使用文本编辑器，将 App.java 替换为以下代码。 按说明提供设备连接字符串和文件名。 注册设备时复制了设备连接字符串。

```java
package com.mycompany.app;

import com.azure.storage.blob.BlobClient;
import com.azure.storage.blob.BlobClientBuilder;
import com.microsoft.azure.sdk.iot.deps.serializer.FileUploadCompletionNotification;
import com.microsoft.azure.sdk.iot.deps.serializer.FileUploadSasUriRequest;
import com.microsoft.azure.sdk.iot.deps.serializer.FileUploadSasUriResponse;
import com.microsoft.azure.sdk.iot.device.DeviceClient;
import com.microsoft.azure.sdk.iot.device.IotHubClientProtocol;

import java.io.BufferedInputStream;
import java.io.File;
import java.io.FileInputStream;
import java.io.IOException;
import java.net.URISyntaxException;
import java.util.Scanner;

public class App 
{
    /**
     * Upload a single file to blobs using IoT Hub.
     *
     */
    public static void main(String[] args)throws IOException, URISyntaxException
    {
        String connString = "Your device connection string here";
        String fullFileName = "Path of the file to upload";

        System.out.println("Starting...");
        System.out.println("Beginning setup.");

        // File upload will always use HTTPS, DeviceClient will use this protocol only
        //   for the other services like Telemetry, Device Method and Device Twin.
        IotHubClientProtocol protocol = IotHubClientProtocol.MQTT;

        System.out.println("Successfully read input parameters.");

        DeviceClient client = new DeviceClient(connString, protocol);

        System.out.println("Successfully created an IoT Hub client.");

        try
        {
            File file = new File(fullFileName);
            if (file.isDirectory())
            {
                throw new IllegalArgumentException(fullFileName + " is a directory, please provide a single file name, or use the FileUploadSample to upload directories.");
            }

            System.out.println("Retrieving SAS URI from IoT Hub...");
            FileUploadSasUriResponse sasUriResponse = client.getFileUploadSasUri(new FileUploadSasUriRequest(file.getName()));

            System.out.println("Successfully got SAS URI from IoT Hub");
            System.out.println("Correlation Id: " + sasUriResponse.getCorrelationId());
            System.out.println("Container name: " + sasUriResponse.getContainerName());
            System.out.println("Blob name: " + sasUriResponse.getBlobName());
            System.out.println("Blob Uri: " + sasUriResponse.getBlobUri());

            System.out.println("Using the Azure Storage SDK to upload file to Azure Storage...");

            try
            {
                BlobClient blobClient =
                    new BlobClientBuilder()
                        .endpoint(sasUriResponse.getBlobUri().toString())
                        .buildClient();

                blobClient.uploadFromFile(fullFileName);
            }
            catch (Exception e)
            {
                System.out.println("Exception encountered while uploading file to blob: " + e.getMessage());

                System.out.println("Failed to upload file to Azure Storage.");

                System.out.println("Notifying IoT Hub that the SAS URI can be freed and that the file upload failed.");

                // Note that this is done even when the file upload fails. IoT Hub has a fixed number of SAS URIs allowed active
                // at any given time. Once you are done with the file upload, you should free your SAS URI so that other
                // SAS URIs can be generated. If a SAS URI is not freed through this API, then it will free itself eventually
                // based on how long SAS URIs are configured to live on your IoT Hub.
                FileUploadCompletionNotification completionNotification = new FileUploadCompletionNotification(sasUriResponse.getCorrelationId(), false);
                client.completeFileUpload(completionNotification);

                System.out.println("Notified IoT Hub that the SAS URI can be freed and that the file upload was a failure.");

                client.closeNow();
                return;
            }

            System.out.println("Successfully uploaded file to Azure Storage.");

            System.out.println("Notifying IoT Hub that the SAS URI can be freed and that the file upload was a success.");
            FileUploadCompletionNotification completionNotification = new FileUploadCompletionNotification(sasUriResponse.getCorrelationId(), true);
            client.completeFileUpload(completionNotification);
            System.out.println("Successfully notified IoT Hub that the SAS URI can be freed, and that the file upload was a success");
        }
        catch (Exception e)
        {
            System.out.println("On exception, shutting down \n" + " Cause: " + e.getCause() + " \nERROR: " +  e.getMessage());
            System.out.println("Shutting down...");
            client.closeNow();
        }

        System.out.println("Press any key to exit...");

        Scanner scanner = new Scanner(System.in);
        scanner.nextLine();
        System.out.println("Shutting down...");
        client.closeNow();
    }
}
```

## <a name="build-and-run-the-application"></a>生成并运行应用程序

在 `my-app` 文件夹中的命令提示符下运行以下命令：

```cmd/sh
mvn clean package -DskipTests
```

生成完成后，运行以下命令以运行应用程序：

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```

可以使用门户查看所配置的存储容器中上传的文件：

![上传的文件](media/iot-hub-java-java-upload/uploaded-file.png)

## <a name="receive-a-file-upload-notification"></a>接收文件上传通知

本部分中的操作将创建一个 Java 控制台应用，用于接收来自 IoT 中心的文件上传通知消息。

1. 为项目创建目录，并在该目录中启动 shell。 在命令行上，执行以下命令

    ```cmd/sh
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=my-app -DarchetypeArtifactId=maven-archetype-quickstart -DarchetypeVersion=1.4 -DinteractiveMode=false
    ```

2. 在命令提示符下，导航到新的 `my-app` 文件夹。

3. 使用文本编辑器，将 `my-app` 文件夹中的 `pom.xml` 文件替换为以下内容。 通过添加服务客户端依赖项，即可使用应用程序中的 **iothub-java-service-client** 包与 IoT 中心服务通信：

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>

    <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
      <modelVersion>4.0.0</modelVersion>

      <groupId>com.mycompany.app</groupId>
      <artifactId>my-app</artifactId>
      <version>1.0-SNAPSHOT</version>

      <name>my-app</name>
      <!-- FIXME change it to the project's website -->
      <url>http://www.example.com</url>

      <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <maven.compiler.source>1.7</maven.compiler.source>
        <maven.compiler.target>1.7</maven.compiler.target>
      </properties>

      <dependencies>
          <dependency>
          <groupId>com.microsoft.azure.sdk.iot</groupId>
          <artifactId>iot-device-client</artifactId>
          <version>1.30.1</version>
        </dependency>
        <dependency>
          <groupId>com.microsoft.azure.sdk.iot</groupId>
          <artifactId>iot-service-client</artifactId>
          <version>1.7.23</version>
        </dependency>
        <dependency>
          <groupId>org.slf4j</groupId>
          <artifactId>slf4j-log4j12</artifactId>
          <version>1.7.29</version>
        </dependency>    
        <dependency>
          <groupId>junit</groupId>
          <artifactId>junit</artifactId>
          <version>4.11</version>
          <scope>test</scope>
        </dependency>
      </dependencies>

      <build>
        <pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
          <plugins>
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.3</version>
                <configuration>
                  <source>1.7</source>
                  <target>1.7</target>
                </configuration>
            </plugin>
            <plugin>
              <artifactId>maven-shade-plugin</artifactId>
              <version>2.4</version>
              <executions>
                  <execution>
                      <phase>package</phase>
                      <goals>
                        <goal>shade</goal>
                      </goals>
                      <configuration>
                          <filters>
                              <filter>
                                  <artifact>*:*</artifact>
                                  <excludes>
                                      <exclude>META-INF/*.SF</exclude>
                                      <exclude>META-INF/*.RSA</exclude>
                                  </excludes>
                              </filter>
                          </filters>
                          <shadedArtifactAttached>true</shadedArtifactAttached>
                          <shadedClassifierName>with-deps</shadedClassifierName>
                      </configuration>
                  </execution>
              </executions>
            </plugin>
          </plugins>
        </pluginManagement>
      </build>
    </project>
    ```

    > [!NOTE]
    > 可以使用 [Maven 搜索](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22iot-service-client%22%20g%3A%22com.microsoft.azure.sdk.iot%22)检查是否有最新版本的 **iot-service-client**。

4. 保存并关闭 `pom.xml` 文件。

5. 获取 IoT 中心服务连接字符串。
    [!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

6. 使用文本编辑器打开 `my-app\src\main\java\com\mycompany\app\App.java` 文件，将代码替换为以下内容。

    ```java
    package com.mycompany.app;

    import com.microsoft.azure.sdk.iot.service.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;


    public class App 
    {
        private static final String connectionString = "{Your service connection string here}";
        private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQPS;

        public static void main(String[] args) throws Exception
        {
            ServiceClient sc = ServiceClient.createFromConnectionString(connectionString, protocol);

            FileUploadNotificationReceiver receiver = sc.getFileUploadNotificationReceiver();
            receiver.open();
            FileUploadNotification fileUploadNotification = receiver.receive(2000);
    
            if (fileUploadNotification != null)
            {
                System.out.println("File Upload notification received");
                System.out.println("Device Id : " + fileUploadNotification.getDeviceId());
                System.out.println("Blob Uri: " + fileUploadNotification.getBlobUri());
                System.out.println("Blob Name: " + fileUploadNotification.getBlobName());
                System.out.println("Last Updated : " + fileUploadNotification.getLastUpdatedTimeDate());
                System.out.println("Blob Size (Bytes): " + fileUploadNotification.getBlobSizeInBytes());
                System.out.println("Enqueued Time: " + fileUploadNotification.getEnqueuedTimeUtcDate());
            }
            else
            {
                System.out.println("No file upload notification");
            }
    
            receiver.close();
        }
    
    }
    ```


7. 保存并关闭 `my-app\src\main\java\com\mycompany\app\App.java` 文件。

8. 使用以下命令生成应用并检查错误：
    ```cmd/sh
    mvn clean package -DskipTests
    ```
## <a name="run-the-application"></a>运行应用程序

现即可运行应用程序。

在 `my-app` 文件夹中的命令提示符下运行以下命令：

```cmd/sh
mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
```
以下屏幕截图显示 **read-file-upload-notification** 应用的输出：

![read-file-upload-notification 应用的输出](media/iot-hub-java-java-upload/read-file-upload-notification.png)

## <a name="next-steps"></a>后续步骤

在本教程中，你已学习了如何使用 IoT 中心的文件上传功能来简化从设备进行的文件上传。 可以使用以下文章继续探索 IoT 中心功能和方案：

* [以编程方式创建 IoT 中心](iot-hub-rm-template-powershell.md)

* [C SDK 简介](iot-hub-device-sdk-c-intro.md)

* [Azure IoT SDK](iot-hub-devguide-sdks.md)

若要进一步探索 IoT 中心的功能，请参阅：

* [使用 IoT Edge 模拟设备](../iot-edge/quickstart-linux.md)