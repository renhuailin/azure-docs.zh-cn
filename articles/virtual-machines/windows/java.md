---
title: 使用 Java 创建和管理 Azure 虚拟机
description: 使用 Java 和 Azure 资源管理器部署虚拟机及其所有支持资源。
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure
ms.topic: how-to
ms.date: 10/09/2021
ms.custom: devx-track-java
ms.author: cynthn
ms.openlocfilehash: fa4d2f5dc33553cb92a5733e5946c055d6a12941
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2021
ms.locfileid: "129810881"
---
# <a name="create-and-manage-windows-vms-in-azure-using-java"></a>使用 Java 创建和管理 Azure 中的 Windows VM

适用于：:heavy_check_mark: Windows VM 

[Azure 虚拟机](overview.md) (VM) 需要多个支持性 Azure 资源。 本文介绍如何使用 Java 创建、管理和删除 VM 资源。 你将学习如何执行以下操作：

> [!div class="checklist"]
> * 创建 Maven 项目
> * 添加依赖项
> * 创建凭据
> * 创建资源
> * 执行管理任务
> * 删除资源
> * 运行应用程序

完成这些步骤大约需要 20 分钟。

## <a name="create-a-maven-project"></a>创建 Maven 项目

1. 如果尚未安装 [Java](/azure/developer/java/fundamentals/java-support-on-azure)，请安装。
2. 安装 [Maven](https://maven.apache.org/download.cgi)。
3. 创建新文件夹和项目：
    
    ```
    mkdir java-azure-test
    cd java-azure-test
    
    mvn archetype:generate -DgroupId=com.fabrikam -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

## <a name="add-dependencies"></a>添加依赖项

1. 在 `testAzureApp` 文件夹下，打开 `pom.xml` 文件并将生成配置添加到 &lt;project&gt; 以启用应用程序生成：

    ```xml
    <build>
      <plugins>
        <plugin>
            <groupId>org.codehaus.mojo</groupId>
            <artifactId>exec-maven-plugin</artifactId>
            <configuration>
                <mainClass>com.fabrikam.testAzureApp.App</mainClass>
            </configuration>
        </plugin>
      </plugins>
    </build>
    ```

2. 添加访问 Azure Java SDK 所需的依赖项。

    ```xml
    <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-identity</artifactId>
      <version>1.3.6</version>
    </dependency>
    <dependency>
      <groupId>com.azure.resourcemanager</groupId>
      <artifactId>azure-resourcemanager</artifactId>
      <version>2.8.0</version>
    </dependency>
    ```

3. 保存文件。

## <a name="set-up-authentication"></a>设置身份验证

了解如何[设置身份验证](/azure/developer/java/sdk/get-started#set-up-authentication)。

## <a name="create-the-management-client"></a>创建管理客户端

1. 打开 `src\main\java\com\fabrikam` 下的 `App.java` 文件，确保此包语句处于顶部：

    ```java
    package com.fabrikam.testAzureApp;
    ```

2. 创建 AzureResourceManager：
   
    ```java
    TokenCredential credential = new EnvironmentCredentialBuilder()
                .authorityHost(AzureAuthorityHosts.AZURE_PUBLIC_CLOUD)
                .build();

    // Please finish 'Set up authentication' step first to set the four environment variables: AZURE_SUBSCRIPTION_ID, AZURE_CLIENT_ID, AZURE_CLIENT_SECRET, AZURE_TENANT_ID
    AzureProfile profile = new AzureProfile(AzureEnvironment.AZURE);

    AzureResourceManager azureResourceManager = AzureResourceManager.configure()
            .withLogLevel(HttpLogDetailLevel.BASIC)
            .authenticate(credential, profile)
            .withDefaultSubscription();
    ```

## <a name="create-resources"></a>创建资源

### <a name="create-the-resource-group"></a>创建资源组

必须在[资源组](../../azure-resource-manager/management/overview.md)中包含所有资源。

若要指定应用程序的值并创建资源组，请将此代码添加到 main 方法中的 try 块：

```java
System.out.println("Creating resource group...");
ResourceGroup resourceGroup = azure.resourceGroups()
    .define("myResourceGroup")
    .withRegion(Region.US_EAST)
    .create();
```

### <a name="create-the-availability-set"></a>创建可用性集

使用[可用性集](tutorial-availability-sets.md)可以更方便地维护应用程序所用的虚拟机。

若要创建可用性集，请将此代码添加到 main 方法的 try 块：

```java
System.out.println("Creating availability set...");
AvailabilitySet availabilitySet = azure.availabilitySets()
    .define("myAvailabilitySet")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .create();
```
### <a name="create-the-public-ip-address"></a>创建公共 IP 地址

与虚拟机通信需要[公共 IP 地址](../../virtual-network/public-ip-addresses.md)。

若要创建虚拟机的公共 IP 地址，请将此代码添加到 main 方法的 try 块：

```java
System.out.println("Creating public IP address...");
PublicIpAddress publicIPAddress = azure.publicIpAddresses()
    .define("myPublicIP")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withDynamicIP()
    .create();
```

### <a name="create-the-virtual-network"></a>创建虚拟网络

虚拟机必须在[虚拟网络](../../virtual-network/virtual-networks-overview.md)的子网中。

若要创建子网和虚拟网络，请将此代码添加到 main 方法的 try 块：

```java
System.out.println("Creating virtual network...");
Network network = azure.networks()
    .define("myVN")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withAddressSpace("10.0.0.0/16")
    .withSubnet("mySubnet", "10.0.0.0/24")
    .create();
```

### <a name="create-the-network-interface"></a>创建网络接口

虚拟机需要使用网络接口在虚拟网络上通信。

若要创建网络接口，请将此代码添加到 main 方法的 try 块：

```java
System.out.println("Creating network interface...");
NetworkInterface networkInterface = azure.networkInterfaces()
    .define("myNIC")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetwork(network)
    .withSubnet("mySubnet")
    .withPrimaryPrivateIPAddressDynamic()
    .withExistingPrimaryPublicIPAddress(publicIPAddress)
    .create();
```

### <a name="create-the-virtual-machine"></a>创建虚拟机

创建所有支持资源后，即可创建虚拟机。

若要创建虚拟机，请将此代码添加到 main 方法的 try 块：

```java
System.out.println("Creating virtual machine...");
VirtualMachine virtualMachine = azure.virtualMachines()
    .define("myVM")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withLatestWindowsImage("MicrosoftWindowsServer", "WindowsServer", "2012-R2-Datacenter")
    .withAdminUsername("azureuser")
    .withAdminPassword("Azure12345678")
    .withComputerName("myVM")
    .withExistingAvailabilitySet(availabilitySet)
    .withSize("Standard_DS1")
    .create();
Scanner input = new Scanner(System.in);
System.out.println("Press enter to get information about the VM...");
input.nextLine();
```

> [!NOTE]
> 本教程创建运行 Windows Server 操作系统版本的虚拟机。 若要详细了解如何选择其他映像，请参阅[使用 Windows PowerShell 和 Azure CLI 来导航和选择 Azure 虚拟机映像](../linux/cli-ps-findimage.md)。
> 
>

如果要使用现有磁盘而不是市场映像，请使用以下代码： 

```java
Disk managedDisk = azure.disks().define("myosdisk")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withWindowsFromVhd("https://mystorage.blob.core.windows.net/vhds/myosdisk.vhd")
    .withStorageAccountName("mystorage")
    .withSizeInGB(128)
    .withSku(DiskSkuTypes.PREMIUM_LRS)
    .create();

azure.virtualMachines().define("myVM")
    .withRegion(Region.US_EAST)
    .withExistingResourceGroup("myResourceGroup")
    .withExistingPrimaryNetworkInterface(networkInterface)
    .withSpecializedOSDisk(managedDisk, OperatingSystemTypes.WINDOWS)
    .withExistingAvailabilitySet(availabilitySet)
    .withSize(VirtualMachineSizeTypes.STANDARD_DS1)
    .create();
```

## <a name="perform-management-tasks"></a>执行管理任务

在虚拟机生命周期中，可能需要运行管理任务，例如启动、停止或删除虚拟机。 此外，建议创建代码来自动执行重复或复杂的任务。

如需对虚拟机执行任何操作，需获取一个虚拟机实例。 请将此代码添加到 main 方法的 try 块：

```java
VirtualMachine vm = azure.virtualMachines().getByResourceGroup("myResourceGroup", "myVM");
```

### <a name="get-information-about-the-vm"></a>获取有关 VM 的信息

若要获取有关虚拟机的信息，请将此代码添加到 main 方法的 try 块：

```java
System.out.println("hardwareProfile");
System.out.println("    vmSize: " + vm.size());
System.out.println("storageProfile");
System.out.println("  imageReference");
System.out.println("    publisher: " + vm.storageProfile().imageReference().publisher());
System.out.println("    offer: " + vm.storageProfile().imageReference().offer());
System.out.println("    sku: " + vm.storageProfile().imageReference().sku());
System.out.println("    version: " + vm.storageProfile().imageReference().version());
System.out.println("  osDisk");
System.out.println("    osType: " + vm.storageProfile().osDisk().osType());
System.out.println("    name: " + vm.storageProfile().osDisk().name());
System.out.println("    createOption: " + vm.storageProfile().osDisk().createOption());
System.out.println("    caching: " + vm.storageProfile().osDisk().caching());
System.out.println("osProfile");
System.out.println("    computerName: " + vm.osProfile().computerName());
System.out.println("    adminUserName: " + vm.osProfile().adminUsername());
System.out.println("    provisionVMAgent: " + vm.osProfile().windowsConfiguration().provisionVMAgent());
System.out.println(
        "    enableAutomaticUpdates: " + vm.osProfile().windowsConfiguration().enableAutomaticUpdates());
System.out.println("networkProfile");
System.out.println("    networkInterface: " + vm.primaryNetworkInterfaceId());
System.out.println("vmAgent");
System.out.println("  vmAgentVersion: " + vm.instanceView().vmAgent().vmAgentVersion());
System.out.println("    statuses");
for (InstanceViewStatus status : vm.instanceView().vmAgent().statuses()) {
    System.out.println("    code: " + status.code());
    System.out.println("    displayStatus: " + status.displayStatus());
    System.out.println("    message: " + status.message());
    System.out.println("    time: " + status.time());
}
System.out.println("disks");
for (DiskInstanceView disk : vm.instanceView().disks()) {
    System.out.println("  name: " + disk.name());
    System.out.println("  statuses");
    for (InstanceViewStatus status : disk.statuses()) {
        System.out.println("    code: " + status.code());
        System.out.println("    displayStatus: " + status.displayStatus());
        System.out.println("    time: " + status.time());
    }
}
System.out.println("VM general status");
System.out.println("  provisioningStatus: " + vm.provisioningState());
System.out.println("  id: " + vm.id());
System.out.println("  name: " + vm.name());
System.out.println("  type: " + vm.type());
System.out.println("VM instance status");
for (InstanceViewStatus status : vm.instanceView().statuses()) {
    System.out.println("  code: " + status.code());
    System.out.println("  displayStatus: " + status.displayStatus());
}
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="stop-the-vm"></a>停止 VM

可停止虚拟机并保留其所有设置，但需继续付费；还可停止虚拟机并解除分配。 解除分配虚拟机时，也会解除分配与其关联的所有资源并将停止计费。

若要停止虚拟机而不解除分配虚拟机，请将此代码添加到 main 方法的 try 块：

```java
System.out.println("Stopping vm...");
vm.powerOff();
System.out.println("Press enter to continue...");
input.nextLine();
```

要解除分配虚拟机，请将 PowerOff 调用更改为以下代码：

```java
vm.deallocate();
```

### <a name="start-the-vm"></a>启动 VM

若要启动虚拟机，请将此代码添加到 main 方法的 try 块：

```java
System.out.println("Starting vm...");
vm.start();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="resize-the-vm"></a>重设 VM 大小

决定虚拟机大小时应考虑部署的诸多方面。 有关详细信息，请参见 [VM 大小](../sizes.md)。  

若要更改虚拟机大小，请将此代码添加到 main 方法的 try 块：

```java
System.out.println("Resizing vm...");
vm.update()
    .withSize(VirtualMachineSizeTypes.STANDARD_DS2)
    .apply();
System.out.println("Press enter to continue...");
input.nextLine();
```

### <a name="add-a-data-disk-to-the-vm"></a>将数据磁盘添加到 VM

若要将数据磁盘添加到大小为 2 GB、LUN 为 0 且缓存类型为 ReadWrite 的虚拟机，请将此代码添加到 main 方法的 try 块：

```java
System.out.println("Adding data disk...");
vm.update()
    .withNewDataDisk(2, 0, CachingTypes.READ_WRITE)
    .apply();
System.out.println("Press enter to delete resources...");
input.nextLine();
```

## <a name="delete-resources"></a>删除资源

由于需要为 Azure 中使用的资源付费，因此，删除不再需要的资源总是一种良好的做法。 如果要删除虚拟机和所有支持资源，只需删除资源组。

1. 若要删除资源组，请将此代码添加到 main 方法的 try 块：
   
    ```java
    System.out.println("Deleting resources...");
    azure.resourceGroups().deleteByName("myResourceGroup");
    ```

2. 保存 App.java 文件。

## <a name="run-the-application"></a>运行应用程序

控制台应用程序从头到尾完成运行大约需要五分钟时间。

1. 若要运行应用程序，请使用此 Maven 命令：

    ```
    mvn compile exec:java
    ```

2. 在按 **Enter** 开始删除资源之前，可能需要在 Azure 门户中花几分钟时间来验证这些资源是否已创建。 单击部署状态以查看有关部署的信息。


## <a name="next-steps"></a>后续步骤
* 详细了解如何使用[用于 Java 的 Azure 库](/java/azure/java-sdk-azure-overview)。
