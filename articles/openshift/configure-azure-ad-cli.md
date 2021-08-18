---
title: 运行 OpenShift 4 的 Azure Red Hat OpenShift - 使用命令行配置 Azure Active Directory 身份验证
description: 了解如何使用命令行为运行 OpenShift 4 的 Azure Red Hat OpenShift 群集配置 Azure Active Directory 身份验证
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: aro、openshift、az aro、red hat、cli
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 9571e7692a70d36155f395ec57bdedafe5970ac8
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2021
ms.locfileid: "112369764"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-cli"></a>为 Azure Red Hat OpenShift 4 群集配置 Azure Active Directory 身份验证 (CLI)

如果选择在本地安装并使用 CLI，本文要求运行 Azure CLI 2.6.0 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

检索特定于群集的 URL，这些 URL 将用于配置 Azure Active Directory 应用程序。

设置资源组和群集名称的变量。

将 \<resource_group> 替换为资源组的名称，将 \<aro_cluster> 替换为群集的名称 。

```azurecli-interactive
resource_group=<resource_group>
aro_cluster=<aro_cluster>
```

构造群集的 OAuth 回叫 URL，并将其存储在变量 oauthCallbackURL 中。 

> [!NOTE]
> OAuth 回叫 URL 中的 `AAD` 部分应与稍后设置的 OAuth 标识提供程序名称匹配。


```azurecli-interactive
domain=$(az aro show -g $resource_group -n $aro_cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g $resource_group -n $aro_cluster --query location -o tsv)
apiServer=$(az aro show -g $resource_group -n $aro_cluster --query apiserverProfile.url -o tsv)
webConsole=$(az aro show -g $resource_group -n $aro_cluster --query consoleProfile.url -o tsv)
```

oauthCallbackURL 的格式与自定义域略有不同：

* 如果使用的是自定义域（例如 `contoso.com`），请运行以下命令。 

    ```azurecli-interactive
    oauthCallbackURL=https://oauth-openshift.apps.$domain/oauth2callback/AAD
    ```

* 如果未使用自定义域，那么 `$domain` 将是含八个字符的 alnum 字符串，并由 `$location.aroapp.io` 进行扩展。

    ```azurecli-interactive
    oauthCallbackURL=https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD
    ```

> [!NOTE]
> OAuth 回叫 URL 中的 `AAD` 部分应与稍后设置的 OAuth 标识提供程序名称匹配。

## <a name="create-an-azure-active-directory-application-for-authentication"></a>创建适用于身份验证的 Azure Active Directory 应用程序

为应用程序将 \<client_secret> 替换为安全密码。

```azurecli-interactive
client_secret=<client_secret>
```

创建 Azure Active Directory 应用程序并检索创建的应用程序标识符。

```azurecli-interactive
app_id=$(az ad app create \
  --query appId -o tsv \
  --display-name aro-auth \
  --reply-urls $oauthCallbackURL \
  --password $client_secret)
```

检索拥有应用程序的订阅的租户 ID。

```azure
tenant_id=$(az account show --query tenantId -o tsv)
```

## <a name="create-a-manifest-file-to-define-the-optional-claims-to-include-in-the-id-token"></a>创建清单文件以定义要包含在 ID 令牌中的可选声明

应用程序开发人员可以在其 Azure AD 应用程序中使用[可选声明](../active-directory/develop/active-directory-optional-claims.md)，用于指定希望通过令牌发送给应用程序的声明。

使用可选声明可以：

- 选择要包含在应用程序令牌中的附加声明。
- 更改 Azure AD 在令牌中返回的某些声明的行为。
- 添加和访问应用程序的自定义声明。

我们将 OpenShift 配置为使用 `email` 声明，并回退到 `upn` 以设置“首选用户名”，方法是将 `upn` 添加到 Azure Active Directory 返回的 ID 令牌中。

创建 manifest.json 文件以配置 Azure Active Directory 应用程序。

```bash
cat > manifest.json<< EOF
[{
  "name": "upn",
  "source": null,
  "essential": false,
  "additionalProperties": []
},
{
"name": "email",
  "source": null,
  "essential": false,
  "additionalProperties": []
}]
EOF
```

## <a name="update-the-azure-active-directory-applications-optionalclaims-with-a-manifest"></a>使用清单更新 Azure Active Directory 应用程序的 optionalClaims

```azurecli-interactive
az ad app update \
  --set optionalClaims.idToken=@manifest.json \
  --id $app_id
```

## <a name="update-the-azure-active-directory-application-scope-permissions"></a>更新 Azure Active Directory 范围权限

为了能够从 Azure Active Directory 读取用户信息，我们需要定义适当的范围。

为 Azure Active Directory Graph.User.Read 范围添加权限，以便能够登录和读取用户配置文件。

```azurecli-interactive
az ad app permission add \
 --api 00000002-0000-0000-c000-000000000000 \
 --api-permissions 311a71cc-e848-46a1-bdf8-97ff7156d8e6=Scope \
 --id $app_id
```

> [!NOTE]
> 可以放心地忽略该消息来授予同意，除非你已通过身份验证成为了此 Azure Active Directory 的全局管理员。 标准域用户首次使用其 AAD 凭据登录到群集时，系统将要求其授予同意。

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>将用户和组分配到群集（可选）

默认情况下，在 Azure Active Directory (Azure AD) 租户中注册的应用程序可供租户的所有已成功进行身份验证的用户使用。 租户管理员和开发人员可以通过 Azure AD 将应用限制为仅供租户中特定的一组用户或安全组使用。

按照 Azure Active Directory 文档上的说明[向应用分配用户和组](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md)。

## <a name="configure-openshift-openid-authentication"></a>配置 OpenShift OpenID 身份验证

检索 `kubeadmin` 凭据。 请运行以下命令，找到 `kubeadmin` 用户的密码。

```azurecli-interactive
kubeadmin_password=$(az aro list-credentials \
  --name $aro_cluster \
  --resource-group $resource_group \
  --query kubeadminPassword --output tsv)
```

使用以下命令登录到 OpenShift 群集的 API 服务器。 

```azurecli-interactive
oc login $apiServer -u kubeadmin -p $kubeadmin_password
```

创建 OpenShift 机密来存储 Azure Active Directory 应用程序机密。

```azurecli-interactive
oc create secret generic openid-client-secret-azuread \
  --namespace openshift-config \
  --from-literal=clientSecret=$client_secret
```

创建 oidc yaml 文件，针对 Azure Active Directory 配置 OpenShift OpenID 身份验证。 

```bash
cat > oidc.yaml<< EOF
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: AAD
    mappingMethod: claim
    type: OpenID
    openID:
      clientID: $app_id
      clientSecret:
        name: openid-client-secret-azuread
      extraScopes:
      - email
      - profile
      extraAuthorizeParameters:
        include_granted_scopes: "true"
      claims:
        preferredUsername:
        - email
        - upn
        name:
        - name
        email:
        - email
      issuer: https://login.microsoftonline.com/$tenant_id
EOF
```

将配置应用于群集。

```azurecli-interactive
oc apply -f oidc.yaml
```

返回的响应将与以下内容类似。

```output
oauth.config.openshift.io/cluster configured
```

## <a name="verify-login-through-azure-active-directory"></a>通过 Azure Active Directory 验证登录

如果现在注销 OpenShift Web 控制台并尝试再次登录，你将看到一个使用 AAD 登录的新选项。 你可能需要等待几分钟。

![具有 Azure Active Directory 选项的登录屏幕](media/aro4-login-2.png)
