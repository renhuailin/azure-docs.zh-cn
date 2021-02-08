---
title: 使用 Azure 门户设置 Azure 证明
description: 如何使用 Azure 门户设置和配置证明提供程序。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 86adac557c6de133e95e97bfedbd302cc6a2b27e
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2021
ms.locfileid: "99429151"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-portal"></a>快速入门：使用 Azure 门户设置 Azure 证明

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

请按照以下步骤使用 Azure 门户管理证明提供程序。

## <a name="1-attestation-provider"></a>1.证明提供程序

### <a name="11-create-an-attestation-provider"></a>1.1 创建证明提供程序

#### <a name="111-to-configure-the-provider-with-unsigned-policies"></a>1.1.1 配置具有未签名策略的提供程序

1.  在 Azure 门户菜单或“主页”中，选择“创建资源”
2.  在搜索框中输入“证明”
3.  在结果列表中选择“Microsoft Azure 证明”
4.  在“Microsoft Azure 证明”页上，选择“创建”
5.  在“创建证明提供程序”页上，提供以下输入：
    
    订阅：选择订阅
    
    **资源组**：选择一个现有资源组，或者选择“新建”并输入资源组名称
    
    **名称**：必须提供唯一的名称

    **位置**：选择一个位置 
    
    **策略签名者证书文件**：请勿上传策略签名者证书文件来配置具有未签名策略的提供程序 
6.  提供所需的输入后，单击“查看+创建”
7.  修复验证问题（如果有），然后单击“创建”。

#### <a name="112-to-configure-the-provider-with-signed-policies"></a>1.1.2 配置具有签名策略的提供程序

1.  在 Azure 门户菜单或“主页”中，选择“创建资源”
2.  在搜索框中输入“证明”
3.  在结果列表中选择“Microsoft Azure 证明”
4.  在“Microsoft Azure 证明”页上，选择“创建”
5.  在“创建证明提供程序”页上，提供以下信息：
    
    a. 订阅：选择订阅
    
    b. **资源组**：选择一个现有资源组，或者选择“新建”并输入资源组名称
    
    c. **名称**：必须提供唯一的名称

    d. **位置**：选择一个位置 
    
    e. **策略签名者证书文件**：要配置具有策略签名证书的证明提供程序，请上传证书文件。 请参阅[此处](./policy-signer-examples.md)的示例 
6.  提供所需的输入后，单击“查看+创建”
7.  修复验证问题（如果有），然后单击“创建”。

### <a name="12-view-attestation-provider"></a>1.2 查看证明提供程序

1.  在 Azure 门户菜单或“主页”页上，选择“所有资源”
2.  在筛选器框中，输入证明提供程序名称并选择它

### <a name="13-delete-attestation-provider"></a>1.3 删除证明提供程序

1.  在 Azure 门户菜单或“主页”页上，选择“所有资源”
2.  在筛选器框中，输入证明提供程序名称
3.  选中该复选框，然后单击“删除”
4.  键入“是”并单击“删除”[或者]
1.  在 Azure 门户菜单或“主页”页上，选择“所有资源”
2.  在筛选器框中，输入证明提供程序名称
3.  选择证明提供程序并导航到概述页面
4.  单击顶部菜单中的“删除”，然后单击“是” 


## <a name="2-attestation-policy-signers"></a>2.证明策略签名者

### <a name="21-view-policy-signer-certificates"></a>2.1 查看策略签名者证书

1.  在 Azure 门户菜单或“主页”页上，选择“所有资源”
2.  在筛选器框中，输入证明提供程序名称
3.  选择证明提供程序并导航到概述页面
4.  单击左侧资源菜单或底部窗格中的“策略签名者证书”
5.  单击“下载策略签名者证书”（如果创建的证明提供程序没有策略签名要求，该按钮将被禁用）
6.  下载的文本文件将具有 JWS 格式的所有证书。
a.  验证已下载的证书计数和证书。

### <a name="22-add-policy-signer-certificate"></a>2.2 添加策略签名者证书

1.  在 Azure 门户菜单或“主页”页上，选择“所有资源”
2.  在筛选器框中，输入证明提供程序名称
3.  选择证明提供程序并导航到概述页面
4.  单击左侧资源菜单或底部窗格中的“策略签名者证书”
5.  单击顶部菜单中的“添加”（如果创建的证明提供程序没有策略签名要求，该按钮将被禁用）
6.  上传策略签名者证书文件，然后单击“添加”。 请参阅[此处](./policy-signer-examples.md)的示例

### <a name="23-delete-policy-signer-certificate"></a>2.3 删除策略签名者证书

1.  在 Azure 门户菜单或“主页”页上，选择“所有资源”
2.  在筛选器框中，输入证明提供程序名称
3.  选择证明提供程序并导航到概述页面
4.  单击左侧资源菜单或底部窗格中的“策略签名者证书”
5.  单击顶部菜单中的“删除”（如果创建的证明提供程序没有策略签名要求，该按钮将被禁用）
6.  上传策略签名者证书文件，然后单击“删除”。 请参阅[此处](./policy-signer-examples.md)的示例 

## <a name="3-attestation-policy"></a>3.证明策略

### <a name="31-view-attestation-policy"></a>3.1 查看证明策略

1.  在 Azure 门户菜单或“主页”页上，选择“所有资源”
2.  在筛选器框中，输入证明提供程序名称
3.  选择证明提供程序并导航到概述页面
4.  单击左侧资源菜单或底部窗格中的“策略”
5.  选择首选的“证明类型”并查看“当前策略” 

### <a name="32-configure-attestation-policy"></a>3.2 配置证明策略

#### <a name="321-when-attestation-provider-is-created-without-policy-signing-requirement"></a>3.2.1 如果创建的证明提供程序没有策略签名要求

##### <a name="upload-policy-in-jwt-format"></a>以 JWT 格式上传策略

1.  在 Azure 门户菜单或“主页”页上，选择“所有资源”
2.  在筛选器框中，输入证明提供程序名称
3.  选择证明提供程序并导航到概述页面
4.  单击左侧资源菜单或底部窗格中的“策略”
5.  单击顶部菜单中的“配置”
6.  如果创建的证明提供程序没有策略签名要求，则用户可以使用“JWT”或“文本”格式上传策略 
7.  为“策略格式”选择 JWT 
8.  以“未签名/已签名 JWT”格式上传策略文件以及策略内容，然后单击“保存” 。 请参阅[此处](./policy-examples.md)的示例
    
    对于文件上传选项，策略预览将以文本格式显示，并且策略预览不可编辑。

7.  单击顶部菜单中的“刷新”以查看配置的策略

##### <a name="upload-policy-in-text-format"></a>以文本格式上传策略

1.  在 Azure 门户菜单或“主页”页上，选择“所有资源”
2.  在筛选器框中，输入证明提供程序名称
3.  选择证明提供程序并导航到概述页面
4.  单击左侧资源菜单或底部窗格中的“策略”
5.  单击顶部菜单中的“配置”
6.  如果创建的证明提供程序没有策略签名要求，则用户可以使用“JWT”或“文本”格式上传策略 
7.  为“策略格式”选择“文本” 
8.  以“文本”格式上传策略文件及内容，或在文本区域中输入策略内容，然后单击“保存” 。 请参阅[此处](./policy-examples.md)的示例

    对于文件上传选项，策略预览将以文本格式显示，并且策略预览不可编辑。

8.  单击“刷新”以查看配置的策略

#### <a name="322-when-attestation-provider-is-created-with-policy-signing-requirement"></a>3.2.2 如果创建的证明提供程序有策略签名要求

##### <a name="upload-policy-in-jwt-format"></a>以 JWT 格式上传策略

1.  在 Azure 门户菜单或“主页”页上，选择“所有资源”
2.  在筛选器框中，输入证明提供程序名称
3.  选择证明提供程序并导航到概述页面
4.  单击左侧资源菜单或底部窗格中的“策略”
5.  单击顶部菜单中的“配置”
6.  如果创建的证明提供程序有策略签名要求，则用户只能以“已签名 JWT 格式”上传策略
7.  以“已签名 JWT 格式”上传策略文件，然后单击“保存” 。 请参阅[此处](./policy-examples.md)的示例

    对于文件上传选项，策略预览将以文本格式显示，并且策略预览不可编辑。
    
8.  单击“刷新”以查看配置的策略

## <a name="next-steps"></a>后续步骤

- [如何创作证明策略并对其签名](author-sign-policy.md)
- [使用代码示例证明 SGX enclave](/samples/browse/?expanded=azure&terms=attestation)

