---
title: "websocketとは"
emoji: "🪢"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["websocket"]
published: true
---

# はじめに

近年の Web アプリケーションでは、チャット、オンラインゲーム、コラボレーションツール、通知配信など、リアルタイム性 を求められる場面が増えている。従来の HTTP 通信は「リクエストを送る → レスポンスを受け取る」という一方向の問い合わせ型であり、双方向のリアルタイム通信を実現するには WebSocket を使用する必要がある。
WebSocket は、一度だけ HTTP で「プロトコルのアップグレード」を行い、その後はクライアントとサーバーが 双方向にデータを送り合える 常時接続の通信路を確立する仕組みを提供する。
本記事では、WebSocket の仕組みと通信の流れを解説し、TypeScript を使った具体的な実装例 も紹介する。

 双方向通信については、以下の記事にまとめてあるので興味がある方はぜひ。
https://zenn.dev/takumi_machino/articles/bidirectional-communication 

# 1. WebSocket は何を解決するプロトコルなのか

従来の Web は、HTTP による一問一答型の通信モデルを持つ。
このため、リアルタイム性が求められる場面では次のような課題があった。

- サーバーから即時通知を送れない
- クライアントは最新状態を知るために **Polling（定期的な問い合わせ）** を行う必要がある
- Long Polling でも疑似リアルタイムであり本質的に双方向ではない
- リクエストごとにヘッダーのオーバーヘッドが大きい

これらを根本的に解決するために、**WebSocket** は「常時接続」かつ「双方向」通信というモデルを提供している。

# 2. WebSocket の通信は 2 段階で構成される

WebSocket の通信は次の 2 ステップで構成される。

1. **HTTP を利用したオープニングハンドシェイク**
2. **WebSocket フレームによる双方向通信**

# 3. オープニングハンドシェイクの仕組み

### 3.1 クライアント → サーバー：Upgrade リクエスト

クライアント（ブラウザやアプリ）は、まず **通常の HTTP GET** を送信する。
ヘッダーには「WebSocket にアップグレードしたい」という情報を含める。

例：

```http
GET /chat HTTP/1.1
Host: example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13
Origin: https://example.com
Sec-WebSocket-Protocol: chat, superchat
```

重要なポイントは次のとおりである。

- **Upgrade: websocket**
  プロトコルを WebSocket に切り替えたいという宣言。

- **Connection: Upgrade**
  Upgrade ヘッダーを有効にする。

- **Sec-WebSocket-Version: 13**
  現行の WebSocket プロトコルバージョン。

- **Sec-WebSocket-Key**
  クライアントが生成したランダムな鍵。
  この鍵を使ってサーバー側が「正当な WebSocket 応答」であることを証明する。

- **Sec-WebSocket-Protocol**
  上位プロトコル（サブプロトコル）を要求するためのヘッダー。

### 3.2 サーバー → クライアント：101 Switching Protocols

サーバーが WebSocket 接続を受理すると、次のレスポンスを返す。

```http
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
Sec-WebSocket-Protocol: chat
```

ここで重要なのが **Sec-WebSocket-Accept** である。

### 3.3 Sec-WebSocket-Key / Accept の仕組み

サーバーは、クライアントが送ってきた `Sec-WebSocket-Key` に対して特定の文字列を付け足し、SHA-1 → Base64 で加工する。

クライアント側は同じ計算を行い、値が一致すれば **正しい WebSocket 応答である** と判断する。

### 3.4 ハンドシェイクが終わった後

`101` が返り、Sec-WebSocket-Accept の検証に成功すると、
通信は HTTP を離れ **WebSocket フレーム通信へ遷移** する。
以後は同じ TCP コネクションを維持しながら双方向通信を行う。

# 4. WebSocket フレームの仕組み

WebSocket はハンドシェイク後、**フレーム（frame）** と呼ばれる単位でデータを送受信する。

フレームはおおまかに次の構成を持つ。

1. ヘッダー（FIN ビット、OPCODE、MASK など）
2. ペイロード（実際のデータ）

詳しい構成については、[RFC](https://triple-underscore.github.io/RFC6455-ja.html#section-11.8:~:text=%E3%81%A6%E3%82%88%E3%81%84%E3%80%82-,5.2.%20%E3%83%95%E3%83%AC%E3%83%BC%E3%83%A0%E6%B3%95%E3%83%97%E3%83%AD%E3%83%88%E3%82%B3%E3%83%AB%E3%81%AE%E5%9F%BA%E5%BA%95%E9%83%A8,-%E3%83%87%E3%83%BC%E3%82%BF%E8%BB%A2%E9%80%81%E3%83%91%E3%83%BC%E3%83%88)を参照してほしい。

# 5. WebSocket のライフサイクル

WebSocket 接続は次のように進行する。

1. HTTP GET による Upgrade リクエスト
2. 101 Switching Protocols
3. WebSocket フレーム通信への切り替え
4. テキスト・バイナリフレームの双方向通信
5. Ping/Pong による死活監視
6. Close フレームで接続終了

# 6. TypeScript で WebSocket を実装する

### 6.1 クライアント（ブラウザ）

```ts
type MessageType = "chat" | "system";

interface ChatMessage {
  type: MessageType;
  text: string;
  sentAt: string;
}

const socket = new WebSocket("wss://example.com/chat");

socket.addEventListener("open", () => {
  console.log("[WebSocket] connected");

  const hello: ChatMessage = {
    type: "system",
    text: "Hello from client",
    sentAt: new Date().toISOString(),
  };

  socket.send(JSON.stringify(hello));
});

socket.addEventListener("message", (event) => {
  const data = event.data;
  const msg = JSON.parse(data) as ChatMessage;
  console.log("[WebSocket] message:", msg);
});

socket.addEventListener("close", (event) => {
  console.log("[WebSocket] closed:", event.code, event.reason);
});
```

### 6.2 サーバー（Node.js + ws）

### インストール

```bash
npm install ws
npm install -D @types/ws
```

### 実装例

```ts
import { WebSocketServer, WebSocket } from "ws";

interface ChatMessage {
  type: "chat" | "system";
  text: string;
  sentAt: string;
}

const wss = new WebSocketServer({ port: 8080 });

wss.on("connection", (socket: WebSocket) => {
  console.log("[Server] client connected");

  const welcome: ChatMessage = {
    type: "system",
    text: "Welcome!",
    sentAt: new Date().toISOString(),
  };
  socket.send(JSON.stringify(welcome));

  socket.on("message", (data) => {
    const msg = JSON.parse(data.toString()) as ChatMessage;
    console.log("[Server] received:", msg);

    wss.clients.forEach((client) => {
      if (client.readyState === WebSocket.OPEN) {
        client.send(JSON.stringify(msg));
      }
    });
  });
});
```

# 参考資料

https://triple-underscore.github.io/RFC6455-ja.html
https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Sec-WebSocket-Accept
