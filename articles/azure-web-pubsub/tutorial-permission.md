---
title: 教程 - 使用 Azure Web PubSub 时向应用程序添加身份验证和权限
description: 演练如何在使用 Azure Web PubSub 时向应用程序添加身份验证和权限。
author: vicancy
ms.author: lianwei
ms.service: azure-web-pubsub
ms.topic: tutorial
ms.date: 08/26/2021
ms.openlocfilehash: e2f2b7ec00250287b71b3882b7078b249262db70
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124769857"
---
# <a name="tutorial-add-authentication-and-permissions-to-your-application-when-using-azure-web-pubsub"></a>教程：使用 Azure Web PubSub 时向应用程序添加身份验证和权限

在[构建聊天应用](./tutorial-build-chat.md)中，你了解了如何使用 WebSocket API 通过 Azure Web PubSub 发送和接收数据。 你可能已注意到，为简单起见，它不需要任何身份验证。 尽管 Azure Web PubSub 要求连接访问令牌，但在该教程中用于生成访问令牌的 `negotiate` API 不需要身份验证。 任何人都可以调用此 API 以获取访问令牌。

在实际应用程序中，你通常希望用户先登录，然后才能使用你的应用程序。 在本教程中，你会了解如何将 Web PubSub 与应用程序的身份验证和授权系统集成，使其更安全。

可以在 [GitHub][code] 上找到本教程的完整代码示例。

在本教程中，你将了解如何：

> [!div class="checklist"]
> * 启用 GitHub 身份验证
> * 向应用程序添加身份验证中间件
> * 向客户端添加权限

## <a name="add-authentication-to-the-chat-room-app"></a>向聊天室应用添加身份验证

本教程重复使用在[构建聊天应用](./tutorial-build-chat.md)中创建的聊天应用程序。 还可以从 [GitHub][chat-js] 克隆聊天应用的完整代码示例。 

在本教程中，你会向聊天应用程序添加身份验证，并将它与 Web PubSub 集成。

首先，向聊天室添加 GitHub 身份验证，以便用户可以使用 GitHub 帐户登录。

1.  安装依赖项。

    ```bash
    npm install --save cookie-parser
    npm install --save express-session
    npm install --save passport
    npm install --save passport-github2
    ```

1.  通过将以下代码添加到 `server.js` 来启用 GitHub 身份验证：

    ```javascript
    const app = express();

    const users = {};
    passport.use(
      new GitHubStrategy({
        clientID: process.argv[3],
        clientSecret: process.argv[4]
      },
      (accessToken, refreshToken, profile, done) => {
        users[profile.id] = profile;
        return done(null, profile);
      }
    ));

    passport.serializeUser((user, done) => {
      done(null, user.id);
    });

    passport.deserializeUser((id, done) => {
      if (users[id]) return done(null, users[id]);
      return done(`invalid user id: ${id}`);
    });

    app.use(cookieParser());
    app.use(session({
      resave: false,
      saveUninitialized: true,
      secret: 'keyboard cat'
    }));
    app.use(passport.initialize());
    app.use(passport.session());
    app.get('/auth/github', passport.authenticate('github', { scope: ['user:email'] }));
    app.get('/auth/github/callback', passport.authenticate('github', { successRedirect: '/' }));
    ```

    上面的代码使用 [Passport.js](http://www.passportjs.org/) 启用 GitHub 身份验证。 下面简单演示了其工作原理：

    1. `/auth/github` 重定向到 github.com 以进行登录。
    1. 登录后，GitHub使用代码将你重定向到 `/auth/github/callback` 以便应用程序完成身份验证。 （若要了解从 GitHub 返回的配置文件如何在服务器上进行验证并保持，请参阅 `passport.use()` 中的验证回调。）
    1. 完成身份验证后，你会重定向到站点的主页 (`/`)。
 
    有关 GitHub OAuth 和 Passport.js 的更多详细信息，请参阅以下文章：

    - [授权 OAuth 应用](https://docs.github.com/en/developers/apps/authorizing-oauth-apps)
    - [Passport.js 文档](http://www.passportjs.org/docs/)

    若要对此进行测试，首先需要创建一个 GitHub OAuth 应用：

    1. 转到 https://www.github.com ，打开配置文件，选择“设置” > “开发人员设置”。
    1. 转到“OAuth 应用”，然后选择“新建 OAuth 应用”。
    1. 填写应用程序名称和主页 URL（该 URL 可以是你喜欢的任何内容），将“授权回调 URL”设置为 `http://localhost:8080/auth/github/callback`。 此 URL 与在服务器中公开的回调 API 相符。
    1. 注册应用程序后，复制客户端 ID 并选择“生成新的客户端密码”。

    然后运行 `node server <connection-string> <client-id> <client-secret>`，并打开 `http://localhost:8080/auth/github`。 你会重定向到 GitHub 进行登录。 登录后，你会重定向到聊天应用程序。

1.  更新聊天室以使用从 GitHub 获取的标识，而不是提示用户输入用户名。

    更新 `public/index.html` 以直接调用 `/negotiate`，而不传入用户 ID。

    ```javascript
    let messages = document.querySelector('#messages');
    let res = await fetch(`/negotiate`);
    if (res.status === 401) {
      let m = document.createElement('p');
      m.innerHTML = 'Not authorized, click <a href="/auth/github">here</a> to login';
      messages.append(m);
      return;
    }
    let data = await res.json();
    let ws = new WebSocket(data.url);
    ```

    用户登录后，请求会自动通过 cookie 携带用户的标识。 因此，我们只需检查 `req` 对象中是否存在用户，然后向 Web PubSub 访问令牌添加用户名：

    ```javascript
    app.get('/negotiate', async (req, res) => {
      if (!req.user || !req.user.username) {
        res.status(401).send('missing user id');
        return;
      }
      let options = {
        userId: req.user.username
      };
      let token = await serviceClient.getAuthenticationToken(options);
      res.json({
        url: token.url
      });
    });
    ```

    现在重新运行服务器，你会在首次打开聊天室时看到“未授权”消息。 选择登录链接进行登录，随后你会看到它如同以前一样工作。

## <a name="work-with-permissions"></a>使用权限

在之前的教程中，你已学习了如何使用 `WebSocket.send()`，通过子协议将消息直接发布到其他客户端。 在实际应用程序中，你可能不希望客户端能够在没有权限控制的情况下发布或订阅任何组。 在此部分中，你将了解如何使用 Web PubSub 的权限系统控制客户端。

在 Web PubSub 中，客户端可以使用子协议执行以下类型的操作：

- 将事件发送到服务器。
- 将消息发布到组。
- 加入（订阅）组。

将事件发送到服务器是客户端的默认操作。 不使用任何协议，因此始终允许执行该操作。 若要发布和订阅组，客户端需要获取权限。 服务器可通过两种方式向客户端授予权限：

- 在连接客户端时指定角色（角色是一种概念，用于表示连接客户端时的初始权限）。
- 连接客户端后，使用 API 向客户端授予权限。

对于用于加入组的权限，客户端在获取权限后仍然需要使用“加入组”消息来加入组。 或者，服务器可以使用 API 将客户端添加到组，即使它没有加入权限。

现在，让我们使用此权限系统向聊天室添加新功能。 你会向聊天室添加一种称为管理员的新用户类型。 你会允许管理员直接从客户端发送系统消息（消息以“[SYSTEM]”开头）。

首先，你需要将系统和用户消息分为两个不同的组，以便可以单独控制其权限。

更改 `server.js` 以将不同的消息发送到不同的组：

```javascript
let handler = new WebPubSubEventHandler(hubName, ['*'], {
  path: '/eventhandler',
  handleConnect: (req, res) => {
    res.success({
      groups: ['system', 'message'],
    });
  },
  onConnected: req => {
    console.log(`${req.context.userId} connected`);
    serviceClient.group('system').sendToAll(`${req.context.userId} joined`, { contentType: 'text/plain' });
  },
  handleUserEvent: (req, res) => {
    if (req.context.eventName === 'message') {
      serviceClient.group('message').sendToAll({
        user: req.context.userId,
        message: req.data
      });
    }
    res.success();
  }
});
```

上面的代码使用 `WebPubSubServiceClient.group().sendToAll()` 将消息发送到组而不是中心。

由于消息现在发送到组，因此你需要将客户端添加到组，以便它们可以继续接收消息。 使用 `handleConnect` 处理程序将客户端添加到组。

> [!Note]
> 当客户端尝试连接到 Web PubSub 时，会触发 `handleConnect`。 在此处理程序中，可以返回组和角色，以便服务可以在建立连接后向组添加连接或授予角色。 该服务还可以使用 `res.fail()` 以拒绝连接。
>

若要触发 `handleConnect`，请转到 Azure 门户中的事件处理程序设置，然后在系统事件选择“连接”。

你还需要更新客户端 HTML，因为现在服务器发送 JSON 消息而不是纯文本：

```javascript
let ws = new WebSocket(data.url, 'json.webpubsub.azure.v1');
ws.onopen = () => console.log('connected');

ws.onmessage = event => {
  let m = document.createElement('p');
  let message = JSON.parse(event.data);
  switch (message.type) {
    case 'message':
      if (message.group === 'system') m.innerText = `[SYSTEM] ${message.data}`;
      else if (message.group === 'message') m.innerText = `[${message.data.user}] ${message.data.message}`;
      break;
  }
  messages.appendChild(m);
};

let message = document.querySelector('#message');
message.addEventListener('keypress', e => {
  if (e.charCode !== 13) return;
  ws.send(JSON.stringify({
    type: 'event',
    event: 'message',
    dataType: 'text',
    data: message.value
  }));
  message.value = '';
});
```

随后更改客户端代码，以便在用户选择“系统消息”时发送到系统组：

```html
<button id="system">system message</button>
...
<script>
  (async function() {
    ...
    let system = document.querySelector('#system');
    system.addEventListener('click', e => {
      ws.send(JSON.stringify({
        type: 'sendToGroup',
        group: 'system',
        dataType: 'text',
        data: message.value
      }));
      message.value = '';
    });
  })();
</script>
```

默认情况下，客户端无权发送到任何组。 更新服务器代码以向管理员用户授予权限（为简单起见，管理员的 ID 作为命令行参数提供）。

```javascript
app.get('/negotiate', async (req, res) => {
  ...
  if (req.user.username === process.argv[5]) options.claims = { role: ['webpubsub.sendToGroup.system'] };
  let token = await serviceClient.getAuthenticationToken(options);
});
```

现在运行 `node server <connection-string> <client-id> <client-secret> <admin-id>`。 你会看到在以 `<admin-id>` 身份登录时，可以将系统消息发送到每个客户端。

但如果以其他用户身份登录，则在选择“系统消息”时不会发生任何情况。 你可能期望服务给出一个错误，让你知道不允许执行该操作。 若要提供此反馈，可以在发布消息时设置 `ackId`。 每当指定 `ackId` 时，Web PubSub 都会返回具有匹配 `ackId` 的消息，以指示操作是否成功。

将发送系统消息的代码更改为以下代码：

```javascript
let ackId = 0;
system.addEventListener('click', e => {
  ws.send(JSON.stringify({
    type: 'sendToGroup',
    group: 'system',
    ackId: ++ackId,
    dataType: 'text',
    data: message.value
    }));
  message.value = '';
});
```

另请更改处理消息的代码以处理 `ack` 消息：

```javascript
ws.onmessage = event => {
  ...
  switch (message.type) {
    case 'ack':
      if (!message.success && message.error.name === 'Forbidden') m.innerText = 'No permission to send system message';
      break;
  }
};
```

现在重新运行服务器并以其他用户身份登录。 你会在尝试发送系统消息时看到错误消息。

可以在 [GitHub][code] 到本教程的完整代码示例。

## <a name="next-steps"></a>后续步骤

本教程提供如何连接到 Web PubSub 服务以及如何使用子协议将消息发布到连接的客户端的基本概念。

若要详细了解如何使用 Web PubSub 服务，请阅读文档中提供的其他教程。

> [!div class="nextstepaction"]
> [了解更多 Azure Web PubSub 示例](https://aka.ms/awps/samples)

[code]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/githubchat/
[chat-js]: https://github.com/Azure/azure-webpubsub/tree/main/samples/javascript/chatapp
