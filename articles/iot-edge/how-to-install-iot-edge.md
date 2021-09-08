---
title: 安装 Azure IoT Edge | Microsoft Docs
description: 有关在 Windows 或 Linux 设备上安装 Azure IoT Edge 的说明
author: kgremban
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/28/2021
ms.author: kgremban
ms.openlocfilehash: 5ed9768f8cb1a6279eeb97a73d508a41984ef160
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123214338"
---
# <a name="install-or-uninstall-azure-iot-edge-for-linux"></a>安装或卸载 Azure IoT Edge for Linux

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

使用 Azure IoT Edge 运行时可将设备转变为 IoT Edge 设备。 该运行时可以部署在像 Raspberry Pi 一样小的设备上，也可以部署在像工业服务器一样大的设备上。 使用 IoT Edge 运行时配置设备后，即可开始从云中部署业务逻辑。 若要了解详细信息，请参阅[了解 Azure IoT Edge 运行时及其体系结构](iot-edge-runtime.md)。

本文列出了在 Linux 设备上安装 Azure IoT Edge 运行时的步骤。

## <a name="prerequisites"></a>先决条件

* [已注册的设备 ID](how-to-register-device.md)

  如果你已为设备注册了对称密钥身份验证，请准备好设备连接字符串。

  如果你已为设备注册了 X.509 自签名证书身份验证，则至少需要有一个用来注册设备的标识证书及其可在设备上使用的匹配私钥。

* Linux 设备

  具有 X64、ARM32 或 ARM64 Linux 设备。 Microsoft 提供了适用于 Ubuntu Server 18.04 和 Raspberry Pi OS Stretch 操作系统的安装包。

  有关生产方案目前支持哪些操作系统的最新信息，请参阅 [Azure IoT Edge 支持的系统](support.md#operating-systems)

  >[!NOTE]
  >对 ARM64 设备的支持为[公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

* 准备设备以访问 Microsoft 安装包。

  安装与设备操作系统匹配的存储库配置。

  * **Ubuntu Server 18.04**：

    ```bash
    curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
    ```

  * **Raspberry Pi OS Stretch**：

    ```bash
    curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
    ```

  将生成的列表复制到 sources.list.d 目录。

  ```bash
  sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
  ```

  安装 Microsoft GPG 公钥。

  ```bash
  curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
  sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
  ```

Azure IoT Edge 软件包受制于每个包（`usr/share/doc/{package-name}` 或 `LICENSE` 目录）中的许可条款。 请在使用包之前阅读许可条款。 安装和使用包即表示你接受这些条款。 如果不同意许可条款，请勿使用该包。

## <a name="install-a-container-engine"></a>安装容器引擎

Azure IoT Edge 依赖于 OCI 兼容的容器运行时。 对于生产方案，建议使用 Moby 引擎。 Moby 引擎是官方唯一支持用于 Azure IoT Edge 的容器引擎。 Docker CE/EE 容器映像与 Moby 运行时兼容。

更新设备上的包列表。

   ```bash
   sudo apt-get update
   ```

安装 Moby 引擎。

   ```bash
   sudo apt-get install moby-engine
   ```

如果在安装 Moby 容器引擎时出现错误，请验证 Linux 内核的 Moby 兼容性。 有些嵌入式设备制造商寄送的设备映像包含的自定义 Linux 内核没有确保容器引擎兼容所需的功能。 运行以下命令，该命令使用 Moby 提供的 [check-config 脚本](https://github.com/moby/moby/blob/master/contrib/check-config.sh)来检查内核配置：

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

在该脚本的输出中，检查 `Generally Necessary` 和 `Network Drivers` 下的所有项是否都已启用。 如果缺少某些功能，请启用它们，方法是通过源重新构建内核，然后选择关联的模块，将其包括在相应的内核 .config 中。同样，如果使用内核配置生成器（例如 `defconfig` 或 `menuconfig`），请找到并启用相应的功能，然后以相应方式重新构建内核。 部署新修改的内核以后，请再次运行 check-config 脚本，验证是否已成功启用所有必需功能。

## <a name="install-iot-edge"></a>安装 IoT Edge

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

IoT Edge 安全守护程序提供和维护 IoT Edge 设备上的安全标准。 守护程序在每次开机时启动，并通过启动 IoT Edge 运行时的其余部分来启动设备。

本部分中的步骤表示在具有 Internet 连接的设备上安装最新版本的典型过程。 如果需要安装特定版本（如预发行版）或需要在脱机状态下安装，请按照本文后面的[脱机或特定版本安装](#offline-or-specific-version-installation-optional)步骤进行操作。

更新设备上的包列表。

   ```bash
   sudo apt-get update
   ```

安装 IoT Edge 版本 1.1.* 以及 libiothsm-std 包：

   ```bash
   sudo apt-get install iotedge
   ```

>[!NOTE]
>IoT Edge 1.1 版本是 IoT Edge 的长期支持分支。 如果运行的是旧版本，建议安装或更新到最新补丁，因为旧版本不再受支持。

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

IoT Edge 服务在 IoT Edge 设备上提供并维护安全标准。 该服务在每次开机时启动，并通过启动 IoT Edge 运行时的其余部分来启动设备。

IoT 标识服务在 IoT Edge 1.2 版中引入。 此服务处理 IoT Edge 以及需要与 IoT 中心通信的其他设备组件的标识预配和管理。

本部分中的步骤表示在具有 Internet 连接的设备上安装最新版本的典型过程。 如果需要安装特定版本（如预发行版）或需要在脱机状态下安装，请按照本文后面的[脱机或特定版本安装](#offline-or-specific-version-installation-optional)步骤进行操作。

>[!NOTE]
>本部分的步骤说明如何安装 IoT Edge 版本 1.2。
>
>如果你已经有运行旧版本的 IoT Edge 设备并想要升级到 1.2，请按照[更新 IoT Edge 安全守护程序和运行时](how-to-update-iot-edge.md)中的步骤操作。 1\.2 版与先前的 IoT Edge 版本有很大不同，因此需要采取特定的步骤进行升级。

更新设备上的包列表。

   ```bash
   sudo apt-get update
   ```

查看可用的 IoT Edge 和 IoT 标识服务版本。

   ```bash
   apt list -a aziot-edge aziot-identity-service
   ```

要安装最新版本的 IoT Edge 和 IoT 标识服务包，请使用以下命令：

   ```bash
   sudo apt-get install aziot-edge
   ```

如果选择安装与最新版本不同的 IoT Edge 版本，请确保为 `aziot-edge` 和 `aziot-identity-service` 服务安装相同版本。

<!-- end 1.2 -->
::: moniker-end

## <a name="provision-the-device-with-its-cloud-identity"></a>为设备预配其云标识

现在容器引擎和 IoT Edge 运行时已安装在设备上，你可以开始下一步操作，即，使用设备的云标识和身份验证信息来设置设备。

根据要使用的身份验证类型，选择下一部分：

* [选项 1：使用对称密钥进行身份验证](#option-1-authenticate-with-symmetric-keys)
* [选项 2：使用 X.509 证书进行身份验证](#option-2-authenticate-with-x509-certificates)

### <a name="option-1-authenticate-with-symmetric-keys"></a>选项 1：使用对称密钥进行身份验证

此时，IoT Edge 运行时安装在 Linux 设备上，你需要使用设备的云标识和身份验证信息来预配设备。

本部分逐步讲解了如何为设备预配对称密钥身份验证。 你应当已在 IoT 中心注册了设备，并从设备信息中检索了连接字符串。 如果没有，请按照[在 IoT 中心注册 IoT Edge 设备](how-to-register-device.md)中的步骤进行操作。

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

在 IoT Edge 设备上，打开配置文件。

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

找到文件的预配配置，并取消注释“使用连接字符串手动预配配置”部分（如果尚未取消注释）。

   ```yml
   # Manual provisioning configuration using a connection string
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
   ```

使用 IoT Edge 设备的连接字符串更新 **device_connection_string** 的值。 请确保注释掉任何其他预配部分。请确保 **provisioning:** 行前面没有空格，并且嵌套项缩进了两个空格。

将剪贴板内容粘贴到 Nano `Shift+Right Click` 或按 `Shift+Insert`。

保存并关闭该文件。

   `CTRL + X`, `Y`, `Enter`

在配置文件中输入预配信息后，重启守护程序：

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

基于在安装 IoT Edge 的过程中提供的模板文件为你的设备创建配置文件。

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

在 IoT Edge 设备上，打开配置文件。

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

找到文件的预配部分，并取消注释“使用连接字符串进行手动预配”行。

   ```toml
   # Manual provisioning with connection string
   [provisioning]
   source = "manual"
   connection_string = "<ADD DEVICE CONNECTION STRING HERE>"
   ```

使用 IoT Edge 设备的连接字符串更新 connection_string 的值。

将剪贴板内容粘贴到 Nano `Shift+Right Click` 或按 `Shift+Insert`。

保存并关闭该文件。

   `CTRL + X`, `Y`, `Enter`

在配置文件中输入预配信息后，应用所做的更改：

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

### <a name="option-2-authenticate-with-x509-certificates"></a>选项 2：使用 X.509 证书进行身份验证

此时，IoT Edge 运行时安装在 Linux 设备上，你需要使用设备的云标识和身份验证信息来预配设备。

本部分逐步讲解了如何为设备预配 X.509 证书身份验证。 你应当已在 IoT 中心注册了设备，并提供了与 IoT Edge 设备上的证书和私钥相匹配的指纹。 如果没有，请按照[在 IoT 中心注册 IoT Edge 设备](how-to-register-device.md)中的步骤进行操作。

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

在 IoT Edge 设备上，打开配置文件。

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

找到文件的预配配置部分，并取消注释 **Manual provisioning configuration using an X.509 identity certificate** 部分。 请确保注释掉任何其他预配部分。请确保 **provisioning:** 行前面没有空格，并且嵌套项缩进了两个空格。

   ```yml
   # Manual provisioning configuration using an x.509 identity certificate
   provisioning:
     source: "manual"
     authentication:
       method: "x509"
       iothub_hostname: "<REQUIRED IOTHUB HOSTNAME>"
       device_id: "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
       identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
       identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

更新以下字段：

* **iothub_hostname**：设备将连接到的 IoT 中心的主机名。 例如 `{IoT hub name}.azure-devices.net`。
* **device_id**：注册设备时提供的 ID。
* **identity_cert**：设备上标识证书的 URI。 例如 `file:///path/identity_certificate.pem`。
* **identity_pk**：所提供标识证书私钥文件的 URI。 例如 `file:///path/identity_key.pem`。

保存并关闭该文件。

   `CTRL + X`, `Y`, `Enter`

在配置文件中输入预配信息后，重启守护程序：

   ```bash
   sudo systemctl restart iotedge
   ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

基于在安装 IoT Edge 的过程中提供的模板文件为你的设备创建配置文件。

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

在 IoT Edge 设备上，打开配置文件。

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

找到文件的预配部分，并取消注释“使用 X.509 标识证书进行手动预配”行。 确保所有其他预配部分都已被注释掉。

   ```toml
   # Manual provisioning with x.509 certificates
   [provisioning]
   source = "manual"
   iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
   device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"

   [provisioning.authentication]
   method = "x509"

   identity_cert = "<REQUIRED URI OR POINTER TO DEVICE IDENTITY CERTIFICATE>"

   identity_pk = "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
   ```

更新以下字段：

* **iothub_hostname**：设备将连接到的 IoT 中心的主机名。 例如 `{IoT hub name}.azure-devices.net`。
* **device_id**：注册设备时提供的 ID。
* **identity_cert**：设备上标识证书的 URI，例如 `file:///path/identity_certificate.pem`。 或者，使用 EST 或本地证书颁发机构动态颁发证书。
* **identity_pk**：所提供标识证书的私钥文件 URI，例如 `file:///path/identity_key.pem`。 或者，提供 PKCS#11 URI，然后在配置文件的 PKCS#11 部分提供你的配置信息。

保存并关闭该文件。

   `CTRL + X`, `Y`, `Enter`

在配置文件中输入预配信息后，应用所做的更改：

   ```bash
   sudo iotedge config apply
   ```

<!-- end 1.2 -->
::: moniker-end

## <a name="verify-successful-configuration"></a>验证成功的配置

验证是否已在 IoT Edge 设备上成功安装并配置运行时。

>[!TIP]
>需要提升权限才能运行 `iotedge` 命令。 安装 IoT Edge 运行时后从计算机中注销并第一次重新登录后，你的权限将自动更新。 在此之前，请在命令前使用 `sudo`。

检查以确保 IoT Edge 系统服务正在运行。

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   sudo systemctl status iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system status
   ```

成功的状态响应是 `Ok`。

::: moniker-end

若需排查服务问题，请检索服务日志。

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

   ```bash
   journalctl -u iotedge
   ```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

   ```bash
   sudo iotedge system logs
   ```

::: moniker-end

使用 `check` 工具验证设备的配置和连接状态。

   ```bash
   sudo iotedge check
   ```

>[!TIP]
>请始终使用 `sudo` 来运行检查工具，即使权限已更新。 该工具需要提升的权限才能访问配置文件以验证配置状态。

查看在 IoT Edge 设备上运行的所有模块。 当服务首次启动时，你应当只会看到 edgeAgent 模块正在运行。 edgeAgent 模块会默认运行，用于安装并启动部署到设备的任何其他模块。

   ```bash
   sudo iotedge list
   ```

创建新的 IoT Edge 设备时，它将在 Azure 门户中显示状态代码 `417 -- The device's deployment configuration is not set`。 此状态正常，表示设备已准备好接收模块部署。

## <a name="offline-or-specific-version-installation-optional"></a>脱机或特定版本安装（可选）

本部分中的步骤适用于标准安装步骤未涵盖的方案。 这可能包括：

* 脱机安装 IoT Edge
* 安装候选发布版本

如果想要安装特定版本的无法通过 `apt-get install` 获取的 Azure IoT Edge 运行时，请使用本部分中的步骤。 Microsoft 包列表仅包含有限的一组最新版本及其子版本，因此，这些步骤适用于想要安装较旧版本或候选发布版本的任何用户。

使用 curl 命令，可以直接从 IoT Edge GitHub 存储库将组件文件作为目标。

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

1. 导航到 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)，找到需要将其作为目标的发行版。

2. 展开该版本的“资产”部分。

3. 每个版本应该都有用于 IoT Edge 安全守护程序和 hsmlib 的新文件。 如果要在脱机设备上安装 IoT Edge，请提前下载这些文件。 否则，请使用以下命令来更新这些组件。

   1. 找到与 IoT Edge 设备的体系结构匹配的 **libiothsm-std** 文件。 右键单击文件链接并复制链接地址。

   2. 将复制的链接用在以下命令中，安装该版本的 hsmlib：

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo apt-get install ./libiothsm-std.deb
      ```

   3. 找到与 IoT Edge 设备的体系结构匹配的 **iotedge** 文件。 右键单击文件链接并复制链接地址。

   4. 将复制的链接用在以下命令中，安装该版本的 IoT Edge 安全守护程序：

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo apt-get install ./iotedge.deb
      ```

<!-- end 1.1 -->
::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

>[!NOTE]
>如果设备当前运行 IoT Edge 1.1 或更早版本，请先卸载 iotedge 和 libiothsm-std 包，然后再执行本部分中的步骤 。 有关详细信息，请参阅[ 1.0 或 1.1 更新到 1.2](how-to-update-iot-edge.md#special-case-update-from-10-or-11-to-12)。

1. 导航到 [Azure IoT Edge 版本](https://github.com/Azure/azure-iotedge/releases)，找到需要将其作为目标的发行版。

2. 展开该版本的“资产”部分。

3. 每个版本都应有用于 IoT Edge 和标识服务的新文件。 如果要在脱机设备上安装 IoT Edge，请提前下载这些文件。 否则，请使用以下命令来更新这些组件。

   1. 找到与你的 IoT Edge 设备体系结构匹配的 aziot-identity-service 文件。 右键单击文件链接并复制链接地址。

   2. 在以下命令中使用复制的链接来安装该版本的标识服务：

      ```bash
      curl -L <identity service link> -o aziot-identity-service.deb && sudo apt-get install ./aziot-identity-service.deb
      ```

   3. 找到与你的 IoT Edge 设备体系结构匹配的 az iot-edge 文件。 右键单击文件链接并复制链接地址。

   4. 在以下命令中使用复制的链接来安装该版本的 IoT Edge。

      ```bash
      curl -L <iotedge link> -o aziot-edge.deb && sudo apt-get install ./aziot-edge.deb
      ```

<!-- end 1.2 -->
::: moniker-end

现在容器引擎和 IoT Edge 运行时已安装在设备上，你可以开始下一步操作，即，[使用设备的云标识来预配设备](#provision-the-device-with-its-cloud-identity)。

## <a name="uninstall-iot-edge"></a>卸载 IoT Edge

若要从设备中删除 IoT Edge 安装，请使用以下命令。

删除 IoT Edge 运行时。

<!-- 1.1 -->
::: moniker range="iotedge-2018-06"

```bash
sudo apt-get remove iotedge
```

::: moniker-end

<!-- 1.2 -->
::: moniker range=">=iotedge-2020-11"

```bash
sudo apt-get remove aziot-edge
```

::: moniker-end

如果要删除与 IoT Edge 关联的所有文件（包括配置文件），请使用 `--purge` 标记。 如果要重新安装 IoT Edge 并在以后使用相同的配置信息，请忽略此标记。

删除 IoT Edge 运行时后，已创建的所有容器都会停止，但仍存在于设备上。 查看所有容器以了解哪些容器仍然存在。

```bash
sudo docker ps -a
```

从设备中删除容器，包括两个运行时容器。

```bash
sudo docker rm -f <container name>
```

最后，从设备中删除容器运行时。

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>后续步骤

转到[部署 IoT Edge 模块](how-to-deploy-modules-portal.md)，了解如何将模块部署到设备上。
