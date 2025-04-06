---
title: ".NET アプリはどうやって動くの？"
emoji: "📄"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Csharp", "dotnet"]
published: true
---

# はじめに

C# や .NET のアプリを Visual Studio 等 で「▶ 実行」したとき、裏でどんな処理が行われアプリが実行されるのか考えたことはあるだろうか。
この記事では、.NET のビルド〜実行までの流れを初心者にもわかるように解説する。
.NET については、以下の記事にまとめているのでぜひ。

https://zenn.dev/takumi_machino/articles/dotnet-history

## ビルドとは何か

ビルドとは、C# などの**ソースコードをコンピューターが実行できる形に変換する作業**のこと  
ただし、.NET の場合は一度 **中間言語（CIL）** という形式に変換してから、実行時に必要な部分だけ本当の **機械語（ネイティブコード）** に変換する

![](/images/howDotnetwork//swimlane-architecture-framework.png)

## ビルド成果物の場所

プロジェクトをビルドすると、以下のようなディレクトリ構成になる

```
MyApp/
└── bin/
    └── Debug/
        └── net8.0/
            ├── MyApp.dll
            ├── {追加したパッケージ名}.Json.dll
            |　               ︙
            ├── MyApp.pdb
            ├── MyApp.runtimeconfig.json
            └── MyApp.deps.json
```

- **`MyApp.dll`**: 実行対象のアセンブリ（CIL 形式）
- **`MyApp.pdb`**: デバッグ情報
- **`{追加したパッケージ名}.Json.dll`**: 追加したパッケージのアセンブリ
- **`runtimeconfig.json`**: 実行時に必要なランタイムの指定
- **`deps.json`**: 依存関係の情報

この **`MyApp.dll`** を **`dotnet`** コマンドで実行することでアプリが起動する。
「▶ 実行」した時は、裏側で **`dotnet`** コマンドを実行している。

```sh
dotnet bin/Debug/net8.0/MyApp.dll
```

アプリ実行時には、CIL 形式の **`.dll`** が .NET ランタイム（CLR）によって読み込まれる  
必要な部分は JIT（Just-In-Time）コンパイルでネイティブコードに変換されてから実行される

```
[MyApp.dll] --読み込み--> [CLR (.NET ランタイム)]
           --JIT変換--> [ネイティブコードとして実行]
```

## .exe はどこから作られるのか

通常の .NET 6/7/8 のプロジェクトでは **`.dll`** が生成され、**`.exe`** は出力されない  
ただし以下のケースでは **`.exe`** が作成される

- **`.NET Framework`** プロジェクト（Windows 専用）
- 下記のような自己完結型でビルドした場合（Windows 専用）

```sh
dotnet publish -r win-x64 --self-contained -c Release
```

これにより以下のような出力が得られる

```
bin/Release/net6.0/win-x64/publish/
├── MyApp.exe
├── MyApp.dll
├── その他DLLや設定ファイル
```

**`.exe`** 単体で起動可能なアプリケーションになる

# 参考

https://dotnet.microsoft.com/ja-jp/learn/dotnet/what-is-dotnet-framework
