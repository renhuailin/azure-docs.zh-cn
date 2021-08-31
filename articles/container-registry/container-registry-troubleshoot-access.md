---
title: 排查与注册表相关的网络问题
description: 访问位于虚拟网络中或防火墙后面的 Azure 容器注册表时的常见问题的症状、原因和解决方法
ms.topic: article
ms.date: 05/10/2021
ms.openlocfilehash: 7ea4eb698f855a98df22e2e0426a0004c890290c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722440"
---
# <a name="troubleshoot-network-issues-with-registry"></a>排查与注册表相关的网络问题

本文将帮助你排查在访问位于虚拟网络中或防火墙或代理服务器后面的 Azure 容器注册表时可能遇到的问题。 

## <a name="symptoms"></a>症状

可能包括以下一项或多项：

* 无法推送或拉取映像，出现错误 `dial tcp: lookup myregistry.azurecr.io`
* 无法推送或拉取映像，出现错误 `Client.Timeout exceeded while awaiting headers`
* 无法推送或拉取映像，出现 Azure CLI 错误 `Could not connect to the registry login server`
* 无法将映像从注册表拉取到 Azure Kubernetes 服务或其他 Azure 服务
* 无法访问 HTTPS 代理后面的注册表，出现错误 `Error response from daemon: login attempt failed with status: 403 Forbidden` 或 `Error response from daemon: Get <registry>: proxyconnect tcp: EOF Login failed`
* 无法配置虚拟网络设置，出现错误“`Failed to save firewall and virtual network settings for container registry`”
* 无法在 Azure 门户中访问或查看注册表设置，或者无法使用 Azure CLI 管理注册表
* 无法添加或修改虚拟网络设置或公共访问规则
* Azure 容器注册表任务无法推送或拉取映像
* Azure 安全中心无法扫描注册表中的映像，或者扫描结果未显示在 Azure 安全中心内
* 在尝试访问使用专用终结点配置的注册表时，你收到错误 `host is not reachable`。

## <a name="causes"></a>原因

* 客户端防火墙或代理阻止访问 - [解决方案](#configure-client-firewall-access)
* 注册表上的公用网络访问规则阻止访问 - [解决方案](#configure-public-access-to-registry)
* 虚拟网络或专用终结点配置阻止访问 - [解决方案](#configure-vnet-access)
* 你尝试将 Azure 安全中心或某些其他的 Azure 服务与具有专用终结点、服务终结点或公共 IP 访问规则的注册表集成 - [解决方案](#configure-service-access)

## <a name="further-diagnosis"></a>进一步诊断 

运行 [az acr check-health](/cli/azure/acr#az_acr_check_health) 命令可详细了解注册表环境的运行状况，以及对目标注册表的访问（可选）。 例如，诊断某些网络连接或配置问题。 

参阅[检查 Azure 容器注册表的运行状况](container-registry-check-health.md)以查看命令示例。 如果报告了错误，请查看[错误参考](container-registry-health-error-reference.md)和以下部分，以了解建议的解决方案。

如果在将 Azure Kubernetes 服务与集成注册表配合使用时遇到问题，请运行 [az aks check-acr](/cli/azure/aks#az_aks_check_acr) 命令，以验证 AKS 群集是否可以访问该注册表。

> [!NOTE]
> 当注册表身份验证或授权存在问题时，也可能出现一些网络连接症状。 请参阅[注册表登录故障排除](container-registry-troubleshoot-login.md)。

## <a name="potential-solutions"></a>可能的解决方案

### <a name="configure-client-firewall-access"></a>配置客户端防火墙访问权限

若要从客户端防火墙或代理服务器后面访问注册表，请将防火墙规则配置为可访问注册表的公共 REST 和数据终结点。 如果启用了[专用数据终结点](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints)，则需要用于访问以下终结点的规则：

* REST 终结点：`<registryname>.azurecr.io`
* 数据终结点：`<registry-name>.<region>.data.azurecr.io`

对于异地复制的注册表，请为每个区域副本配置对数据终结点的访问权限。

在 HTTPS 代理后面，确保 Docker 客户端和 Docker 守护程序均已针对代理行为进行了配置。 如果更改了 Docker 守护程序的代理设置，必需重启该守护程序。 

ContainerRegistryLoginEvents 表中的注册表资源日志可能有助于诊断尝试的连接被阻止的问题。

相关链接：

* [配置规则以访问防火墙后的 Azure 容器注册表](container-registry-firewall-access-rules.md)
* [HTTP/HTTPS 代理配置](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
* [Azure 容器注册表中的异地复制](container-registry-geo-replication.md)
* [监视 Azure 容器注册表](monitor-service.md)

### <a name="configure-public-access-to-registry"></a>配置对注册表的公共访问权限

如果通过 Internet 访问注册表，请确认注册表允许从客户端通过公用网络进行的访问。 默认情况下，Azure 容器注册表允许从所有网络访问公共注册表终结点。 注册表可以将访问限制为来自所选网络或所选 IP 地址。 

如果为具有服务终结点的虚拟网络配置了注册表，禁用公用网络访问还会禁止通过服务终结点进行访问。 如果为具有专用链接的虚拟网络配置了注册表，则 IP 网络规则不适用于注册表的专用终结点。 

相关链接：

* [配置公共 IP 网络规则](container-registry-access-selected-networks.md)
* [使用 Azure 专用链接以私密方式连接到 Azure 容器注册表](container-registry-private-link.md)
* [使用 Azure 虚拟网络中的服务终结点限制对容器注册表的访问](container-registry-vnet.md)


### <a name="configure-vnet-access"></a>配置 VNet 访问权限

确认虚拟网络为专用链接配置了专用终结点，或者配置了服务终结点（预览版）。 当前不支持 Azure Bastion 终结点。

如果配置了专用终结点，请确认 DNS 已将注册表的公共 FQDN（例如，myregistry.azurecr.io）解析为注册表的专用 IP 地址。

  * 使用 `--vnet` 参数运行 [az acr check-health](/cli/azure/acr#az_acr_check_health) 命令，以确认虚拟网络中到专用终结点的 DNS 路由。
  * 使用网络实用工具（如 `dig` 或 `nslookup`）进行 DNS 查找。 
  * 确保为注册表 FQDN 和每个数据终结点 FQDN [配置 DNS 记录](container-registry-private-link.md#dns-configuration-options)。 

查看用于限制从网络中的其他资源发往注册表的流量的 NSG 规则和服务标记。 

如果配置了注册表的服务终结点，请确认用于允许从该网络子网进行访问的网络规则已添加到注册表。 服务终结点仅支持从网络中的虚拟机和 AKS 群集进行访问。

若要使用其他 Azure 订阅中的虚拟网络限制注册表访问，请确保在该订阅中注册 `Microsoft.ContainerRegistry` 资源提供程序。 使用 Azure 门户、Azure CLI 或其他 Azure 工具为 Azure 容器注册表[注册资源提供程序](../azure-resource-manager/management/resource-providers-and-types.md)。

如果在网络中配置了 Azure 防火墙或类似的解决方案，请检查是否已允许来自其他资源（如 AKS 群集）的出口流量到达注册表终结点。

相关链接：

* [使用 Azure 专用链接以私密方式连接到 Azure 容器注册表](container-registry-private-link.md)
* [排查 Azure 专用终结点连接问题](../private-link/troubleshoot-private-endpoint-connectivity.md)
* [使用 Azure 虚拟网络中的服务终结点限制对容器注册表的访问](container-registry-vnet.md)
* [AKS 群集所需的出站网络规则和 FQDN](../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
* [Kubernetes：调试 DNS 解析](https://kubernetes.io/docs/tasks/administer-cluster/dns-debugging-resolution/)
* [虚拟网络服务标记](../virtual-network/service-tags-overview.md)

### <a name="configure-service-access"></a>配置服务访问

目前，多个 Azure 服务不允许访问具有网络限制的容器注册表：

* Azure 安全中心无法在限制对专用终结点、所选子网或 IP 地址进行访问的注册表中执行[映像漏洞扫描](../security-center/defender-for-container-registries-introduction.md?bc=%2fazure%2fcontainer-registry%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fcontainer-registry%2ftoc.json)。 
* 某些 Azure 服务（包括 Azure 应用服务和 Azure 容器实例）的资源无法访问具有网络限制的容器注册表。

如果需要使用容器注册表访问或集成这些 Azure 服务，请去除网络限制。 例如，删除注册表的专用终结点，或者删除或修改注册表的公共访问规则。

从 2021 年 1 月开始，可以将受网络限制的注册表配置为[允许从所选的受信任服务访问](allow-access-trusted-services.md)。

相关链接：

* [通过安全中心扫描 Azure 容器注册表映像](../security-center/defender-for-container-registries-introduction.md)
* 提供[反馈](https://feedback.azure.com/forums/347535-azure-security-center/suggestions/41091577-enable-vulnerability-scanning-for-images-that-are)
* [允许受信任的服务安全访问受网络限制的容器注册表](allow-access-trusted-services.md)


## <a name="advanced-troubleshooting"></a>高级故障排除

如果在注册表中启用了[收集资源日志](monitor-service.md)，请查看 ContainterRegistryLoginEvents 日志。 此日志存储身份验证事件和状态，包括传入标识和 IP 地址。 查询此日志可获得有关[注册表身份验证失败](monitor-service.md#registry-authentication-failures)的信息。 

相关链接：

* [用于诊断评估和审核的日志](./monitor-service.md)
* [容器注册表常见问题解答](container-registry-faq.yml)
* [Azure 容器注册表的 Azure 安全基线](security-baseline.md)
* [Azure 容器注册表的最佳做法](container-registry-best-practices.md)

## <a name="next-steps"></a>后续步骤

如果此处无法解决你的问题，请参阅以下选项。

* 其他注册表故障排除主题包括：
  * [注册表登录故障排除](container-registry-troubleshoot-login.md) 
  * [注册表性能故障排除](container-registry-troubleshoot-performance.md)
* [社区支持](https://azure.microsoft.com/support/community/)选项
* [Microsoft 问答](/answers/products/)
* [开具支持票证](https://azure.microsoft.com/support/create-ticket/)