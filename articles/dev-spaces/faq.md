---
title: 有关 Azure Dev Spaces 的常见问题解答
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: 获取有关 Azure Dev Spaces 的某些常见问题的解答
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s '
ms.openlocfilehash: e83bed86714e4b92c63f4e7b7eb55df7a2a7eaff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96548828"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>有关 Azure Dev Spaces 的常见问题解答

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

这可以解答有关 Azure Dev Spaces 的常见问题。

## <a name="what-versions-of-kubernetes-are-supported-for-azure-dev-spaces"></a>Azure Dev Spaces 支持哪些版本的 Kubernetes？

Azure Dev Spaces 支持 [AKS 中当前支持的正式发布 (GA) 版本的 Kubernetes（最高支持 1.18）][aks-supported-k8s]。 AKS 上的 Kubernetes 1.19 及更高版本使用 ContainerD 作为容器运行时，该运行时不适用于 Azure Dev Spaces。

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>当前哪些 Azure 区域提供 Azure Dev Spaces？

有关可用区域的完整列表，请参阅[支持的区域][supported-regions]。

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>我是否可以将具有 Azure Dev Spaces 的 AKS 群集迁移到另一个区域？

可以，如果想要将具有 Azure Dev Spaces 的 AKS 群集移动到另一个[支持的区域][supported-regions]，则建议你在另一个区域中创建新群集，然后安装并配置 Azure Dev Spaces，并将资源和应用程序部署到新群集。 有关迁移 AKS 的详细信息，请参阅[迁移到 Azure Kubernetes 服务 (AKS)][aks-migration]。

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>我是否可以将 Azure Dev Spaces 与现有 Dockerfile 或 Helm 图表配合使用？

可以，如果你的项目已有 Dockerfile 或 Helm 图表，则可以将这些文件与 Azure Dev Spaces 配合使用。 运行 `azds prep` 时，请使用 `--chart` 参数并指定图表的位置。 Azure Dev Spaces 仍将生成 azds.yaml 和 Dockerfile.develop 文件，但不会替换或修改现有 Dockerfile 或 Helm 图表。 可能需要修改 azds.yaml 和 Dockerfile.develop 文件，才能在运行 `azds up` 时将所有内容与现有应用程序一起正常使用。

使用自己的 Dockerfile 或 Helm 图表时，存在以下限制：
* 如果只使用一个 Dockerfile，则必须包括启用开发方案所需的全部内容，例如语言 SDK，而不只是运行时。 如果对 Azure Dev Spaces 使用单独的 Dockerfile（如 Dockerfile.develop），则必须在该 Dockerfile 中包含启用开发方案所需的全部内容。
* Helm 图表必须支持将部分或整个图像标记作为 values.yaml 中的值进行传递。
* 如果要对入口进行任何修改，则还可以更新 Helm 图表以使用 Azure Dev Spaces 提供的入口解决方案。
* 如果要使用 [Azure Dev Spaces 提供的路由功能][dev-spaces-routing]，则单个项目的所有服务都必须适合单个 Kubernetes 命名空间，并且必须使用简单命名进行部署，例如 service-a。 在标准的 Helm 图表中，可以通过指定 fullnameOverride 属性的值来完成此命名更新。

## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>我是否可以修改 Azure Dev Spaces 生成的文件？

可以，你可以修改[准备项目时 Azure Dev Spaces 生成的][dev-spaces-prep] azds.yaml 文件、Dockerfile 和 Helm 图表。 修改这些文件将更改生成和运行项目的方式。

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>我是否可以在不使用公共 IP 地址的情况下使用 Azure Dev Spaces？

不可以，无法在没有公共 IP 地址的 AKS 群集上预配 Azure Dev Spaces。 公共 IP 是 [Azure Dev Spaces 进行路由所必需的][dev-spaces-routing]。

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>我是否可以将自己的入口与 Azure Dev Spaces 配合使用？

可以，你可以随 Azure Dev Spaces 创建的入口一起配置自己的入口。 例如，可以使用 [traefik][ingress-traefik] 或 [NGINX][ingress-nginx]。

## <a name="can-i-use-https-with-azure-dev-spaces"></a>我是否可以将 HTTPS 与 Azure Dev Spaces 配合使用？

可以，你可以使用 [traefik][ingress-https-traefik] 或 [NGINX][ingress-https-nginx] 配置带有 HTTPS 的自己的入口。

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>我是否可以在使用 CNI（而不是 kubenet）的群集上使用 Azure Dev Spaces？ 

可以，你可以在使用 CNI 进行联网的 AKS 群集上使用 Azure Dev Spaces。 例如，可以在具有[现有 Windows 容器][windows-containers]的 AKS 群集上使用 Azure Dev Spaces，该群集使用 CNI 进行联网。 有关将用于联网的 CNI 与 Azure Dev Spaces 配合使用的详细信息，请参阅[此处](configure-networking.md#using-azure-cni)。

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>我是否可以将 Azure Dev Spaces 与 Windows 容器配合使用？

目前，Azure Dev Spaces 只能在 Linux Pod 和节点上运行，但你可以在具有[现有 Windows 容器][windows-containers]的 AKS 群集上运行 Azure Dev Spaces。

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>我是否可以在启用了 API 服务器授权 IP 地址范围的 AKS 群集上使用 Azure Dev Spaces？

可以，你可以在启用了 [API 服务器授权 IP 地址范围][aks-auth-range]的 AKS 群集上使用 Azure Dev Spaces。 有关将启用了 API 服务器授权 IP 地址范围的 AKS 群集与 Azure Dev Spaces 配合使用的详细信息，请参阅[此处](configure-networking.md#using-api-server-authorized-ip-ranges)。

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>我是否可以在限制群集节点的出口流量的 AKS 群集上使用 Azure Dev Spaces？

可以，一旦允许使用正确的 FQDN，你就可以在启用了[限制群集节点的出口流量][aks-restrict-egress-traffic]的 AKS 群集上使用 Azure Dev Spaces。 有关如何将启用了限制群集节点的出口流量的 AKS 群集与 Azure Dev Spaces 配合使用的详细信息，请参阅[此处](configure-networking.md#ingress-and-egress-network-traffic-requirements)。

## <a name="can-i-use-azure-dev-spaces-on-kubernetes-rbac-enabled-aks-clusters"></a>我是否可以在启用了 Kubernetes RBAC 的 AKS 群集上使用 Azure Dev Spaces？

可以，你可以在启用了或不启用基于 Kubernetes 角色的访问控制 (Kubernetes RBAC) 的 AKS 群集上使用 Azure Dev Spaces。

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>当在 Visual Studio 中为项目启用入口后，会发生什么情况？

使用 Visual Studio 准备项目时，可以选择为服务启用入口。 启用入口会创建一个公共终结点，以便在 AKS 群集上运行时访问服务，这是可选的。 如果不启用入口，则只能从 AKS 群集中访问服务。

## <a name="can-i-use-pod-managed-identities-with-azure-dev-spaces"></a>我是否可以将 Pod 托管标识与 Azure Dev Spaces 配合使用？

可以，你可以在启用了 Azure Dev Spaces 的 AKS 群集上使用 [Pod 托管标识][aks-pod-managed-id]，但在具有 Pod 托管标识的群集上启用 Azure Dev Spaces 后，还需要执行[其他配置步骤][dev-spaces-pod-managed-id-steps]。 如果已安装了 Pod 托管标识并且希望卸载它，则可以在[卸载说明][aks-pod-managed-id-uninstall]中找到更多详细信息。

## <a name="can-i-use-azure-dev-spaces-with-multiple-microservices-in-an-application"></a>我是否可以将 Azure Dev Spaces 与应用程序中的多个微服务配合使用？

可以，你可以在具有多个微服务的应用程序中使用 Azure Dev Spaces，但必须在其根目录中准备并运行单个微服务。 Azure Dev Spaces CLI、Azure Dev Spaces VS Code 扩展和 Visual Studio Azure 开发工作负载需要 azds.yaml 文件位于微服务的根目录，才能运行和调试。 有关单个应用程序中的多个微服务的示例，请参阅[单车共享示例应用程序][bike-sharing]。

在 Visual Studio Code 中，可以[在单个工作区中打开单独的项目][vs-code-multi-root-workspaces]，并通过 Azure Dev Spaces 单独对它们进行调试。 每个项目都必须自包含，并为 Azure Dev Spaces 做好准备。

在 Visual Studio 中，可以配置 .NET Core 解决方案，以便通过 Azure Dev Spaces 进行调试。

## <a name="can-i-use-azure-dev-spaces-with-a-service-mesh"></a>我是否可以将 Azure Dev Spaces 与服务网格配合使用？

此时，不能将 Azure Dev Spaces 与服务网格（如 [Istio][istio] 或 [Linkerd][linkerd]）配合使用。 可以在同一 AKS 群集上运行 Azure Dev Spaces 和服务网格，但不能在同一命名空间中同时启用 Azure Dev Spaces 和服务网格。

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-migration]: ../aks/aks-migration.md
[aks-pod-managed-id]: ../aks/developer-best-practices-pod-security.md#use-pod-managed-identities
[aks-pod-managed-id-uninstall]: https://github.com/Azure/aad-pod-identity#uninstall-notes
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[aks-supported-k8s]: ../aks/supported-kubernetes-versions.md#azure-portal-and-cli-versions
[bike-sharing]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[dev-spaces-pod-managed-id-steps]: troubleshooting.md#error-no-azureassignedidentity-found-for-podazdsazds-webhook-deployment-id-in-assigned-state
[dev-spaces-prep]: how-dev-spaces-works-prep.md
[dev-spaces-routing]: how-dev-spaces-works-routing.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[istio]: https://istio.io/
[linkerd]: https://linkerd.io/
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[vs-code-multi-root-workspaces]: https://code.visualstudio.com/docs/editor/multi-root-workspaces
[windows-containers]: how-to/run-dev-spaces-windows-containers.md