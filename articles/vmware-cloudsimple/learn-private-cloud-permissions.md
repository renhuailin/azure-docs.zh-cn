---
title: Azure VMware Solution by CloudSimple - 私有云权限模型
description: 介绍 CloudSimple 私有云权限模型、分组和类别
author: suzizuber
ms.author: v-szuber
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c49dcb4158785170d67742df5c8c797b139edbe5
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620291"
---
# <a name="cloudsimple-private-cloud-permission-model-of-vmware-vcenter"></a>VMware vCenter 的 CloudSimple 私有云权限模型

CloudSimple 保留对私有云环境的完全管理访问权限。 为每位 CloudSimple 客户授予了足够的管理权限，使其能够在自己的环境中部署和管理虚拟机。  如果需要，你可以暂时提升自己的权限以执行管理功能。

## <a name="cloud-owner"></a>云所有者

创建私有云时，会在 vCenter 单一登录域中创建一个 CloudOwner 用户，该用户具有 Cloud-Owner-Role 访问权限，可管理私有云中的对象 。 此用户还可以设置私有云 vCenter 的其他 [vCenter 标识源](set-vcenter-identity.md)和其他用户。

> [!NOTE]
> 创建私有云时，CloudSimple 私有云 vCenter 的默认用户是 cloudowner@cloudsimple.local。

## <a name="user-groups"></a>用户组

在部署私有云的过程中，会创建一个名为“Cloud-Owner-Group”的组。 此组中的用户可以管理私有云上的 vSphere 环境的各个部分。 会自动授予此组 Cloud-Owner-Role 权限，并且会将 CloudOwner 用户添加为此组的成员 。  CloudSimple 创建具有受限权限的其他组，以便进行轻松管理。  可将任何用户添加到这些预先创建的组，并且会自动将以下定义的权限分配给这些组中的用户。

### <a name="pre-created-groups"></a>预先创建的组

| 组名 | 目的 | 角色 |
| -------- | ------- | ------ |
| Cloud-Owner-Group | 此组的成员具有对私有云 vCenter 的管理权限 | [Cloud-Owner-Role](#cloud-owner-role) |
| Cloud-Global-Cluster-Admin-Group | 此组的成员具有对私有云 vCenter 群集的管理权限 | [Cloud-Cluster-Admin-Role](#cloud-cluster-admin-role) |
| Cloud-Global-Storage-Admin-Group | 此组的成员可以管理私有云 vCenter 上的存储 | [Cloud-Storage-Admin-Role](#cloud-storage-admin-role) |
| Cloud-Global-Network-Admin-Group | 此组的成员可以在私有云 vCenter 上管理网络和分布式端口组 | [Cloud-Network-Admin-Role](#cloud-network-admin-role) |
| Cloud-Global-VM-Admin-Group | 此组的成员可以管理私有云 vCenter 上的虚拟机 | [Cloud-VM-Admin-Role](#cloud-vm-admin-role) |

若要向单个用户授予管理私有云的权限，请创建用户帐户并将其添加到相应的组。

> [!CAUTION]
> 新用户只能添加到 Cloud-Owner-Group、Cloud-Global-Cluster-Admin-Group、Cloud-Global-Storage-Admin-Group、Cloud-Global-Network-Admin-Group或 Cloud-Global-VM-Admin-Group。  添加到“管理员”组的用户将被自动删除。  服务帐户只能添加到“管理员”组，且服务帐户不得用于登录 vSphere Web UI。

## <a name="list-of-vcenter-privileges-for-default-roles"></a>默认角色的 vCenter 权限列表

### <a name="cloud-owner-role"></a>Cloud-Owner-Role

| **类别** | **Privilege** |
|----------|-----------|
| **警报** | 确认警报 <br> 创建警报 <br> 禁用警报操作 <br> 修改警报 <br> 删除警报 <br> 设置警报状态 |
| **权限** | 修改权限 |
| **内容库** | 添加库项 <br> 创建本地库 <br> 创建订阅的库 <br> 删除库项 <br> 删除本地库 <br> 删除订阅的库 <br> 下载文件 <br> 逐出库项 <br> 逐出订阅的库 <br> 导入存储 <br> 探测订阅信息 <br> 读取存储 <br> 同步库项 <br> 同步订阅的库 <br> 类型自检 <br> 更新配置设置 <br> 更新文件 <br> 更新库 <br> 更新库项 <br> 更新本地库 <br> 更新订阅的库 <br> 查看配置设置 |
| **加密操作** | 添加磁盘 <br> 克隆 <br> 解密 <br> 直接访问 <br> 加密 <br> 加密新增内容 <br> 管理 KMS <br> 管理加密策略 <br> 管理密钥 <br> Migrate <br> 重新加密 <br> 注册 VM <br> 注册主机 |
| **dvPort 组** | 创建 <br> 删除 <br> 修改 <br> 策略操作 <br> 范围操作 |
| **数据存储** | 分配空间 <br> 浏览数据存储 <br> 配置数据存储 <br> 低级别文件操作 <br> 移动数据存储 <br> 删除数据存储 <br> 删除文件 <br> 重命名数据存储 <br> 更新虚拟机文件 <br> 更新虚拟机元数据 |
| **ESX 代理管理器** | Config <br> 修改 <br> 查看 |
| **扩展名** | 注册扩展 <br> 注销扩展 <br> 更新扩展 |
| **外部统计信息提供程序**| 注册 <br> 注销 <br> 更新 |
| **文件夹** | 创建文件夹 <br> 删除文件夹 <br> 移动文件夹 <br> 重命名文件夹 |
| **全球** | 取消任务 <br> 容量计划 <br> 诊断 <br> 禁用方法 <br> 启用方法 <br> 全局标记 <br> 健康产业 <br> 许可证 <br> 记录事件 <br> 管理自定义属性 <br> 代理 <br> 脚本操作 <br> 服务管理器 <br> 设置自定义属性 <br> 系统标记 |
| **运行状况更新提供程序** | 注册 <br> 注销 <br> 更新 |
| **主机 > 配置** | 存储分区配置 |
| **主机 > 库存** | 修改群集 |
| **vSphere 标记** | 分配或取消分配 vSphere 标记 <br> 创建 vSphere 标记 <br> 创建 vSphere 标记类别 <br> 删除 vSphere 标记 <br> 删除 vSphere 标记类别 <br> 编辑 vSphere 标记 <br> 编辑 vSphere 标记类别 <br> 修改类别的 UsedBy 字段 <br> 修改标记的 UsedBy 字段 |
| **Network** | Assign network <br> 配置 <br> 移动网络 <br> 删除 |
| **性能** | 修改间隔 |
| **主机配置文件** | 查看 |
| **资源** | 应用建议 <br> 将 vApp 分配到资源池 <br> Assign virtual machine to resource pool <br> 创建资源池 <br> 迁移已关闭的虚拟机 <br> 迁移已启动的虚拟机 <br> 修改资源池 <br> 移动资源池 <br> 查询 vMotion <br> 删除资源池 <br> 重命名资源池 |
| **计划任务** | 创建任务 <br> 修改任务 <br> 删除任务 <br> 运行任务 |
| **会话** | 模拟用户 <br> Message <br> 验证会话 <br> 查看和停止会话 |
| **数据存储群集** | 配置数据存储群集 |
| **配置文件驱动的存储** | 配置文件驱动的存储更新 <br> 配置文件驱动的存储视图 |
| **存储视图** | 配置服务 <br> 查看 |
| **任务** | 创建任务 <br> 更新任务 |
| **传输服务**| 管理 <br> 监视 |
| **vApp** | 添加虚拟机 <br> 分配资源池 <br> 分配 vApp <br> 克隆 <br> 创建 <br> 删除 <br> 导出 <br> 导入 <br> 移动 <br> 关机 <br> 开机 <br> 重命名 <br> 挂起 <br> 注销 <br> 查看 OVF 环境 <br> vApp 应用程序配置 <br> vApp 实例配置 <br> vApp managedBy 配置 <br> vApp 资源配置 |
| **VRMPolicy** | 查询 VRMPolicy <br> 更新 VRMPolicy |
| **虚拟机 > 配置** | 添加现有磁盘 <br> 添加新磁盘 <br> 添加或删除设备 <br> 高级 <br> 更改 CPU 计数 <br> 更改资源 <br> 配置 managedBy <br> 磁盘更改跟踪 <br> 磁盘租用 <br> 显示连接设置 <br> 扩展虚拟磁盘 <br> 主机 USB 设备 <br> 内存 <br> 修改设备设置 <br> 查询容错兼容性 <br> 查询无主文件 <br> 原始设备 <br> 从路径重载 <br> 删除磁盘 <br> 重命名 <br> 重置来宾信息 <br> 设置批注 <br> 设置 <br> 交换文件位置 <br> 切换分支父级 <br> 解锁虚拟机 <br> 升级虚拟机兼容性 |
| **虚拟机 > 来宾操作** | 来宾操作别名修改 <br> 来宾操作别名查询 <br> 来宾操作修改 <br> 来宾操作程序执行 <br> 来宾操作查询 |
| **虚拟机 > 交互** | 回答问题 <br> 在虚拟机上备份操作 <br> 配置 CD 媒体 <br> 配置软盘媒体 <br> 控制台交互 <br> 创建屏幕截图 <br> 对所有磁盘进行碎片整理 <br> 设备连接 <br> 拖放 <br> 通过 VIX API 管理来宾操作系统 <br> 插入 USB HID 扫描代码 <br> 暂停或取消暂停 <br> 执行擦除或收缩操作 <br> 关机 <br> 开机 <br> 在虚拟机上记录会话 <br> 在虚拟机上重播会话 <br> 重置 <br> 恢复容错 <br> 挂起 <br> 挂起容错 <br> 测试故障转移 <br> 测试重启辅助 VM <br> 关闭容错 <br> 启用容错 <br> VMware 工具安装 |
| **虚拟机 > 清单** | 从现有创建 <br> 新建 <br> 移动 <br> 注册 <br> 删除 <br> 注销 |
| **虚拟机 > 预配** | 允许磁盘访问 <br> 允许文件访问 <br> Allow read-only disk access <br> 允许虚拟机下载 <br> 允许虚拟机文件上传 <br> 克隆模板 <br> 克隆虚拟机 <br> 从虚拟机创建模板 <br> 自定义 <br> 部署模板 <br> 标记为模板 <br> 标记为虚拟机 <br> 修改自定义规范 <br> 提升磁盘 <br> 读取自定义规范 |
| **虚拟机 > 服务配置** | 允许通知 <br> 允许轮询全局事件通知 <br> 管理服务配置 <br> 修改服务配置 <br> 查询服务配置 <br> 读取服务配置 |
| **虚拟机 > 快照管理** | Create snapshot <br> 删除快照 <br> 重命名快照 <br> 还原到快照 |
| **虚拟机 > vSphere 复制** | 配置复制 <br> 管理复制 <br> 监视复制 |
| **vService** | 创建依赖项 <br> 销毁依赖项 <br> 重新配置依赖项配置 <br> 更新依赖项 |

### <a name="cloud-cluster-admin-role"></a>Cloud-Cluster-Admin-Role

| **类别** | **Privilege** |
|----------|-----------|
| **数据存储** | 分配空间 <br> 浏览数据存储 <br> 配置数据存储 <br> 低级别文件操作 <br> 删除数据存储 <br> 重命名数据存储 <br> 更新虚拟机文件 <br> 更新虚拟机元数据 |
| **文件夹** | 创建文件夹 <br> 删除文件夹 <br> 移动文件夹 <br> 重命名文件夹 |
| **主机 > 配置**  | 存储分区配置 |
| **vSphere 标记** | 分配或取消分配 vSphere 标记 <br> 创建 vSphere 标记 <br> 创建 vSphere 标记类别 <br> 删除 vSphere 标记 <br> 删除 vSphere 标记类别 <br> 编辑 vSphere 标记 <br> 编辑 vSphere 标记类别 <br> 修改类别的 UsedBy 字段 <br> 修改标记的 UsedBy 字段 |
| **Network** | Assign network |
| **资源** | 应用建议 <br> 将 vApp 分配到资源池 <br> Assign virtual machine to resource pool <br> 创建资源池 <br> 迁移已关闭的虚拟机 <br> 迁移已启动的虚拟机 <br> 修改资源池 <br> 移动资源池 <br> 查询 vMotion <br> 删除资源池 <br> 重命名资源池 |
| **vApp** | 添加虚拟机 <br> 分配资源池 <br> 分配 vApp <br> 克隆 <br> 创建 <br> 删除 <br> 导出 <br> 导入 <br> 移动 <br> 关机 <br> 开机 <br> 重命名 <br> 挂起 <br> 注销 <br> 查看 OVF 环境 <br> vApp 应用程序配置 <br> vApp 实例配置 <br> vApp managedBy 配置 <br> vApp 资源配置 |
| **VRMPolicy** | 查询 VRMPolicy <br> 更新 VRMPolicy |
| **虚拟机 > 配置** | 添加现有磁盘 <br> 添加新磁盘 <br> 添加或删除设备 <br> 高级 <br> 更改 CPU 计数 <br> 更改资源 <br> 配置 managedBy <br> 磁盘更改跟踪 <br> 磁盘租用 <br> 显示连接设置 <br> 扩展虚拟磁盘 <br> 主机 USB 设备 <br> 内存 <br> 修改设备设置 <br> 查询容错兼容性 <br> 查询无主文件 <br> 原始设备 <br> 从路径重载 <br> 删除磁盘 <br> 重命名 <br> 重置来宾信息 <br> 设置批注 <br> 设置 <br> 交换文件位置 <br> 切换分支父级 <br> 解锁虚拟机 <br> 升级虚拟机兼容性 |
| **虚拟机 > 来宾操作** | 来宾操作别名修改 <br> 来宾操作别名查询 <br> 来宾操作修改 <br> 来宾操作程序执行 <br> 来宾操作查询 |
| **虚拟机 > 交互** | 回答问题 <br> 在虚拟机上备份操作 <br> 配置 CD 媒体 <br> 配置软盘媒体 <br> 控制台交互 <br> 创建屏幕截图 <br> 对所有磁盘进行碎片整理 <br> 设备连接 <br> 拖放 <br> 通过 VIX API 管理来宾操作系统 <br> 插入 USB HID 扫描代码 <br> 暂停或取消暂停 <br> 执行擦除或收缩操作 <br> 关机 <br> 开机 <br> 在虚拟机上记录会话 <br> 在虚拟机上重播会话 <br> 重置 <br> 恢复容错 <br> 挂起 <br> 挂起容错 <br> 测试故障转移 <br> 测试重启辅助 VM <br> 关闭容错 <br> 启用容错 <br> VMware 工具安装
| **虚拟机 > 清单** | 从现有创建 <br> 新建 <br> 移动 <br> 注册 <br> 删除 <br> 注销 |
| **虚拟机 > 预配** | 允许磁盘访问 <br> 允许文件访问 <br> Allow read-only disk access <br> 允许虚拟机下载 <br> 允许虚拟机文件上传 <br> 克隆模板 <br> 克隆虚拟机 <br> 从虚拟机创建模板 <br> 自定义 <br> 部署模板 <br> 标记为模板 <br> 标记为虚拟机 <br> 修改自定义规范 <br> 提升磁盘  <br> 读取自定义规范 |
| **虚拟机 > 服务配置** | 允许通知 <br> 允许轮询全局事件通知 <br> 管理服务配置 <br> 修改服务配置 <br> 查询服务配置 <br> 读取服务配置
| **虚拟机 > 快照管理** | Create snapshot <br> 删除快照 <br> 重命名快照 <br> 还原到快照 |
| **虚拟机 > vSphere 复制** | 配置复制 <br> 管理复制 <br> 监视复制 |
| **vService** | 创建依赖项 <br> 销毁依赖项 <br> 重新配置依赖项配置 <br> 更新依赖项 |

### <a name="cloud-storage-admin-role"></a>Cloud-Storage-Admin-Role

| **类别** | **Privilege** |
|----------|-----------|
| **数据存储** | 分配空间 <br> 浏览数据存储 <br> 配置数据存储 <br> 低级别文件操作 <br> 删除数据存储 <br> 重命名数据存储 <br> 更新虚拟机文件 <br> 更新虚拟机元数据 |
| **主机 > 配置** | 存储分区配置 |
| **数据存储群集** | 配置数据存储群集 |
| **配置文件驱动的存储** | 配置文件驱动的存储更新 <br> 配置文件驱动的存储视图 |
| **存储视图** | 配置服务 <br> 查看 |

### <a name="cloud-network-admin-role"></a>Cloud-Network-Admin-Role

| **类别** | **Privilege** |
|----------|-----------|
| **dvPort 组** | 创建 <br> 删除 <br> 修改 <br> 策略操作 <br> 范围操作 |
| **Network** | Assign network <br> 配置 <br> 移动网络 <br> 删除 |
| **虚拟机 > 配置** | 修改设备设置 |

### <a name="cloud-vm-admin-role"></a>Cloud-VM-Admin-Role

| **类别** | **Privilege** |
|----------|-----------|
| **数据存储** | 分配空间 <br> 浏览数据存储 |
| **Network** | Assign network |
| **资源** | Assign virtual machine to resource pool <br> 迁移已关闭的虚拟机 <br> 迁移已启动的虚拟机
| **vApp** | 导出 <br> 导入 |
| **虚拟机 > 配置** | 添加现有磁盘 <br> 添加新磁盘 <br> 添加或删除设备 <br> 高级 <br> 更改 CPU 计数 <br> 更改资源 <br> 配置 managedBy <br> 磁盘更改跟踪 <br> 磁盘租用 <br> 显示连接设置 <br> 扩展虚拟磁盘 <br> 主机 USB 设备 <br> 内存 <br> 修改设备设置 <br> 查询容错兼容性 <br> 查询无主文件 <br> 原始设备 <br> 从路径重载 <br> 删除磁盘 <br> 重命名 <br> 重置来宾信息 <br> 设置批注 <br> 设置 <br> 交换文件位置 <br> 切换分支父级 <br> 解锁虚拟机 <br> 升级虚拟机兼容性 |
| **虚拟机 > 来宾操作** | 来宾操作别名修改 <br> 来宾操作别名查询 <br> 来宾操作修改 <br> 来宾操作程序执行 <br> 来宾操作查询    |
| **虚拟机 > 交互** | 回答问题 <br> 在虚拟机上备份操作 <br> 配置 CD 媒体 <br> 配置软盘媒体 <br> 控制台交互 <br> 创建屏幕截图 <br> 对所有磁盘进行碎片整理 <br> 设备连接 <br> 拖放 <br> 通过 VIX API 管理来宾操作系统 <br> 插入 USB HID 扫描代码 <br> 暂停或取消暂停 <br> 执行擦除或收缩操作 <br> 关机 <br> 开机 <br> 在虚拟机上记录会话 <br> 在虚拟机上重播会话 <br> 重置 <br> 恢复容错 <br> 挂起 <br> 挂起容错 <br> 测试故障转移 <br> 测试重启辅助 VM <br> 关闭容错 <br> 启用容错 <br> VMware 工具安装 |
| **虚拟机 > 清单** | 从现有创建 <br> 新建 <br> 移动 <br> 注册 <br> 删除 <br> 注销 |
| **虚拟机 > 预配** | 允许磁盘访问 <br> 允许文件访问 <br> Allow read-only disk access <br> 允许虚拟机下载 <br> 允许虚拟机文件上传 <br> 克隆模板 <br> 克隆虚拟机 <br> 从虚拟机创建模板 <br> 自定义 <br> 部署模板 <br> 标记为模板 <br> 标记为虚拟机 <br> 修改自定义规范 <br> 提升磁盘 <br> 读取自定义规范 |
| **虚拟机 > 服务配置** | 允许通知 <br> 允许轮询全局事件通知 <br> 管理服务配置 <br> 修改服务配置 <br> 查询服务配置 <br> 读取服务配置
| **虚拟机 > 快照管理** | Create snapshot <br> 删除快照 <br> 重命名快照 <br> 还原到快照 |
| **虚拟机 > vSphere 复制** | 配置复制 <br> 管理复制 <br> 监视复制 |
| **vService** | 创建依赖项 <br> 销毁依赖项 <br> 重新配置依赖项配置 <br> 更新依赖项 |
