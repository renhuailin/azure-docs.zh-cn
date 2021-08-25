---
title: 教程：配置 Azure IoT Edge 设备 - IoT Edge 上的机器学习
description: 在本教程中，我们将运行 Linux 的 Azure 虚拟机配置为充当透明网关的 Azure IoT Edge 设备。
author: kgremban
ms.author: kgremban
ms.date: 2/5/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: amqp
ms.openlocfilehash: 445163b0d33f404845a66b7d885815d6336dac46
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726367"
---
# <a name="tutorial-configure-an-azure-iot-edge-device"></a>教程：配置 Azure IoT Edge 设备

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

在本文中，我们会将一个运行 Linux 的 Azure 虚拟机配置为充当透明网关的 Azure IoT Edge 设备。 借助透明网关配置，设备在不知道网关存在的情况下就能通过网关连接到 Azure IoT 中心。 同时，与 IoT 中心内的设备交互的用户也察觉不到中间网关设备。 最后，我们将通过向透明网关添加 IoT Edge 模块，向我们的系统添加边缘分析功能。

>[!NOTE]
>本教程中的概念适用于所有版本的 IoT Edge，而你创建的用于试用该方案的示例设备运行的是 IoT Edge 版本 1.1。

本文的步骤通常由云开发人员执行。

本教程的此部分介绍了如何：

> [!div class="checklist"]
>
> * 创建证书，以允许网关设备安全地连接到下游设备。
> * 创建 IoT Edge 设备。
> * 创建 Azure 虚拟机以模拟 IoT Edge 设备。

## <a name="prerequisites"></a>先决条件

有一系列教程介绍如何在 IoT Edge 上使用 Azure 机器学习，本文是其中的一篇。 这一系列中的每篇文章都环环相扣，后一篇以前一篇为基础。 如果你是直接转到本文的，请参阅本系列的[第一篇文章](tutorial-machine-learning-edge-01-intro.md)。

## <a name="create-certificates"></a>创建证书

充当网关的设备必须能够安全地连接到下游设备。 借助 IoT Edge 可以使用公钥基础结构 (PKI) 在设备之间建立安全连接。 在这种情况下，我们允许将下游 IoT 设备连接到充当透明网关的 IoT Edge 设备。 要维持合理的安全性，下游设备应确认 IoT Edge 设备的标识。 要详细了解 IoT Edge 设备如何使用证书，请查看 [Azure IoT Edge 证书使用详细信息](iot-edge-certs.md)。

在本部分，我们要使用到时会生成和运行的 Docker 映像来创建自签名证书。 我们选择用 Docker 映像来完成此步骤的原因是，它减少了在 Windows 开发计算机上创建证书所需的步骤数。 若要了解 Docker 映像可实现的自动化操作，请参阅[创建演示证书以测试 IoT Edge 设备功能](how-to-create-test-certificates.md)。

1. 登录到开发 VM。
1. 使用路径和名称 **c:\edgeCertificates** 创建一个新的文件夹。

1. 从 Windows 的“开始”菜单启动用于 Windows 的 Docker。

1. 打开 Visual Studio Code。

1. 选择“文件” > “打开文件夹”，然后选择“C:\\source\\IoTEdgeAndMlSample\\CreateCertificates”  。

1. 在“资源管理器”窗格中，右键单击“dockerfile”并选择“生成映像”  。

1. 在对话框中，接受映像名称和标记的默认值：**createcertificates: latest**。

    ![展示如何在 Visual Studio Code 中创建证书的屏幕截图。](media/tutorial-machine-learning-edge-05-configure-edge-device/create-certificates.png)

1. 等待生成完成。

    > [!NOTE]
    > 可能会出现一条有关缺少公钥的警告。 可放心忽略此警告。 同样，你将看到一条安全警告，其中建议检查或重置对映像的权限 - 对此映像而言，可放心忽略。

1. 在 Visual Studio Code 终端窗口中，运行 createcertificates 容器。

    ```cmd
    docker run --name createcertificates --rm -v c:\edgeCertificates:/edgeCertificates createcertificates /edgeCertificates
    ```

1. Docker 将提示提供对 c:\\ 驱动器的访问权限  。 选择“共享”  。

1. 在系统提示时提供凭据。

1. 容器运行完毕后，请检查 **c:\\edgeCertificates** 中是否存在以下文件：

    * c:\\edgeCertificates\\certs\\azure-iot-test-only.root.ca.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device-full-chain.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pem
    * c:\\edgeCertificates\\certs\\new-edge-device.cert.pfx
    * c:\\edgeCertificates\\private\\new-edge-device.key.pem

## <a name="upload-certificates-to-azure-key-vault"></a>将证书上传到 Azure Key Vault

为安全存储证书并使其可通过多台设备访问，我们将证书上传到 Azure 密钥保管库中。 如在前面的列表中看到的那样，我们有两种类型的证书文件：PFX 和 PEM。 我们将 PFX 文件视为密钥保管库证书，将其上传到密钥保管库。 PEM 文件是纯文本形式，我们将其视为密钥保管库机密。 我们之前通过运行 [Jupyter Notebook](tutorial-machine-learning-edge-04-train-model.md#run-the-jupyter-notebooks) 创建了 Azure 机器学习工作区，现在我们将使用与该工作区关联的密钥保管库实例。

1. 在 [Azure 门户](https://portal.azure.com)中，转到你的 Azure 机器学习工作区。

1. 在机器学习工作区的概述页中，找到 **密钥保管库** 的名称。

    ![展示如何复制密钥保管库名称的屏幕截图。](media/tutorial-machine-learning-edge-05-configure-edge-device/find-key-vault-name.png)

1. 在部署计算机上，将证书上传到 Key Vault。 将“\<subscriptionId\>”和“\<keyvaultname\>”替换为自己的资源信息 。

    ```powershell
    c:\source\IoTEdgeAndMlSample\CreateCertificates\upload-keyvaultcerts.ps1 -SubscriptionId <subscriptionId> -KeyVaultName <keyvaultname>
    ```

1. 根据提示登录到 Azure。

1. 脚本将运行几分钟时间，其输出中列出新的密钥保管库条目。

    ![展示密钥保管库脚本输出的屏幕截图。](media/tutorial-machine-learning-edge-05-configure-edge-device/key-vault-entries-output.png)

## <a name="register-an-iot-edge-device"></a>注册 IoT Edge 设备

若要将 Azure IoT Edge 设备连接到 IoT 中心，请先在中心注册设备。 我们从云端设备标识中提取连接字符串，用它在 IoT Edge 设备上配置运行时。 在配置的设备连接到中心后，我们便可以部署模块并发送消息了。 我们还可以通过更改 IoT 中心内相应的设备标识来更改物理 IoT Edge 设备的配置。

在本教程中，我们将使用 Visual Studio Code 注册新的设备标识。 也可以使用 Azure 门户或 Azure CLI 完成这些步骤。 无论选择哪种方法，请确保获取 IoT Edge 设备的设备连接字符串。 在 Azure 门户上设备的“详细信息”页中，可以找到设备连接字符串。

1. 在开发计算机上，打开 Visual Studio Code。

1. 从 Visual Studio Code“资源管理器”视图中，展开“Azure IoT 中心”框架 。

1. 选择省略号图标，然后选择“创建 IoT Edge 设备”。

1. 向设备提供一个名称。 为方便起见，我们使用名称 **aaTurbofanEdgeDevice**，以便它排在所列出设备的顶部。

1. 新设备将在设备列表中显示。

    ![展示 Visual Studio Code 资源管理器中的设备视图的屏幕截图。](media/tutorial-machine-learning-edge-05-configure-edge-device/iot-hub-devices-list.png)

## <a name="deploy-an-azure-virtual-machine"></a>部署 Azure 虚拟机

我们使用 Ubuntu 18.04 LTS 虚拟机，并安装和配置 Azure IoT Edge 运行时。 部署使用 [iotedge-vm-deploy](https://github.com/Azure/iotedge-vm-deploy) 项目存储库中维护的 [Azure 资源管理器模板](../azure-resource-manager/templates/overview.md)。 它使用模板中提供的连接字符串预配在上一步中注册的 IoT Edge 设备。

你可以使用 Azure 门户或 Azure CLI 来部署虚拟机。 我们将演示 Azure 门户步骤。 有关详细信息，请参阅[在 Ubuntu 虚拟机上运行 Azure IoT Edge](how-to-install-iot-edge-ubuntuvm.md)。

### <a name="deploy-using-deploy-to-azure-button"></a>使用“部署到 Azure”按钮进行部署

1. 若要使用 `iotedge-vm-deploy` ARM 模板部署 Ubuntu 18.04 LTS 虚拟机，请单击下面的按钮：

    [![iotedge-vm-deploy 的“部署到 Azure”按钮](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

1. 在新启动的窗口中，填写可用的窗体字段。

   | 字段 | 说明 |
   | - | - |
   | **订阅** | 要将虚拟机部署到的活动 Azure 订阅。 |
   | **资源组** | 用于包含该虚拟机及其相关资源的现有或新建的资源组。 |
   | **DNS 标签前缀** | 必需的值，用作虚拟机主机名的前缀。 |
   | **管理员用户名** | 一个用户名，将为其提供对部署的 root 特权。 |
   | **设备连接字符串** | 在预期 [IoT 中心](../iot-hub/about-iot-hub.md)内创建的设备的[设备连接字符串](./how-to-register-device.md)。 |
   | **VM 大小** | 要部署的虚拟机的 [大小](../cloud-services/cloud-services-sizes-specs.md)
   | **Ubuntu OS 版本** | 要在基础虚拟机上安装的 Ubuntu OS 版本。 |
   | **位置** | 要部署虚拟机的[地理区域](https://azure.microsoft.com/global-infrastructure/locations/)，此值默认为所选资源组的位置。 |
   | **身份验证类型** | 根据偏好选择“sshPublicKey”或“密码”。 |
   | **管理员密码或密钥** | SSH 公钥的值或密码的值，具体取决于所选的身份验证类型。 |

1. 填写完所有字段后，选中页面底部的复选框以接受条款，然后依次选择“查看 + 创建”和“创建”以开始部署。

1. 在 Azure 门户中，导航到你的虚拟机。 可以通过资源组或在门户登陆页面上的“Azure 服务”下选择“虚拟机”来找到它。

1. 记下虚拟机的“DNS 名称”。 你将需要使用它来登录到虚拟机。

## <a name="connect-to-your-iot-edge-device"></a>连接到 IoT Edge 设备

1. 打开命令提示符，并使用以下命令登录到虚拟机。 根据上一部分的内容输入自己的用户名和 DNS 名称信息。

    ```bash
    ssh <adminUsername>@<DNS_name>
    ```

1. 当系统提示验证主机的真实性时，请键入 **yes** 并按 **Enter**。

1. 根据提示提供密码。

1. Ubuntu 会显示一条欢迎消息，然后你应会看到类似于 `<username>@<machinename>:~$` 的提示。

## <a name="download-key-vault-certificates"></a>下载 Key Vault 证书

在本文的前面部分中，我们已将证书上传到 Key Vault，使其可供 IoT Edge 设备和叶设备使用。 叶设备是一个下游设备，它使用 IoT Edge 设备作为网关与 IoT 中心进行通信。

在本教程稍后部分，我们将处理叶设备。 在此部分中，请将证书下载到 IoT Edge 设备。

1. 从 Linux 虚拟机上的 SSH 会话，使用 Azure CLI 登录到 Azure。

    ```azurecli
    az login
    ```

1. 系统将提示你打开浏览器并转到 [Microsoft 设备登录](https://microsoft.com/devicelogin)页，然后提供唯一的代码。 你可在本地计算机上执行这些步骤。 完成身份验证后，关闭浏览器窗口。

1. 如果你成功通过身份验证，Linux VM 将进行登录并列出你的 Azure 订阅。

1. 设置要对 Azure CLI 命令使用的 Azure 订阅。

    ```azurecli
    az account set --subscription <subscriptionId>
    ```

1. 在 VM 上为证书创建一个目录。

    ```bash
    sudo mkdir /edgeMlCertificates
    ```

1. 下载存储在密钥保管库中的证书：new-edge-device-full-chain.cert.pem、new-edge-device.key.pem 和 azure-iot-test-only.root.ca.cert.pem。

    ```azurecli
    key_vault_name="<key vault name>"
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-full-chain-cert-pem -f /edgeMlCertificates/new-edge-device-full-chain.cert.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name new-edge-device-key-pem -f /edgeMlCertificates/new-edge-device.key.pem
    sudo az keyvault secret download --vault-name $key_vault_name --name azure-iot-test-only-root-ca-cert-pem -f /edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem
    ```

## <a name="update-the-iot-edge-device-configuration"></a>更新 IoT Edge 设备配置

IoT Edge 运行时使用 /etc/iotedge/config.yaml 文件来保留其配置。 我们需要更新此文件中的两部分信息：

* **证书**：用来与下游设备建立连接的证书
* **主机名**：VM IoT Edge 设备的完全限定域名 (FQDN)

通过直接编辑 config.yaml 文件来更新证书和主机名。

1. 打开 config.yaml 文件。

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. 更新 config.yaml 文件的 certificates 节：删除前导 **#** 并设置路径，使该文件如以下示例所示：

    ```yaml
    certificates:
      device_ca_cert: "/edgeMlCertificates/new-edge-device-full-chain.cert.pem"
      device_ca_pk: "/edgeMlCertificates/new-edge-device.key.pem"
      trusted_ca_certs: "/edgeMlCertificates/azure-iot-test-only.root.ca.cert.pem"
    ```

    确保 **certificates:** 行中不包含任何前置空格，并且每个嵌套证书都缩进了两个空格。

    在 nano 中右键单击会将剪贴板的内容粘贴到当前游标位置。 若要替换字符串，请使用箭头键转到要替换的字符串，删除该字符串，然后单击右键并粘贴缓冲区中的内容。

1. 在 Azure 门户中转到你的虚拟机。 复制“概述”部分中的 DNS 名称（计算机的 FQDN）  。

1. 将 FQDN 粘贴到 config.yml 文件的 hostname 节中。 确保以全小写形式输入名称。

    ```yaml
    hostname: '<machinename>.<region>.cloudapp.azure.com'
    ```

1. 按 **Ctrl+X**、**Y**、**Enter** 保存并关闭文件。

1. 重启 IoT Edge 守护程序。

    ```bash
    sudo systemctl restart iotedge
    ```

1. 检查 IoT Edge 守护程序的状态。 执行该命令后，输入 **:q** 退出。

    ```bash
    systemctl status iotedge
    ```

## <a name="troubleshooting"></a>疑难解答

如果在状态中看到了错误（带“\[ERROR\]”前缀的彩色文本），请检查守护程序日志了解详细错误信息。

   ```bash
   journalctl -u iotedge --no-pager --no-full
   ```

有关修复错误的详细信息，请查看[故障排除](troubleshoot.md)页。

## <a name="clean-up-resources"></a>清理资源

本教程是一系列文章的一部分，其中每篇文章都基于前一篇文章中介绍的内容。 请等到完成最后一篇教程之后才清理任何资源。

## <a name="next-steps"></a>后续步骤

我们刚刚完成了将 Azure VM 配置为 IoT Edge 透明网关的过程。 我们首先生成了测试证书并已将其上传到密钥保管库。 接下来，我们使用一个脚本和资源管理器模板，通过 Azure 市场中的“Ubuntu Server 16.04 LTS + Azure IoT Edge 运行时”映像部署了 VM。 在 VM 启动并运行后，我们通过 SSH 建立了连接。 然后我们登录到了 Azure，并从密钥保管库下载了证书。 我们通过更新 config.yaml 文件，对 IoT Edge 运行时的配置进行了多处更新。

请继续学习下一篇文章，了解如何构建 IoT Edge 模块。

> [!div class="nextstepaction"]
> [创建并部署自定义 IoT Edge 模块](tutorial-machine-learning-edge-06-custom-modules.md)
