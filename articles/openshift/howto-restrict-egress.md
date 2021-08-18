---
title: 限制 Azure Red Hat OpenShift (ARO) 群集中的出口流量
description: 了解控制 Azure Red Hat OpenShift (ARO) 中的出口流量所需的端口和地址
author: sakthi-vetrivel
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 04/09/2021
ms.openlocfilehash: 53efeff1024d104f4dae2d70fc6f00c73d3d8fed
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114291824"
---
# <a name="control-egress-traffic-for-your-azure-red-hat-openshift-aro-cluster-preview"></a>控制 Azure Red Hat OpenShift (ARO) 群集（预览版）的出口流量

本文提供了从 Azure Red Hat OpenShift 群集 (ARO) 保护出站流量的必要详细信息。 其中包含基本 ARO 部署的群集要求，以及有关可选 Red Hat 和第三方组件的更多要求。 结尾处有一个[示例](#private-aro-cluster-setup)，介绍如何使用 Azure 防火墙来配置这些要求。 请记住，你可以将此信息应用到 Azure 防火墙或任何出站限制方法/设备。

## <a name="before-you-begin"></a>在开始之前

本文假定你要创建新的 AKS 群集。 如果需要基本 ARO 群集，请参阅 [ARO 快速入门](./tutorial-create-cluster.md)。

> [!IMPORTANT]
> ARO 预览版功能是可选择启用的自助功能。 预览版是“按原样”和“按可用”提供的，并且未包含在服务级别协议和有限保修中。 客户支持部门会尽力为 ARO 预览版功能提供部分支持。

## <a name="minimum-required-fqdn--application-rules"></a>必需的 FQDN/应用程序规则

此列表基于在此处的 OpenShift 文档中找到的 FQDN 列表： https://docs.openshift.com/container-platform/4.6/installing/install_config/configuring-firewall.html

必须具有以下 FQDN/应用程序规则：

| 目标 FQDN | 端口 | 用途 |
| ----------- | ----------- | ------------- |
| **`*.quay.io`** | **HTTPS:443** | 安装所必需，由群集使用。 群集使用此域名下载平台容器映像。 |
| **`registry.redhat.io`** | **HTTPS:443** | 核心外接程序所必需。 群集使用此域名下载核心组件，例如开发工具、基于操作员的外接程序和 Red Hat 提供的容器映像。
| **`mirror.openshift.com`** | **HTTPS:443** | 在 VDI 环境或便携式计算机上，需要使用此域名来访问镜像安装内容和映像。 为了了解要从 quay.io 拉取哪些映像，在群集中下载平台版本签名时需要此域名。 |
| **`api.openshift.com`** | **HTTPS:443** | 在下载映像签名之前，群集需要使用此域名才能检查是否有可用的更新。 |
| **`arosvc.azurecr.io`** | **HTTPS:443** | ARO 运算符的内部专用注册表。  子网上不允许服务终结点 Microsoft.containerregistry 时需要此域名。 |
| **`management.azure.com`** | **HTTPS:443** | 群集使用此域名来访问 Azure API。 |
| **`login.microsoftonline.com`** | **HTTPS:443** | 群集使用此域名来对 Azure 进行身份验证。 |
| **`gcs.prod.monitoring.core.windows.net`** | **HTTPS:443** | 此域名用于 Microsoft Geneva 监视，以便 ARO 团队可以监视客户的群集。 |
| **`*.blob.core.windows.net`** | **HTTPS:443** | 此域名用于 Microsoft Geneva 监视，以便 ARO 团队可以监视客户的群集。 |
| **`*.servicebus.windows.net`** | **HTTPS:443** | 此域名用于 Microsoft Geneva 监视，以便 ARO 团队可以监视客户的群集。 |
| **`*.table.core.windows.net`** | **HTTPS:443** | 此域名用于 Microsoft Geneva 监视，以便 ARO 团队可以监视客户的群集。 |

> [!NOTE] 
> 对于公开 *.blob、*.table 和其他大型地址空间的很多客户，可能产生数据外泄问题。 你可能需要考虑使用 [OpenShift 出口防火墙](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/configuring-egress-firewall.html)来阻止群集中部署的应用程序访问这些目标，对于特定的应用程序需求，则使用 Azure 专用链接。

---

## <a name="complete-list-of-required-and-optional-fqdns"></a>必需和可选 FQDN 的完整列表

### <a name="first-group-installing-and-downloading-packages-and-tools"></a>第一组：下载并安装程序包和工具

- **`quay.io`** ：安装所必须，由群集使用。 群集使用此域名下载平台容器映像。
- **`registry.redhat.io`** ：核心外接程序所必须。 群集使用此域名来下载核心组件，例如开发工具、基于操作员的外接程序或 Red Hat 提供的容器映像，如中间件、通用基础映像...
- **`sso.redhat.com`** ：在 VDI 环境中或便携式计算机上，需要使用此域名才能连接到 cloud.redhat.com。 这是可下载拉取机密，并使用我们在 Red Hat 中提供的一些 SaaS 解决方案，以便监视订阅、群集清单、退款报告等内容的站点。
- **`openshift.org`** ：在 VDI 环境或便携式计算机上，需要使用此域名才能连接，从而下载 RH CoreOS 映像，但是，在 Azure 中，这些映像是从市场获取的，因此，无需下载操作系统映像。

---

### <a name="second-group-telemetry"></a>第二组：遥测

你可以忽略本部分的全部内容，但是，在了解如何操作之前，不妨先看一看具体内容： https://docs.openshift.com/container-platform/4.6/support/remote_health_monitoring/about-remote-health-monitoring.html
- **`cert-api.access.redhat.com`** ：在 VDI 或便携式计算机环境中使用。
- **`api.access.redhat.com`** ：在 VDI 或便携式计算机环境中使用。
- **`infogw.api.openshift.com`** ：在 VDI 或便携式计算机环境中使用。
- **`https://cloud.redhat.com/api/ingress`** ：在群集中使用，供见解操作员与 aaS Red Hat Insights 进行集成。
在 OpenShift 容器平台中，客户可以选择不报告运行状况和使用情况信息。 但是，连接的群集可让 Red Hat 更快地响应问题，并向客户提供更理想的支持，同时也有助于更好地了解产品如何升级群集。 有关详细信息，请访问： https://docs.openshift.com/container-platform/4.6/support/remote_health_monitoring/opting-out-of-remote-health-reporting.html 。

---

### <a name="third-group-cloud-apis"></a>第三组：云 API

- **`management.azure.com`** ：群集使用此域名来访问 Azure API。

---

### <a name="fourth-group-other-openshift-requirements"></a>第四组：其他 OPENSHIFT 要求

- **`mirror.openshift.com`** ：在 VDI 环境或便携式计算机中，需要使用此域名来访问镜像安装内容和映像，并且在群集中也需要使用此域名来下载平台版本签名（群集使用此签名来了解要从 quay.io 中提取哪些映像）。
- **`storage.googleapis.com/openshift-release`** ：下载平台版本签名的替代站点，群集使用此签名来了解要从 quay.io 拉取哪些映像。
- **`*.apps.<cluster_name>.<base_domain>`** （或等效 ARO URL）：将域加入允许列表时，企业网络中使用此域名来连接 OpenShift 中部署的应用程序，或者用于访问 OpenShift 控制台。
- **`api.openshift.com`** ：在下载映像签名之前，群集需要使用此域名才能检查是否有可用的更新。
- **`registry.access.redhat.com`** ：使用 ODO CLI 工具时，需要在 VDI 或便携式计算机环境中访问注册表才能下载开发映像。 （对于不熟悉 Kubernetes 的开发人员，这是一种替代 CLI 工具。） https://docs.openshift.com/container-platform/4.6/cli_reference/developer_cli_odo/understanding-odo.html

---

### <a name="fifth-group-microsoft--red-hat-aro-monitoring-service"></a>第一组：MICROSOFT 和 RED HAT ARO 监视服务

- **`login.microsoftonline.com`** ：群集使用此域名来对 Azure 进行身份验证。
- **`gcs.prod.monitoring.core.windows.net`** ：此域名用于 Microsoft Geneva 监视，以便 ARO 团队可以监视客户的群集。
- **`*.blob.core.windows.net`** ：此域名用于 Microsoft Geneva 监视，以便 ARO 团队可以监视客户的群集。
- **`*.servicebus.windows.net`** ：此域名用于 Microsoft Geneva 监视，以便 ARO 团队可以监视客户的群集。
- **`*.table.core.windows.net`** ：此域名用于 Microsoft Geneva 监视，以便 ARO 团队可以监视客户的群集。

## <a name="aro-integrations"></a>ARO 集成

### <a name="azure-monitor-for-containers"></a>用于容器的 Azure Monitor

有两个选项可以提供对用于容器的 Azure Monitor 的访问，你可以允许 Azure Monitor [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags)或者提供对必需 FQDN/应用程序规则的访问权限。  下面是有关如何将 Azure Monitor 添加到现有 ARO 群集的[说明](../azure-monitor/containers/container-insights-azure-redhat4-setup.md)。

#### <a name="required-network-rules"></a>必需的网络规则

必须具有以下 FQDN/应用程序规则：

| 目标终结点                                                             | 协议 | 端口    | 用途  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - `AzureMonitor:443`  | TCP           | 443      | 此终结点用于将指标数据和日志发送到 Azure Monitor 和 Log Analytics。 |

#### <a name="required-fqdn--application-rules"></a>必需的 FQDN/应用程序规则

启用适用于容器的 Azure Monitor 的 ARO 群集需要以下 FQDN/应用程序规则：

| FQDN                                    | 端口      | 用途      |
|-----------------------------------------|-----------|----------|
| **`dc.services.visualstudio.com`** | **`HTTPS:443`**    | 此终结点适用于使用 Azure Monitor 的指标和监视遥测。 |
| **`*.ods.opinsights.azure.com`**    | **`HTTPS:443`**    | Azure Monitor 使用此终结点来引入日志分析数据。 |
| **`*.oms.opinsights.azure.com`** | **`HTTPS:443`** | 此终结点由 omsagent 使用，用于对日志分析服务进行身份验证。 |
| **`*.monitoring.azure.com`** | **`HTTPS:443`** | 此终结点用于将指标数据发送到 Azure Monitor。 |


## <a name="private-aro-cluster-setup"></a>专用 ARO 群集设置
目标是通过 Azure 防火墙路由出口流量，从而保护 ARO 群集
### <a name="before"></a>早于:
![以前](media/concepts-networking/aro-private.jpg)
### <a name="after"></a>晚于：
![之后](media/concepts-networking/aro-fw.jpg)

## <a name="create-a-private-aro-cluster"></a>创建专用 ARO 群集

### <a name="set-up-vars-for-your-environment"></a>为环境设置 VARS
```bash

CLUSTER=aro-cluster # Name of your created cluster
RESOURCEGROUP=aro-rg # The name of your resource group where you created the ARO cluster
AROVNET=aro-vnet # The name of your vnet from your created ARO cluster
JUMPSUBNET=jump-subnet
LOCATION=eastus # The location where ARO cluster is deployed

```

### <a name="create-a-resource-group"></a>创建资源组
```bash
az group create -g "$RESOURCEGROUP" -l $LOCATION
```

### <a name="create-the-virtual-network"></a>创建虚拟网络
```bash
az network vnet create \
  -g $RESOURCEGROUP \
  -n $AROVNET \
  --address-prefixes 10.0.0.0/8
```

### <a name="add-two-empty-subnets-to-your-virtual-network"></a>将虚拟网络添加两个空子网
```bash
  az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n "$CLUSTER-master" \
    --address-prefixes 10.10.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry

  az network vnet subnet create \
    -g $RESOURCEGROUP \
    --vnet-name $AROVNET \
    -n "$CLUSTER-worker" \
    --address-prefixes 10.20.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry
```

### <a name="disable-network-policies-for-private-link-service-on-your-virtual-network-and-subnets-this-is-a-requirement-for-the-aro-service-to-access-and-manage-the-cluster"></a>在虚拟网络和子网上禁用专用链接服务的网络策略。 这是 ARO 服务访问和管理群集的必要条件。
```bash
az network vnet subnet update \
  -g "$RESOURCEGROUP" \
  --vnet-name $AROVNET \
  -n "$CLUSTER-master" \
  --disable-private-link-service-network-policies true
```
### <a name="create-a-firewall-subnet"></a>创建防火墙子网
```bash
az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n "AzureFirewallSubnet" \
    --address-prefixes 10.100.1.0/26
```

## <a name="create-a-jump-host-vm"></a>创建跳转主机 VM
### <a name="create-a-jump-subnet"></a>创建跳转子网
```bash
  az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n $JUMPSUBNET \
    --address-prefixes 10.30.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry
```
### <a name="create-a-jump-host-vm"></a>创建跳转主机 VM
```bash
VMUSERNAME=aroadmin

az vm create --name ubuntu-jump \
             --resource-group $RESOURCEGROUP \
             --ssh-key-values ~/.ssh/id_rsa.pub \
             --admin-username $VMUSERNAME \
             --image UbuntuLTS \
             --subnet $JUMPSUBNET \
             --public-ip-address jumphost-ip \
             --vnet-name $AROVNET 
```

## <a name="create-an-azure-red-hat-openshift-cluster"></a>创建 Azure Red Hat OpenShift 群集
### <a name="get-a-red-hat-pull-secret-optional"></a>获取 Red Hat 拉取机密（可选）

Red Hat 拉取机密使群集能够访问 Red Hat 容器注册表以及其他内容。 此步骤是可选的，但建议执行。

1. [转到 Red Hat OpenShift 群集管理器门户](https://cloud.redhat.com/openshift/install/azure/aro-provisioned)并登录。

   你将需要使用你的企业电子邮件登录 Red Hat 帐户或创建一个新的 Red Hat 帐户，并接受条款和条件。

2. 单击“下载拉取机密”。

将保存的 `pull-secret.txt` 文件保存在一个安全的位置 - 每次创建群集时都要使用该文件。

运行 `az aro create` 命令时，可以使用 `--pull-secret @pull-secret.txt` 参数引用拉取机密。 从存储 `pull-secret.txt` 文件的目录执行 `az aro create`。 否则，将 `@pull-secret.txt` 替换为 `@<path-to-my-pull-secret-file`。

如果要在其他脚本中复制拉取机密或引用它，则应该将拉取机密格式设置为有效的 JSON 字符串。

```bash
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet $AROVNET \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker" \
  --apiserver-visibility Private \
  --ingress-visibility Private \
  --pull-secret @pull-secret.txt
```

## <a name="create-an-azure-firewall"></a>创建 Azure 防火墙

### <a name="create-a-public-ip-address"></a>创建公共 IP 地址
```bash
az network public-ip create -g $RESOURCEGROUP -n fw-ip --sku "Standard" --location $LOCATION
```
### <a name="update-install-azure-firewall-extension"></a>更新安装 Azure 防火墙扩展
```bash
az extension add -n azure-firewall
az extension update -n azure-firewall
```

### <a name="create-azure-firewall-and-configure-ip-config"></a>创建 Azure 防火墙并配置 IP 配置
```bash
az network firewall create -g $RESOURCEGROUP -n aro-private -l $LOCATION
az network firewall ip-config create -g $RESOURCEGROUP -f aro-private -n fw-config --public-ip-address fw-ip --vnet-name $AROVNET

```

### <a name="capture-azure-firewall-ips-for-a-later-use"></a>捕获 Azure 防火墙 IP 以供以后使用
```bash
FWPUBLIC_IP=$(az network public-ip show -g $RESOURCEGROUP -n fw-ip --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RESOURCEGROUP -n aro-private --query "ipConfigurations[0].privateIpAddress" -o tsv)

echo $FWPUBLIC_IP
echo $FWPRIVATE_IP
```

### <a name="create-a-udr-and-routing-table-for-azure-firewall"></a>为 Azure 防火墙创建 UDR 和路由表
```bash
az network route-table create -g $RESOURCEGROUP --name aro-udr

az network route-table route create -g $RESOURCEGROUP --name aro-udr --route-table-name aro-udr --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP
```

### <a name="add-application-rules-for-azure-firewall"></a>为 Azure 防火墙添加应用程序规则
基于此[列表](https://docs.openshift.com/container-platform/4.3/installing/install_config/configuring-firewall.html#configuring-firewall_configuring-firewall)运行 OpenShift 的规则：
```bash
az network firewall application-rule create -g $RESOURCEGROUP -f aro-private \
 --collection-name 'ARO' \
 --action allow \
 --priority 100 \
 -n 'required' \
 --source-addresses '*' \
 --protocols 'http=80' 'https=443' \
 --target-fqdns 'registry.redhat.io' '*.quay.io' 'sso.redhat.com' 'management.azure.com' 'mirror.openshift.com' 'api.openshift.com' 'quay.io' '*.blob.core.windows.net' 'gcs.prod.monitoring.core.windows.net' 'registry.access.redhat.com' 'login.microsoftonline.com' '*.servicebus.windows.net' '*.table.core.windows.net' 'grafana.com'
```
Docker 映像的可选规则：
```bash
az network firewall application-rule create -g $RESOURCEGROUP -f aro-private \
 --collection-name 'Docker' \
 --action allow \
 --priority 200 \
 -n 'docker' \
 --source-addresses '*' \
 --protocols 'http=80' 'https=443' \
 --target-fqdns '*cloudflare.docker.com' '*registry-1.docker.io' 'apt.dockerproject.org' 'auth.docker.io'
```

### <a name="associate-aro-subnets-to-fw"></a>将 ARO 子网关联到 FW
```bash
az network vnet subnet update -g $RESOURCEGROUP --vnet-name $AROVNET --name "$CLUSTER-master" --route-table aro-udr
az network vnet subnet update -g $RESOURCEGROUP --vnet-name $AROVNET --name "$CLUSTER-worker" --route-table aro-udr
```

## <a name="test-the-configuration-from-the-jumpbox"></a>测试 Jumpbox 的配置
仅在已为 Docker 映像添加规则时，这些步骤才适用。 
### <a name="configure-the-jumpbox"></a>配置 Jumpbox
登录到 Jumpbox VM，并安装 `azure-cli`、`oc-cli` 和 `jq` Utils。 要安装 openshift-cli，请查看 Red Hat 客户门户。
```bash
#Install Azure-cli
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
#Install jq
sudo apt install jq -y
```
### <a name="log-into-the-aro-cluster"></a>登录到 ARO 群集
列出群集凭据：
```bash

# Login to Azure
az login
# Set Vars in Jumpbox
CLUSTER=aro-cluster # Name of your created cluster
RESOURCEGROUP=aro-rg # The name of your resource group where you created the ARO cluster

#Get the cluster credentials
ARO_PASSWORD=$(az aro list-credentials -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.kubeadminPassword')
ARO_USERNAME=$(az aro list-credentials -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.kubeadminUsername')
```
获取 API 服务器终结点：
```bash
ARO_URL=$(az aro show -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.apiserverProfile.url')
```

### <a name="download-the-oc-cli-to-the-jumpbox"></a>将 oc CLI 下载到 Jumpbox
```bash
cd ~
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz

mkdir openshift
tar -zxvf openshift-client-linux.tar.gz -C openshift
echo 'export PATH=$PATH:~/openshift' >> ~/.bashrc && source ~/.bashrc
```

使用 `oc login` 登录：
```bash
oc login $ARO_URL -u $ARO_USERNAME -p $ARO_PASSWORD
```

### <a name="run-centos-to-test-outside-connectivity"></a>运行 CentOS 以测试外部连接性
创建 Pod
```bash
cat <<EOF | oc apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: centos
spec:
  containers:
  - name: centos
    image: centos
    ports:
    - containerPort: 80
    command:
    - sleep
    - "3600"
EOF
```
Pod 运行后，执行到其中并测试外部连接性。

```bash
oc exec -it centos -- /bin/bash
curl microsoft.com
```

## <a name="access-the-web-console-of-the-private-cluster"></a>访问专用群集的 Web 控制台

### <a name="set-up-ssh-forwards-commands"></a>设置 SSH 转发命令

```bash
sudo ssh -i $SSH_PATH -L 443:$CONSOLE_URL:443 aroadmin@$JUMPHOST

example:
sudo ssh -i /Users/jimzim/.ssh/id_rsa -L 443:console-openshift-console.apps.d5xm5iut.eastus.aroapp.io:443 aroadmin@104.211.18.56
```

### <a name="modify-the-etc-hosts-file-on-your-local-machine"></a>在本地计算机上修改 etc. 主机文件
```bash
##
# Host Database
#
127.0.0.1 console-openshift-console.apps.d5xm5iut.eastus.aroapp.io
127.0.0.1 oauth-openshift.apps.d5xm5iut.eastus.aroapp.io
```

### <a name="use-sshuttle-as-another-option"></a>使用 sshuttle 作为另一个选项

[SSHuttle](https://github.com/sshuttle/sshuttle)


## <a name="clean-up-resources"></a>清理资源

```bash

# Clean up the ARO cluster, vnet, firewall and jumpbox

# Remove udr from master and worker subnets first or will get error when deleting ARO cluster
az network vnet subnet update --vnet-name $AROVNET -n aro-cluster-master -g $RESOURCEGROUP --route-table aro-udr --remove routeTable
az network vnet subnet update --vnet-name $AROVNET -n aro-cluster-worker -g $RESOURCEGROUP --route-table aro-udr --remove routeTable

# Remove ARO Cluster
az aro delete -n $CLUSTER -g $RESOURCEGROUP

# Remove the resource group that contains the firewall, jumpbox and vnet
az group delete -n $RESOURCEGROUP
```
