---
title: 如何安装和运行空间分析容器 - 计算机视觉
titleSuffix: Azure Cognitive Services
description: 利用空间分析容器可以检测人员和距离。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 06/08/2021
ms.author: pafarley
ms.openlocfilehash: 2261bf9f52747bee8617b4393c207703a252a1ad
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2021
ms.locfileid: "113005989"
---
# <a name="install-and-run-the-spatial-analysis-container-preview"></a>安装和运行空间分析容器（预览版）

借助空间分析容器可以分析实时流视频，以理解人员之间的空间关系、他们的动作以及与物理环境中对象的交互。 容器非常适合用于满足特定的安全性和数据管理要求。

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services)
* 拥有 Azure 订阅后，在 Azure 门户中为标准 S1 层<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="创建计算机视觉资源"  target="_blank">创建计算机视觉资源</a>，以获取密钥和终结点。 部署后，单击“转到资源”。
    * 需要使用所创建资源的密钥和终结点来运行空间分析容器。 稍后你将使用该密钥和终结点。

### <a name="spatial-analysis-container-requirements"></a>空间分析容器要求

若要运行空间分析容器，需要一台搭载 [NVIDIA Tesla T4 GPU](https://www.nvidia.com/en-us/data-center/tesla-t4/) 的计算设备。 建议使用具有 GPU 加速功能的 [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/)，但是，该容器可在满足最低要求的任何其他台式机上运行。 我们将此设备称为主计算机。

#### <a name="azure-stack-edge-device"></a>[Azure Stack Edge 设备](#tab/azure-stack-edge)

Azure Stack Edge 是一个硬件即服务解决方案，也是一台具有网络数据传输功能的支持 AI 的边缘计算设备。 有关详细准备工作和设置说明，请参阅 [Azure Stack Edge 文档](../../databox-online/azure-stack-edge-deploy-prep.md)。

#### <a name="desktop-machine"></a>[台式机](#tab/desktop-machine)

#### <a name="minimum-hardware-requirements"></a>最低硬件需求

* 4 GB 系统 RAM
* 4 GB GPU RAM
* 8 核 CPU
* 1 个 NVIDIA Tesla T4 GPU
* 20 GB 机械硬盘 (HDD) 空间

#### <a name="recommended-hardware"></a>推荐硬件

* 32 GB 系统 RAM
* 16 GB GPU RAM
* 8 核 CPU
* 2 个 NVIDIA Tesla T4 GPU
* 50 GB 固态硬盘 (SSD) 空间

在本文中，你将下载并安装以下软件包。 主计算机必须能够运行以下各项（请参阅后面的说明）：

* [NVIDIA 图形驱动程序](https://docs.nvidia.com/datacenter/tesla/tesla-installation-notes/index.html)和 [NVIDIA CUDA 工具包](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)
* [NVIDIA MPS](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf)（多进程服务）的配置。
* [Docker CE](https://docs.docker.com/install/linux/docker-ce/ubuntu/#install-docker-engine---community-1) 和 [NVIDIA-Docker2](https://github.com/NVIDIA/nvidia-docker) 
* [Azure IoT Edge](../../iot-edge/how-to-install-iot-edge.md) 运行时。

#### <a name="azure-vm-with-gpu"></a>[带 GPU 的 Azure VM](#tab/virtual-machine)
在本示例中，我们将使用带有一个 K80 GPU 的 [NC 系列 VM](../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

---

| 要求 | 说明 |
|--|--|
| 照相机 | 空间分析容器并非仅限用于特定的相机品牌。 相机设备需要：支持实时流式处理协议 (RTSP) 和 H.264 编码、可供主机计算机访问，并能够以 15FPS 和 1080p 的分辨率进行流式处理。 |
| Linux OS | 必须在主计算机上安装 [Ubuntu Desktop 18.04 LTS](http://releases.ubuntu.com/18.04/)。  |

## <a name="set-up-the-host-computer"></a>设置主计算机

建议使用 Azure Stack Edge 设备作为主计算机。 如果要配置其他设备，请单击“台式机”；如果要使用 VM，请单击“虚拟机”。 

#### <a name="azure-stack-edge-device"></a>[Azure Stack Edge 设备](#tab/azure-stack-edge)

### <a name="configure-compute-on-the-azure-stack-edge-portal"></a>在 Azure Stack Edge 门户上配置计算 
 
空间分析使用 Azure Stack Edge 的计算功能来运行 AI 解决方案。 若要启用计算功能，请确保： 

* 已[连接并激活](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md) Azure Stack Edge 设备。 
* 具有一个运行 PowerShell 5.0 或更高版本的 Windows 客户端系统，以便能够访问该设备。  
* 若要部署 Kubernetes 群集，需要通过 [Azure 门户](https://portal.azure.com/)上的“本地 UI”配置 Azure Stack Edge 设备： 
  1. 在 Azure Stack Edge 设备上启用计算功能。 若要启用计算，请在设备的 Web 界面中转到“计算”页。 
  2. 选择要为其启用计算的网络接口，然后单击“启用”。 这会在设备中的该网络接口上创建一个虚拟交换机。
  3. 将 Kubernetes 测试节点 IP 地址和 Kubernetes 外部服务 IP 地址留空。
  4. 单击“应用”。 此操作可能需要大约两分钟。 

![配置计算](media/spatial-analysis/configure-compute.png)

### <a name="set-up-an-edge-compute-role-and-create-an-iot-hub-resource"></a>设置 Edge 计算角色并创建 IoT 中心资源

在 [Azure 门户](https://portal.azure.com/)中导航到你的 Azure Stack Edge 资源。 在“概述”页或导航列表中，单击 Edge 计算的“入门”按钮。  在“配置边缘计算”磁贴中单击“配置”。 ****   

![链接](media/spatial-analysis/configure-edge-compute-tile.png)

在“配置边缘计算”页中，选择现有的 IoT 中心或选择新建一个。 **** 默认将使用标准 (S1) 定价层来创建 IoT 中心资源。 若要使用免费层 IoT 中心资源，请创建一个资源并将其选中。 IoT 中心资源会使用 Azure Stack Edge 资源所用的同一订阅和资源组。 

单击“创建”。 创建 IoT 中心资源可能需要几分钟时间。 创建 IoT 中心资源后，“配置 Edge 计算”磁贴会更新，以显示新配置 **** 。 若要确认是否已配置 Edge 计算角色，请在“配置计算”磁贴上选择“查看配置” ****  **** 。

如果在 Edge 设备上设置了 Edge 计算角色，则会创建两个设备：一个 IoT 设备，一个 IoT Edge 设备。 可在 IoT 中心资源中查看这两个设备。 Azure IoT Edge 运行时已在 IoT Edge 设备上运行。

> [!NOTE]
> * 目前只有 Linux 平台支持 IoT Edge 设备。 如需有关 Azure Stack Edge 设备故障排除的帮助，请参阅[日志记录和故障排除](spatial-analysis-logging.md)一文。
> * 若要详细了解如何将 IoT Edge 设备配置为通过代理服务器进行通信，请参阅[将 IoT Edge 设备配置为通过代理服务器进行通信](../../iot-edge/how-to-configure-proxy-support.md#azure-portal)

###  <a name="enable-mps-on-azure-stack-edge"></a>在 Azure Stack Edge 上启用 MPS 

1. 以管理员身份运行 Windows PowerShell 会话。 

2. 确保 Windows 远程管理服务正在客户端上运行。 在 PowerShell 终端中使用以下命令 
    
    ```powershell
    winrm quickconfig
    ```
    
    如果看到了有关防火墙异常的警告，请检查网络连接类型，并参阅 [Windows 远程管理](/windows/win32/winrm/installation-and-configuration-for-windows-remote-management)文档。

3. 为设备 IP 地址分配一个变量。 
    
    ```powershell
    $ip = "<device-IP-address>" 
    ```
    
4. 若要将设备的 IP 地址添加到客户端的受信任主机列表，请使用以下命令： 
    
    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force 
    ```

5. 在设备上启动 Windows PowerShell 会话。 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell 
    ```

6. 根据提示提供密码。 使用登录到本地 Web UI 时所用的同一密码。 默认的本地 Web UI 密码为 `Password1`。

键入 `Start-HcsGpuMPS` 以在设备上启动 MPS 服务。 

如需有关 Azure Stack Edge 设备故障排除的帮助，请参阅 [Azure Stack Edge 设备故障排除](spatial-analysis-logging.md#troubleshooting-the-azure-stack-edge-device) 

#### <a name="desktop-machine"></a>[台式机](#tab/desktop-machine)

如果你的主计算机不是 Azure Stack Edge 设备，请按照以下说明操作。

#### <a name="install-nvidia-cuda-toolkit-and-nvidia-graphics-drivers-on-the-host-computer"></a>在主计算机上安装 NVIDIA CUDA 工具包和 Nvidia 图形驱动程序

使用以下 bash 脚本安装所需的 Nvidia 图形驱动程序及 CUDA 工具包。

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/cuda-ubuntu1804.pin
sudo mv cuda-ubuntu1804.pin /etc/apt/preferences.d/cuda-repository-pin-600
sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/7fa2af80.pub
sudo add-apt-repository "deb http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1804/x86_64/ /"
sudo apt-get update
sudo apt-get -y install cuda
```

重新启动计算机并运行以下命令。

```bash
nvidia-smi
```

你会看到以下输出。

![NVIDIA 驱动程序输出](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-host-computer"></a>在主计算机上安装 Docker CE 和 nvidia-docker2

在主计算机上安装 Docker CE。

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```

安装 *nvidia-docker-2* 软件包。

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
sudo apt-get update
sudo apt-get install -y docker-ce nvidia-docker2
sudo systemctl restart docker
```

## <a name="enable-nvidia-mps-on-the-host-computer"></a>在主计算机上启用 NVIDIA MPS

> [!TIP]
> * 如果 GPU 计算功能低于 7.x（Volta 之前的产品），请不要安装 MPS。 有关参考信息，请参阅 [CUDA 兼容性](https://docs.nvidia.com/deploy/cuda-compatibility/index.html#support-title)。 
> * 在主计算机上的终端窗口中运行 MPS 指令。 不要在 Docker 容器实例内部执行此操作。

为获得最佳性能和利用率，请为 [NVIDIA 多进程服务 (MPS)](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf) 配置主计算机的 GPU。 在主计算机上的终端窗口中运行 MPS 指令。

```bash
# Set GPU(s) compute mode to EXCLUSIVE_PROCESS
sudo nvidia-smi --compute-mode=EXCLUSIVE_PROCESS

# Cronjob for setting GPU(s) compute mode to EXCLUSIVE_PROCESS on boot
echo "SHELL=/bin/bash" > /tmp/nvidia-mps-cronjob
echo "PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin" >> /tmp/nvidia-mps-cronjob
echo "@reboot   root    nvidia-smi --compute-mode=EXCLUSIVE_PROCESS" >> /tmp/nvidia-mps-cronjob

sudo chown root:root /tmp/nvidia-mps-cronjob
sudo mv /tmp/nvidia-mps-cronjob /etc/cron.d/

# Service entry for automatically starting MPS control daemon
echo "[Unit]" > /tmp/nvidia-mps.service
echo "Description=NVIDIA MPS control service" >> /tmp/nvidia-mps.service
echo "After=cron.service" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Service]" >> /tmp/nvidia-mps.service
echo "Restart=on-failure" >> /tmp/nvidia-mps.service
echo "ExecStart=/usr/bin/nvidia-cuda-mps-control -f" >> /tmp/nvidia-mps.service
echo "" >> /tmp/nvidia-mps.service
echo "[Install]" >> /tmp/nvidia-mps.service
echo "WantedBy=multi-user.target" >> /tmp/nvidia-mps.service

sudo chown root:root /tmp/nvidia-mps.service
sudo mv /tmp/nvidia-mps.service /etc/systemd/system/

sudo systemctl --now enable nvidia-mps.service
```

## <a name="configure-azure-iot-edge-on-the-host-computer"></a>在主计算机上配置 Azure IoT Edge

若要在主计算机上部署空间分析容器，请使用标准 (S1) 或免费 (F0) 定价层创建 [Azure IoT 中心](../../iot-hub/iot-hub-create-through-portal.md)服务的实例。 

使用 Azure CLI 创建 Azure IoT 中心的实例。 请视情况替换参数。 或者，也可以在 [Azure 门户](https://portal.azure.com/)中创建 Azure IoT 中心。

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```
```bash
sudo az login
```
```bash
sudo az account set --subscription "<name or ID of Azure Subscription>"
```
```bash
sudo az group create --name "<resource-group-name>" --location "<your-region>"
```
有关可用区域，请参阅[区域支持](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)。
```bash
sudo az iot hub create --name "<iothub-group-name>" --sku S1 --resource-group "<resource-group-name>"
```
```bash
sudo az iot hub device-identity create --hub-name "<iothub-name>" --device-id "<device-name>" --edge-enabled
```

需要安装 [Azure IoT Edge](../../iot-edge/how-to-install-iot-edge.md) 版本 1.0.9。 遵循以下步骤下载正确的版本：

Ubuntu Server 18.04：
```bash
curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
```

复制生成的列表。
```bash
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
```

安装 Microsoft GPG 公钥。

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
```
```bash
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

更新设备上的包列表。

```bash
sudo apt-get update
```

安装版本 1.0.9：

```bash
sudo apt-get install iotedge=1.0.9* libiothsm-std=1.0.9*
```

接下来，使用[连接字符串](../../iot-edge/how-to-register-device.md)将主计算机注册为 IoT 中心实例中的 IoT Edge 设备。

需将 IoT Edge 设备连接到 Azure IoT 中心。 需要从前面创建的 IoT Edge 设备中复制该连接字符串。 或者，可以在 Azure CLI 中运行以下命令。

```bash
sudo az iot hub device-identity connection-string show --device-id my-edge-device --hub-name test-iot-hub-123
```

在主计算机上打开 `/etc/iotedge/config.yaml` 进行编辑。 请将 `ADD DEVICE CONNECTION STRING HERE` 替换为连接字符串。 保存并关闭该文件。 运行此命令以重启主计算机上的 IoT Edge 服务。

```bash
sudo systemctl restart iotedge
```

通过 [Azure 门户](../../iot-edge/how-to-deploy-modules-portal.md)或 [Azure CLI](../cognitive-services-apis-create-account-cli.md?tabs=windows) 将空间分析容器部署为主计算机上的 IoT 模块。 如果使用门户，请将映像 URI 设置为 Azure 容器注册表的位置。 

如果使用 Azure CLI，请执行以下步骤来部署容器。

#### <a name="azure-vm-with-gpu"></a>[带 GPU 的 Azure VM](#tab/virtual-machine)

也可以使用搭载 GPU 的 Azure 虚拟机来运行空间分析。 以下示例将使用带有一个 K80 GPU 的 [NC 系列](../../virtual-machines/nc-series.md?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) VM。

#### <a name="create-the-vm"></a>创建 VM

在 Azure 门户中打开 [创建虚拟机](https://ms.portal.azure.com/#create/Microsoft.VirtualMachine) 向导。

为 VM 命名，选择“(美国)美国西部 2”作为区域。 

> [!IMPORTANT]
> 请务必将 `Availability Options` 设置为“无需基础结构冗余”。 参阅下图完成配置，并参阅后续步骤找到正确的 VM 大小。 

:::image type="content" source="media/spatial-analysis/virtual-machine-instance-details.jpg" alt-text="虚拟机配置详细信息。" lightbox="media/spatial-analysis/virtual-machine-instance-details.jpg":::

若要查找 VM 大小，请选择“查看所有大小”，然后查看“非高级存储 VM 大小”的列表，如下所示。

:::image type="content" source="media/spatial-analysis/virtual-machine-sizes.png" alt-text="虚拟机大小。" lightbox="media/spatial-analysis/virtual-machine-sizes.png":::

然后选择“NC6”或“NC6_Promo”。 

:::image type="content" source="media/spatial-analysis/promotional-selection.png" alt-text="促销选项" lightbox="media/spatial-analysis/promotional-selection.png":::

接下来创建 VM。 创建后，在 Azure 门户中导航到 VM 资源，然后在左窗格中选择 `Extensions`。 此时会显示扩展窗口，其中包含所有可用扩展。 选择 `NVIDIA GPU Driver Extension`，单击“创建”，然后完成向导。

成功应用扩展后，在 Azure 门户中导航到 VM 主页，然后单击 `Connect`。 可以通过 SSH 或 RDP 访问 VM。 RDP 很有帮助，因为它可让你查看可视化工具窗口（稍后将予以说明）。 遵循[这些步骤](../../virtual-machines/linux/use-remote-desktop.md)配置 RDP 访问，然后与 VM 建立远程桌面连接。

### <a name="verify-graphics-drivers-are-installed"></a>验证是否已安装图形驱动程序

运行以下命令以验证是否已成功安装图形驱动程序。 

```bash
nvidia-smi
```

你会看到以下输出。

![NVIDIA 驱动程序输出](media/spatial-analysis/nvidia-driver-output.png)

### <a name="install-docker-ce-and-nvidia-docker2-on-the-vm"></a>在 VM 上安装 Docker CE 和 nvidia-docker2

请逐条运行以下命令，以在 VM 上安装 Docker CE 和 nvidia-docker2。

在主计算机上安装 Docker CE。

```bash
sudo apt-get update
```
```bash
sudo apt-get install -y apt-transport-https ca-certificates curl gnupg-agent software-properties-common
```
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
```bash
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```
```bash
sudo apt-get update
```
```bash
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```


安装 *nvidia-docker-2* 软件包。

```bash
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
```
```bash
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
```
```bash
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list
```
```bash
sudo apt-get update
```
```bash
sudo apt-get install -y docker-ce nvidia-docker2
```
```bash
sudo systemctl restart docker
```

设置并配置 VM 后，请遵循以下步骤配置 Azure IoT Edge。 

## <a name="configure-azure-iot-edge-on-the-vm"></a>在 VM 上配置 Azure IoT Edge

若要在 VM 上部署空间分析容器，请使用标准 (S1) 或免费 (F0) 定价层创建 [Azure IoT 中心](../../iot-hub/iot-hub-create-through-portal.md)服务的实例。

使用 Azure CLI 创建 Azure IoT 中心的实例。 请视情况替换参数。 或者，也可以在 [Azure 门户](https://portal.azure.com/)中创建 Azure IoT 中心。

```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```
```bash
sudo az login
```
```bash
sudo az account set --subscription "<name or ID of Azure Subscription>"
```
```bash
sudo az group create --name "<resource-group-name>" --location "<your-region>"
```
有关可用区域，请参阅[区域支持](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)。
```bash
sudo az iot hub create --name "<iothub-group-name>" --sku S1 --resource-group "<resource-group-name>"
```
```bash
sudo az iot hub device-identity create --hub-name "<iothub-name>" --device-id "<device-name>" --edge-enabled
```

需要安装 [Azure IoT Edge](../../iot-edge/how-to-install-iot-edge.md) 版本 1.0.9。 遵循以下步骤下载正确的版本：

Ubuntu Server 18.04：
```bash
curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
```

复制生成的列表。
```bash
sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
```

安装 Microsoft GPG 公钥。

```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
```
```bash
sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
```

更新设备上的包列表。

```bash
sudo apt-get update
```

安装版本 1.0.9：

```bash
sudo apt-get install iotedge=1.0.9* libiothsm-std=1.0.9*
```

接下来，使用[连接字符串](../../iot-edge/how-to-register-device.md)将 VM 注册为 IoT 中心实例中的 IoT Edge 设备。

需将 IoT Edge 设备连接到 Azure IoT 中心。 需要从前面创建的 IoT Edge 设备中复制该连接字符串。 或者，可以在 Azure CLI 中运行以下命令。

```bash
sudo az iot hub device-identity connection-string show --device-id my-edge-device --hub-name test-iot-hub-123
```

在 VM 上，打开 `/etc/iotedge/config.yaml` 进行编辑。 请将 `ADD DEVICE CONNECTION STRING HERE` 替换为连接字符串。 保存并关闭该文件。 运行此命令以重启 VM 上的 IoT Edge 服务。

```bash
sudo systemctl restart iotedge
```

通过 [Azure 门户](../../iot-edge/how-to-deploy-modules-portal.md)或 [Azure CLI](../cognitive-services-apis-create-account-cli.md?tabs=windows) 将空间分析容器部署为 VM 上的 IoT 模块。 如果使用门户，请将映像 URI 设置为 Azure 容器注册表的位置。 

如果使用 Azure CLI，请执行以下步骤来部署容器。

---

### <a name="iot-deployment-manifest"></a>IoT 部署清单

若要简化多台主计算机上的容器部署，可以创建一个部署清单文件来指定容器创建选项和环境变量。 可在 GitHub 上找到 [Azure Stack Edge](https://go.microsoft.com/fwlink/?linkid=2142179)、[其他台式机](https://go.microsoft.com/fwlink/?linkid=2152270)和[带 GPU 的 Azure VM](https://go.microsoft.com/fwlink/?linkid=2152189) 的部署清单示例。

下表显示了 IoT Edge 模块使用的各个环境变量。 也可以在 `spatialanalysis` 中使用 `env` 特性，在上面链接的部署清单中设置这些变量：

| 设置名称 | “值” | 说明|
|---------|---------|---------|
| ARCHON_LOG_LEVEL | Info；Verbose | 日志记录级别，选择两个值中的一个|
| ARCHON_SHARED_BUFFER_LIMIT | 377487360 | 请勿修改|
| ARCHON_PERF_MARKER| false| 要进行性能日志记录，请将此变量设置为 true，否则应设置为 false| 
| ARCHON_NODES_LOG_LEVEL | Info；Verbose | 日志记录级别，选择两个值中的一个|
| OMP_WAIT_POLICY | PASSIVE | 请勿修改|
| QT_X11_NO_MITSHM | 1 | 请勿修改|
| APIKEY | 你的 API 密钥| 在 Azure 门户中从你的计算机视觉资源收集此值。 可以在资源的“密钥和终结点”部分找到它。 |
| 账单 | 你的终结点 URI| 在 Azure 门户中从你的计算机视觉资源收集此值。 可以在资源的“密钥和终结点”部分找到它。|
| EULA | accept | 需将此值设置为 *accept* 才能运行容器 |
| DISPLAY | :1 | 此值需与主计算机上 `echo $DISPLAY` 的输出相同。 Azure Stack Edge 设备没有显示器。 此项设置不适用|
| ARCHON_GRAPH_READY_TIMEOUT | 600 | 如果你的 GPU **不是** T4 或 NVIDIA 2080 Ti，请添加此环境变量|
| ORT_TENSORRT_ENGINE_CACHE_ENABLE | 0 | 如果你的 GPU **不是** T4 或 NVIDIA 2080 Ti，请添加此环境变量|
| KEY_ENV | ASE 加密密钥 | 如果 Video_URL 是经过模糊处理的字符串，请添加此环境变量 |
| IV_ENV | 初始化向量 | 如果 Video_URL 是经过模糊处理的字符串，请添加此环境变量|

> [!IMPORTANT]
> 必须指定 `Eula`、`Billing` 和 `ApiKey` 选项运行容器；否则，该容器不会启动。  有关详细信息，请参阅[计费](#billing)。

使用自己的设置和所选操作更新 [Azure Stack Edge 设备](https://go.microsoft.com/fwlink/?linkid=2142179)、[台式机](https://go.microsoft.com/fwlink/?linkid=2152270)或[带有 GPU 的 Azure VM](https://go.microsoft.com/fwlink/?linkid=2152189) 的部署清单后，可使用以下 [Azure CLI](../cognitive-services-apis-create-account-cli.md?tabs=windows) 命令在主计算机上将容器部署为 IoT Edge 模块。

```azurecli
sudo az login
sudo az extension add --name azure-iot
sudo az iot edge set-modules --hub-name "<iothub-name>" --device-id "<device-name>" --content DeploymentManifest.json --subscription "<name or ID of Azure Subscription>"
```

|参数  |说明  |
|---------|---------|
| `--hub-name` | 你的 Azure IoT 中心名称。 |
| `--content` | 部署文件的名称。 |
| `--target-condition` | 主计算机的 IoT Edge 设备名称。 |
| `-–subscription` | 订阅 ID 或名称。 |

此命令将启动部署。 在 Azure 门户中导航到 Azure IoT 中心实例的页面，以查看部署状态。 在设备完成容器映像下载并开始运行映像之前，状态可能显示为“417 - 设备的部署配置未设置”。

## <a name="validate-that-the-deployment-is-successful"></a>验证部署是否成功

有几种方法可用于验证容器是否正在运行。 在 Azure 门户上 Azure IoT 中心实例的空间分析模块的“IoT Edge 模块设置”中，找到“运行时状态”。 验证“运行时状态”的“所需值”和“报告的值”是否为“正在运行”。  

![示例部署验证](./media/spatial-analysis/deployment-verification.png)

部署完成并且容器运行后，**主计算机** 会开始将事件发送到 Azure IoT 中心。 如果使用了操作的 `.debug` 版本，对于在部署清单中配置的每个相机，你将看到一个可视化工具窗口。 现在，可以定义要在部署清单中监视的视线和区域，然后按照说明重新部署。 

## <a name="configure-the-operations-performed-by-spatial-analysis"></a>配置由空间分析执行的操作

需使用[空间分析操作](spatial-analysis-operations.md)将容器配置为使用连接的相机、配置操作，等等。 对于配置的每个相机设备，空间分析的操作会生成发送到 Azure IoT 中心实例的 JSON 消息输出流。

## <a name="use-the-output-generated-by-the-container"></a>使用容器生成的输出

若要开始使用容器生成的输出，请参阅以下文章：

*    使用适用于所选编程语言的 Azure 事件中心 SDK 连接到 Azure IoT 中心终结点，并接收事件。 有关详细信息，请参阅[从内置终结点读取设备到云的消息](../../iot-hub/iot-hub-devguide-messages-read-builtin.md)。 
*    在 Azure IoT 中心设置消息路由，以将事件发送到其他终结点，或者将事件保存到 Azure Blob 存储，等等。有关详细信息，请参阅 [IoT 中心消息路由](../../iot-hub/iot-hub-devguide-messages-d2c.md)。 

## <a name="running-spatial-analysis-with-a-recorded-video-file"></a>对录制的视频文件运行空间分析

可对录制的视频或直播视频使用空间分析。 若要对录制的视频使用空间分析，请尝试录制一个视频文件，并将其另存为 mp4 文件。 在 Azure 中创建一个 Blob 存储帐户，或使用现有帐户。 然后在 Azure 门户中更新以下 Blob 存储设置：
    1. 将“需要安全传输”更改为“已禁用” 
    2. 将“允许 Blob 公共访问”更改为“已启用” 

导航到“容器”部分，并创建一个新容器或使用现有容器。 然后将视频文件上传到该容器。 展开已上传文件的文件设置，并选择“生成 SAS”。 请务必将“过期日期”设置得足够长，使之涵盖测试期限。 将“允许的协议”设置为“HTTP”（不支持“HTTPS”）。 

单击“生成 SAS 令牌和 URL”并复制 Blob SAS URL。 将开头的 `https` 替换为 `http`，在支持视频播放的浏览器中测试 URL。

在所有图中，请将 [Azure Stack Edge 设备](https://go.microsoft.com/fwlink/?linkid=2142179)、[台式机](https://go.microsoft.com/fwlink/?linkid=2152270)或[带 GPU 的 Azure VM](https://go.microsoft.com/fwlink/?linkid=2152189) 的部署清单中的 `VIDEO_URL` 替换为创建的 URL。 将 `VIDEO_IS_LIVE` 设置为 `false`，然后使用更新的清单重新部署空间分析容器。 请参阅以下示例。

空间分析模块将开始使用视频文件，而且会持续自动重播。


```json
"zonecrossing": {
    "operationId" : "cognitiveservices.vision.spatialanalysis-personcrossingpolygon",
    "version": 1,
    "enabled": true,
    "parameters": {
        "VIDEO_URL": "Replace http url here",
        "VIDEO_SOURCE_ID": "personcountgraph",
        "VIDEO_IS_LIVE": false,
      "VIDEO_DECODE_GPU_INDEX": 0,
        "DETECTOR_NODE_CONFIG": "{ \"gpu_index\": 0, \"do_calibration\": true }",
        "SPACEANALYTICS_CONFIG": "{\"zones\":[{\"name\":\"queue\",\"polygon\":[[0.3,0.3],[0.3,0.9],[0.6,0.9],[0.6,0.3],[0.3,0.3]], \"events\": [{\"type\": \"zonecrossing\", \"config\": {\"threshold\": 16.0, \"focus\": \"footprint\"}}]}]}"
    }
   },

```

## <a name="troubleshooting"></a>故障排除

如果在启动或运行容器时遇到问题，请参阅[遥测和故障排除](spatial-analysis-logging.md)了解常见问题的解决步骤。 此文还包含了有关生成和收集日志以及收集系统运行状况的信息。

## <a name="billing"></a>计费

空间分析容器使用 Azure 帐户中的计算机视觉资源向 Azure 发送账单信息。 空间分析公共预览版目前供免费使用。 

Azure 认知服务容器在未连接到计量/计费终结点的情况下无权运行。 必须始终让容器可以向计费终结点传送计费信息。 认知服务容器不会将客户数据（例如，正在分析的视频或图像）发送给 Microsoft。


## <a name="summary"></a>总结

在本文中，你已学习相关的概念，以及下载、安装和运行空间分析容器的工作流。 综上所述：

* 空间分析是适用于 Docker 的 Linux 容器。
* 从 Microsoft 容器注册表下载容器映像。
* 容器映像作为 Azure IoT Edge 中的 IoT 模块运行。
* 如何配置容器并将其部署在主计算机上。

## <a name="next-steps"></a>后续步骤

* [部署人员计数 Web 应用程序](spatial-analysis-web-app.md)
* [配置空间分析操作](spatial-analysis-operations.md)
* [日志记录和故障排除](spatial-analysis-logging.md)
* [相机放置指南](spatial-analysis-camera-placement.md)
* [区域和线条放置指南](spatial-analysis-zone-line-placement.md)
