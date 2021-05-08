---
title: 为 Azure Red Hat OpenShift 4 配置内置容器注册表
description: 为 Azure Red Hat OpenShift 4 配置内置容器注册表
author: jiangma
ms.author: jiangma
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: dda050b8d824f0ff0ac1c84d2f008387de55aedf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "100636369"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>为 Azure Red Hat OpenShift 4 配置内置容器注册表

Azure Red Hat OpenShift 提供了被称为 [OpenShift 容器注册表 (OCR)](https://docs.openshift.com/container-platform/4.6/registry/architecture-component-imageregistry.html) 的集成容器映像注册表，该注册表增加了按需自动预配新映像存储库的功能。 它为用户提供一个内置位置，以便用户的应用程序内部版本推送生成的映像。

本文介绍如何为 Azure Red Hat OpenShift (ARO) 4 群集配置该内置容器映像注册表。 将了解如何执行以下操作：

> [!div class="checklist"]
> * 设置 Azure AD
> * 设置 OpenID 连接
> * 访问该内置容器映像注册表

## <a name="before-you-begin"></a>开始之前

本文假定你有现有的 ARO 群集。 如果需要 ARO 群集，请参阅 ARO 教程[创建 Azure Red Hat OpenShift 4 群集](./tutorial-create-cluster.md)。 请确保在创建群集时将 `--pull-secret` 参数设置为 `az aro create`。  这对于配置 Azure Active Directory 身份验证和内置容器注册表是必需的。

在获得群集后，请按照[连接到 Azure Red Hat OpenShift 4 群集](./tutorial-connect-cluster.md)中的步骤连接到该群集。
   * 请务必遵循“安装 OpenShift CLI”中的步骤，因为我们将在本文的后面部分使用 `oc` 命令。
   * 请记下群集控制台 URL，如 `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` 所示。 本文后面会用到 `<random>` 和 `<region>` 的值。
   * 请记下 `kubeadmin` 凭据。 本文后面也将会用到这些凭据。

### <a name="configure-azure-active-directory-authentication"></a>配置 Azure Active Directory 身份验证 

Azure Active Directory (Azure AD) 实现 OpenID Connect (OIDC)。 利用 OIDC，你可以使用 Azure AD 来登录到 ARO 群集。 请按照[配置 Azure Active Directory 身份验证](configure-azure-ad-cli.md)中的步骤来设置群集。

## <a name="access-the-built-in-container-image-registry"></a>访问该内置容器映像注册表

现在，你已为 ARO 群集设置了身份验证方法，接下来让我们启用对内置注册表的访问。

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>将 Azure AD 用户定义为管理员

1. 使用 Azure AD 用户的凭据，从浏览器登录到 OpenShift Web 控制台。 我们将针对 Azure Active Directory 使用 OpenShift OpenID 身份验证，以便使用 OpenID 来定义管理员。

   1. 使用 InPrivate、Incognito 或其他等效浏览器窗口功能来登录到该控制台。 在启用 OIDC 后，窗口的外观将有所不同。
   
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="已启用 OpenID Connect 的登录窗口。":::
   1. 选择“openid”

   > [!NOTE]
   > 记下用于在此登录的用户名和密码。 此用户名和密码将充当本文及其他文章中其他操作的管理员。
2. 使用以下步骤登录到 OpenShift CLI。  为便于讨论，此过程被称为“`oc login`”。
   1. 在 Web 控制台的右上角，展开已登录用户的上下文菜单，然后选择“复制登录命令”。
   2. 如有必要，请使用同一用户登录到新选项卡窗口。
   3. 选择“显示令牌”。
   4. 将“使用此令牌登录”下方的值复制到剪贴板并在 shell 中运行，如下所示。

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

3. 在控制台中运行 `oc whoami`，并将输出记录为 \<aad-user>。  本文稍后会用到此值。
4. 从 OpenShift Web 控制台注销。 选择浏览器窗口右上方标记为 \<aad-user> 的按钮，然后选择“注销” 。


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>向 Azure AD 用户授予注册表交互所需的角色

1. 使用 `kubeadmin` 凭据从浏览器登录到 OpenShift Web 控制台。
1. 通过遵循上述 `oc login` 的步骤使用 `kubeadmin` 的令牌登录到 OpenShift CLI，但在使用 `kubeadmin` 登录到 Web 控制台后执行这些操作。
1. 执行以下命令，以便为 aad-user 启用对内置注册表的访问。

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   
   # Output should look similar to the following.
   # Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge

   # Output should look similar to the following.
   # config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>

   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   # Output should look similar to the following.
   # clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

#### <a name="obtain-the-container-registry-url"></a>获取容器注册表 URL

如下一步所示，使用 `oc get route` 命令来获取容器注册表 URL。

```bash
# Note: the value of "Container Registry URL" in the output is the fully qualified registry name.
HOST=$(oc get route default-route --template='{{ .spec.host }}')
echo "Container Registry URL: $HOST"
```

   > [!NOTE]
   > 请注意容器注册表 URL 的控制台输出。 它将用作本指南以及后续指南的完全限定注册表名称。

## <a name="next-steps"></a>后续步骤

现在，你已经设置了内置容器映像注册表，接下来可以通过在 OpenShift 上部署应用程序来开始使用了。 对于 Java 应用程序，请查阅[在 Azure Red Hat OpenShift 4 群集上使用 Open Liberty/WebSphere Liberty 部署 Java 应用程序](howto-deploy-java-liberty-app.md)。