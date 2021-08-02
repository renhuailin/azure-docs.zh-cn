---
title: 在 Azure 云服务（外延支持）中应用密钥保管库 VM 扩展
description: 启用 Azure 云服务（外延支持）的密钥保管库 VM 扩展
ms.topic: how-to
ms.service: cloud-services-extended-support
author: shisriva
ms.author: shisriva
ms.reviewer: gachandw
ms.date: 05/12/2021
ms.custom: ''
ms.openlocfilehash: 27a25cbff54acda871530daca455045e5519c936
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110103728"
---
# <a name="apply-the-key-vault-vm-extension-to-azure-cloud-services-extended-support"></a>将密钥保管库 VM 扩展应用到 Azure 云服务（外延支持）

## <a name="what-is-the-key-vault-vm-extension"></a>什么是密钥保管库 VM 扩展？
通过密钥保管库 VM 扩展，可自动刷新 Azure 密钥保管库中存储的证书。 具体而言，该扩展监视一系列观测到的存储在密钥保管库中的证书，并在检测到更改时检索并安装相应的证书。 有关更多详细信息，请参阅[适用于 Windows 的密钥保管库 VM 扩展](../virtual-machines/extensions/key-vault-windows.md)。

## <a name="whats-new-in-the-key-vault-vm-extension"></a>密钥保管库 VM 扩展中有哪些新增功能？
现在，密钥保管库 VM 扩展在 Azure 云服务（外延支持）平台上受支持，可启用端到端的证书管理。 该扩展现在可以按预定义的轮询间隔从配置的密钥保管库拉取证书，并安装这些证书以供服务使用。 

## <a name="how-can-i-leverage-the-key-vault-vm-extension"></a>如何利用密钥保管库 VM 扩展？
以下教程介绍了如何在 PaaSV1 服务上安装密钥保管库 VM 扩展，其方法是先在保管库中创建启动证书，以从 AAD 获取令牌，该令牌有助于在保管库中进行扩展身份验证。 设置身份验证过程并安装扩展后，将按定期的轮询间隔自动拉取所有最新证书。 


## <a name="prerequisites"></a>先决条件 
若要使用 Azure 密钥保管库 VM 扩展，需要一个 Azure Active Directory 租户。 有关设置新 Active Directory 租户的详细信息，请参阅[设置 AAD 租户](../active-directory/develop/quickstart-create-new-tenant.md)

## <a name="enable-the-azure-key-vault-vm-extension"></a>启用 Azure 密钥保管库 VM 扩展

1. 在保管库中[生成证书](../key-vault/certificates/create-certificate-signing-request.md)，并下载该证书的 .cer。

2. 在 [Azure 门户](https://portal.azure.com)中导航到“应用注册”。
    
    :::image type="content" source="media/app-registration-0.jpg" alt-text="显示在门户中选择“应用注册”。":::
    

3. 在“应用注册”页中，选择页面左上角的“新建注册”
    
    :::image type="content" source="media/app-registration-1.png" alt-text="显示 Azure 门户中的“应用注册”。":::

4. 在下一页上，可以填写表单并完成应用创建。

5. 将证书的 .cer 上传到 Azure Active Directory 应用门户。

    - 还可以选择性地利用[密钥保管库事件网格通知功能](https://azure.microsoft.com/updates/azure-key-vault-event-grid-integration-is-now-available/)来上传证书。  

6. 在密钥保管库中授予 Azure Active Directory 应用机密列出/获取权限：
    - 如果使用的是 RBAC 预览版，请搜索创建的 AAD 应用的名称，并为其分配“密钥保管库机密用户(预览版)”角色。
    - 如果使用的是保管库访问策略，请为创建的 AAD 应用分配“机密获取”权限。 有关详细信息，请参阅[分配访问策略](../key-vault/general/assign-access-policy-portal.md)

7. 使用 ARM 模板安装第一步中创建的证书的第一个版本和密钥保管库 VM 扩展，如下所示：

    ```json
        {
       "osProfile":{
          "secrets":[
             {
                "sourceVault":{
                   "id":"[parameters('sourceVaultValue')]"
                },
                "vaultCertificates":[
                   {
                      "certificateUrl":"[parameters('bootstrpCertificateUrlValue')]"
                   }
                ]
             }
          ]
       }{
          "name":"KVVMExtensionForPaaS",
          "properties":{
             "type":"KeyVaultForPaaS",
             "autoUpgradeMinorVersion":true,
             "typeHandlerVersion":"1.0",
             "publisher":"Microsoft.Azure.KeyVault",
             "settings":{
                "secretsManagementSettings":{
                   "pollingIntervalInS":"3600",
                   "certificateStoreName":"My",
                   "certificateStoreLocation":"LocalMachine",
                   "linkOnRenewal":false,
                   "requireInitialSync":false, 
                   "observedCertificates":"[parameters('keyVaultObservedCertificates']"
                },
                "authenticationSettings":{
                   "clientId":"Your AAD app ID",
                   "clientCertificateSubjectName":"Your boot strap certificate subject name [Do not include the 'CN=' in the subject name]"
                }
             }
          }
       }
    ```
    可能需要在 ServiceDefinition.csdef 中指定启动证书的证书存储，如下所示：
    
    ```xml
        <Certificates>
                 <Certificate name="bootstrapcert" storeLocation="LocalMachine" storeName="My" />
        </Certificates> 
    ```

## <a name="next-steps"></a>后续步骤
通过[在云服务（外延支持）中启用监视](enable-alerts.md)来进一步改善部署