---
title: 为已启用 Arc 的超大规模 PostgreSQL 服务器组获取连接终结点并形成连接字符串
titleSuffix: Azure Arc enabled data services
description: 为已启用 Arc 的超大规模 PostgreSQL 服务器组获取连接终结点并形成连接字符串
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: de7d23689ae984ea0abece5edb03cf8a0c3a9be1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670335"
---
# <a name="get-connection-endpoints-and-form-connection-strings-for-your-arc-enabled-postgresql-hyperscale-server-group"></a>为已启用 Arc 的超大规模 PostgreSQL 服务器组获取连接终结点并形成连接字符串

本文介绍如何为服务器组检索连接终结点，以及如何形成将用于应用程序和/或工具的连接字符串。


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-connection-end-points"></a>获取连接终结点：

### <a name="from-cli-with-azdata"></a>使用 azdata 从 CLI 获取
#### <a name="1-connect-to-your-arc-data-controller"></a>1.连接到 Arc 数据控制器：
- 如果已在 Arc 数据控制器的主机上打开会话，请运行以下命令：
```console
azdata login
```

- 如果没有在 Arc 数据控制器的主机上打开会话，请运行以下命令： 
```console
azdata login --endpoint https://<external IP address of host/data controller>:30080
```

#### <a name="2-show-the-connection-endpoints"></a>2.显示连接终结点
运行以下命令：
```console
azdata arc postgres endpoint list -n <server group name>
```
例如：
```console
azdata arc postgres endpoint list -n postgres01
```

它会显示终结点列表：用于连接应用程序的 PostgreSQL 终结点，以及用于日志分析和监视的数据库、Kibana 和 Grafana 终结点。 例如： 
```console
Arc
 ===================================================================================================================
 Postgres01 Instance
 -------------------------------------------------------------------------------------------------------------------
 Description           Endpoint

 PostgreSQL Instance   postgresql://postgres:<replace with password>@12.345.567.89:5432
 Log Search Dashboard  https://89.345.712.81:30777/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))
 Metrics Dashboard     https://89.345.712.81:30777/grafana/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01

```
使用这些终结点进行以下操作：
- 形成连接字符串并连接到客户端工具或应用程序
- 从浏览器访问 Grafana 和 Kibana 仪表板

例如，可以使用名为 PostgreSQL 实例的终结点通过 psql 连接到服务器组。 例如：
```console
psql postgresql://postgres:MyPassworkd@12.345.567.89:5432
psql (10.14 (Ubuntu 10.14-0ubuntu0.18.04.1), server 12.4 (Ubuntu 12.4-1.pgdg16.04+1))
WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=#
```
> [!NOTE]
>
> - 名为“PostgreSQL 实例”的终结点中指示的 postgres 用户的密码是在部署服务器组时选择的密码 。
> - 关于 azdata：与连接关联的租约持续大约 10 个小时。 此期限过后，需要重新进行连接。 如果租约已过期，尝试通过 azdata（azdata login 除外）执行命令时，会看到以下错误消息：错误: (401)
> 原因: 未授权
> HTTP 响应标头: HTTPHeaderDict({'Date': 'Sun, 06 Sep 2020 16:58:38 GMT', 'Content-Length': '0', 'WWW-Authenticate': '
> 基础领域=“需要登录凭据”，持有者错误="invalid_token"，error_description=“令牌已过期”'})_ 发生此情况时，需要使用 azdate 重新进行连接，如上所述   。

## <a name="from-cli-with-kubectl"></a>使用 kubectl 从 CLI 获取
- 如果服务器组为 Postgres 版本 12（默认），则使用以下命令：
```console
kubectl get postgresql-12/<server group name> -n <namespace name>
```
- 如果服务器组为 Postgres 版本 11，则使用以下命令：
```console
kubectl get postgresql-11/<server group name> -n <namespace name>
```

这些命令将生成如下所示的输出。 你可以使用该信息来形成连接字符串：
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   3/3          123.456.789.4:31066      5d20h
``` 


## <a name="form-connection-strings"></a>形成连接字符串：
为服务器组使用下面的连接字符串模板表。 然后，可以根据需要进行复制/粘贴和自定义：

### <a name="adonet"></a>ADO.NET

```ado.net
Server=192.168.1.121;Database=postgres;Port=24276;User Id=postgres;Password={your_password_here};Ssl Mode=Require;`
```

### <a name="c-libpq"></a>C++ (libpq)

```cpp
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="jdbc"></a>JDBC

```jdbc
jdbc:postgresql://192.168.1.121:24276/postgres?user=postgres&password={your_password_here}&sslmode=require
```

### <a name="nodejs"></a>Node.js

```node.js
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="php"></a>PHP

```php
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="psql"></a>psql

```psql
psql "host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require"
```

### <a name="python"></a>Python

```python
dbname='postgres' user='postgres' host='192.168.1.121' password='{your_password_here}' port='24276' sslmode='true'
```

### <a name="ruby"></a>Ruby

```ruby
host=192.168.1.121; dbname=postgres user=postgres password={your_password_here} port=24276 sslmode=require
```

## <a name="next-steps"></a>后续步骤
- 阅读了解如何[横向扩展（添加工作器节点）](scale-out-postgresql-hyperscale-server-group.md)服务器组
- 阅读了解如何[纵向扩展或缩减（增加/减少内存/vcore）](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)服务器组


