---
title: "NuGetについて"
emoji: "📄"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Csharp", "dotnet"]
published: true
---

# はじめに

C# や .NET 系の開発では、ライブラリを簡単に使えるようにする仕組みが整ってる。
その中心にあるのが **_NuGet（ニューゲットと読む）。_**  
今回は、NuGet について解説していこう。

# NuGet とは

NuGet は **.NET 専用のパッケージ管理システム**。  
**npm が JavaScript** にあるように、**.NET には NuGet** がある。
パッケージは、**nuget.org**という NuGet パッケージの公式レジストリサイトに配置されている。

パッケージをインストールするだけで中身を自由に使える。
中身は **DLL や設定ファイル**だったりする。
また、自作の便利なクラスや関数をまとめて「NuGet パッケージ」として公開できる。

パッケージにはバージョンがあり、アップデートもできるし、特定のバージョンを指定して使うこともできる。
つまり、**依存関係の管理とかも勝手にやってくれる**便利なヤツ。

# パッケージのインストール方法

パッケージをインストールする際に、依存関係等を調べる必要がある。
開発する `.NET`のバージョンが対応できるか等々。
下記サイトに NuGet に上がっているパッケージ情報が記載されている。
https://www.nuget.org/

### Visual Studio

Visual Studio では GUI で簡単に入れられる。  
プロジェクトを右クリック → 「NuGet パッケージの管理」から欲しいパッケージ名で検索して、「インストール」ボタンを押すだけ。

### VS Code

VS Code は GUI がないから、基本はコマンドを使う。  
`.csproj` を開いてるプロジェクトで、以下のコマンドを実行する。

```bash
dotnet add package パッケージ名
```

たとえば Dapper を入れたいなら：

```bash
dotnet add package Dapper
```

これだけで OK。`.csproj` に自動で書き込まれる。

### dotnet コマンド

VS Code 以外でも、CLI（コマンドライン）から操作する場合は基本的に `dotnet` コマンドを使う。

- パッケージを追加：  
  `dotnet add package パッケージ名`

- パッケージ一覧を見る：  
  `dotnet list package`

- アップデート：  
  `dotnet outdated`（※別途ツールのインストールが必要）

# おまけ　 NuGet の仕組み

NuGet を使うと、裏ではこんな感じの流れになってる：

1.  **検索＆取得**  
    下記のコマンドでパッケージ名を指定すると、NuGet サーバー（公式は `https://api.nuget.org`）に問い合わせがいく。

    ```bash
    dotnet add package Dapper
    ```

2.  **ダウンロード**  
    対象のパッケージが見つかると、`.nupkg` という ZIP 形式のファイルがダウンロードされる。

3.  **展開＆参照**  
     `.nupkg` の中には DLL やライセンスファイルなどが入ってる。プロジェクトの `obj` や ローカルキャッシュの`packages` フォルダなどに展開されて、プロジェクトの参照に組み込まれる。
    ちなみに、一度インストールしたパッケージはローカルキャッシュ（`~/.nuget/packages`）に保存されるから、次からはネットに取りに行かずにローカルから使える。

4.  **管理**  
     `.csproj` ファイルには、追加されたパッケージ情報が `<PackageReference>` タグで記録される。  
     依存してる他のライブラリ（依存関係）も一緒に追加される。

    ```xml
    <ItemGroup>
     <PackageReference Include="Dapper" Version="2.1.66" />
    </ItemGroup>
    ```

5.  **ビルド**
    `.csproj` ファイルから該当するパッケージの`.dll`がプロジェクトの出力先（`bin/Debug/...`）にコピーされる

        ```
        bin/Debug/net8.0/
        ├── MyApp.dll
        ├── Dapper.dll ← NuGet パッケージからコピーされた DLL
        ├──     ︙
        ```

        `.NET` のビルドについても書いているのでぜひ。

https://zenn.dev/takumi_machino/articles/how-dotnet-work
