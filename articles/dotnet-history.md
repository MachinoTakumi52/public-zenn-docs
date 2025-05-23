---
title: ".NETとは"
emoji: "📄"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Csharp", "dotnet"]
published: true
---

# はじめに

現在、`.NETは9.0.2`まで[リリース](https://github.com/dotnet/core/blob/main/release-notes/9.0/9.0.2/9.0.2.md?WT.mc_id=dotnet-35129-website)されており、豊富な機能が提供されているため、さまざまなアプリケーションを作成できるようになった。
しかし、ネットで検索すると`.NET`と付く言葉がたくさんあり、初学者は混乱するだろうと思う。自身も最初は、混乱し理解をあまりせず曖昧な認識のまま使用していた。
そんな方が少しでも減ればいいなと思い、**`.NET`の歴史**、**そもそも`.NET`って何？** といった所をまとめていく。

# **`.NET`って何？**

端的に述べると Microsoft が開発した**オープンソース アプリケーション プラットフォーム**。
読んで字のごとく、**オープンソース**（ソースコードを誰でも自由に無償で閲覧・利用・改変・再配布できるもの）のアプリケーションプラットフォームである。

では、**プラットフォーム**とは何なのか。
[辞書](https://www.weblio.jp/content/%E3%83%97%E3%83%A9%E3%83%83%E3%83%88%E3%83%95%E3%82%A9%E3%83%BC%E3%83%A0#goog_rewarded:~:text=%E3%81%A3%E3%81%9F%E3%80%82-,IT%E7%94%A8%E8%AA%9E%E3%81%AB%E3%81%8A%E3%81%91%E3%82%8B%E3%83%97%E3%83%A9%E3%83%83%E3%83%88%E3%83%95%E3%82%A9%E3%83%BC%E3%83%A0,-IT%E7%94%A8%E8%AA%9E%E3%81%AB%E3%81%8A%E3%81%91%E3%82%8B)には、以下のように定義されている。

> ソフトウェアを動かすための土台、基盤を指す

辞書の意味から考えてみるとプラットフォームとは、アプリケーションを動かすための**エンジン**的なものと捉えることができる。

**つまり、無償で利用できるアプリケーションを動かすためのエンジン的なもの**である。
しかし実際は、開発がしやすいように**クラスライブラリ**や**開発ツール**なども含んでいる。
そのため、`.NET`とは 「**アプリケーションを動かすためのエンジン** + **開発者を支えるライブラリやツール**」だ。

# ランタイム

アプリケーションを動かすためのエンジンのことを**ランタイム**と呼ぶ。
`.NET`のランタイムは、**共通言語ランタイム(CLR)**　と呼ばれ、`C#`や`VB`などで書かれたプログラムを **中間言語 (IL)** から最終的に**機械語(ネイティブコード)** に変換し、メモリ管理やセキュリティチェックなどを行いながら実行している。
詳しく述べると **JIT (Just-In-Time) コンパイル方式**をとっており、実行時に必要な箇所だけ **IL → ネイティブコード (機械語)** に変換している。また、不要になったオブジェクトを自動で検知し、メモリを解放してあげる**ガベージコレクション**などで最適化を行なっている。

要するに、以下のような流れでランタイムを使用しアプリを実行している。

1. **ソースコード (C#など)**
   開発者が書いたコード（.cs ファイルなど）。

2. **コンパイル → 中間言語(IL)**
   C#コンパイラによって、ソースコードは IL という形式へ変換される。
   この段階で出力されるのが、.dll や.exe のアセンブリ。

3. **CLR (ランタイム) 上で実行**
   ロード: CLR がアセンブリを読み込む。
   JIT コンパイル: 必要になった箇所から順次 IL → ネイティブコード に変換する。

# 開発者を支えるライブラリやツール

たくさんの機能があるがその中から抜粋して記述した。

## ライブラリ

- **BCL（Base Class Library）**
  `system`系は全て、**BCL** と呼ばれる`.NET` が標準で提供する基本的なクラスライブラリの集合体である。

- **NuGet パッケージ**
  **NuGet** は `.NET` 向けのパッケージ管理システムで、**BCL** に含まれない追加の機能やサードパーティライブラリを簡単に導入できる。

## フレームワーク

- **ASP.NET Core**
  **Web サイト**や **Web アプリケーション**、**REST API** など、Web ベースのアプリを開発するために使用する。
  `OS` は `Windows`, `Linux`, `macOS` といった主要なサーバー環境すべてに対応している。

- **Windows Forms**
  Windows 専用のデスクトップアプリを開発するために使用する。
  Windows OS でしか動かない。

- **.NET MAUI**
  **デスクトップ（Windows、macOS）** や **モバイル（iOS、Android）** 向けの ネイティブアプリ を 一つの C#プロジェクトで開発することができる。
  　`Windows`,`macOS`,`iOS`,`Android`で動く。

## dotnet CLI

コマンドラインツールでありプロジェクトの作成やビルド、実行等を行なってくれる。
`visual studio`で実行ボタンを押下すると裏では**dotnet CLI**を介して実行している。

# ランタイムと SDK

余談ではあるが Microsoft の[.NET ダウンロードサイト](https://dotnet.microsoft.com/ja-jp/download/dotnet/9.0)を閲覧すると、`ランタイム`と`SDK`というものがダウンロードできる。
それぞれの違いとしては簡単に述べると以下だ。

ランタイム：OS 上でアプリを動かすだけに必要なコンポーネント群
SDK：アプリを“開発”または“ビルド”するために必要なツール

# 歴史

### 大雑把な年表

1. **.NET Framework (2002〜)**

   - Windows 専用として長年進化
   - 4.8 を最後に大幅な新機能追加は行われず、保守メンテナンス中心

2. **.NET Core (2016〜)**

   - クロスプラットフォーム対応を目指して登場
   - オープンソース化や CLI ツールの導入、軽量高速を強みに急成長

3. **.NET 5 (2020〜)**

   - .NET Framework / Core / Mono / Xamarin を統合する “One .NET” ビジョンを打ち出し、「.NET」 に名称を一本化
   - LTS リリース (.NET 6 / 8 …) を軸に年 1 回のメジャーアップデートが続いている

4. **クロスプラットフォーム & 多様なアプリ形態**
   - Web, デスクトップ, モバイル, ゲーム (Unity), IoT, クラウドなど、あらゆる分野で C# / .NET を使える世界へ
   - .NET MAUI でマルチプラットフォーム UI、Blazor でブラウザ上の WebAssembly 実行など、アプリ開発手法が多様化

## 1. .NET Framework 時代 (Windows 専用)

### 2002 年: .NET Framework 1.0

- **正式リリース**: 2002 年 2 月（Visual Studio .NET と同時期）
- **概要**
  - Windows 上で動作するアプリケーション開発のための新しいプラットフォームとして登場
  - CLR（Common Language Runtime）のコンセプトや、C# / VB.NET といった新言語を導入
  - Windows Forms / ASP.NET (Web Forms) / ADO.NET などが整備された

### 2003 年: .NET Framework 1.1

- **Visual Studio .NET 2003** と共にリリース
- バグ修正やパフォーマンス改善などマイナーアップデートが中心

### 2005 年: .NET Framework 2.0

- **C# 2.0** や **Generics**（ジェネリック型）、**匿名メソッド**など、言語仕様が大幅に拡張
- **Visual Studio 2005** と同時期に登場
- 大規模開発での利便性が向上し、業務システムにも広く普及

### 2006 年: .NET Framework 3.0

- **WPF (Windows Presentation Foundation)**、**WCF (Windows Communication Foundation)**、**WF (Windows Workflow Foundation)**、**CardSpace** などが追加
- 「.NET Framework 2.0 + 新 API 群」をまとめた位置づけ

### 2007 年: .NET Framework 3.5

- **LINQ (Language Integrated Query)** が登場し、データ操作を統合的に扱えるように
- ASP.NET MVC の初期プレビュー版もこの頃に紹介され、Web 開発の新スタイルが普及し始める

### 2010 年: .NET Framework 4.0

- **C# 4.0**（`dynamic` 型や COM 相互運用の強化など）、並列プログラミング (TPL: Task Parallel Library) が強化
- **Visual Studio 2010** と同時に登場

### 2012 年以降: .NET Framework 4.x 系列

- **4.5 / 4.6 / 4.7 / 4.8** と細かくアップデート
- 2012 年に登場した .NET Framework 4.5 からは、**async/await** が導入され非同期プログラミングが簡単に
- 2019 年に登場した .NET Framework 4.8 が実質的に「最後のメジャーリリース」
- 今も Windows 向けに保守的なアップデートが継続されているが、機能追加は限定的

## 2. クロスプラットフォームへの挑戦 〜 Mono / Xamarin

### Mono プロジェクト (2001 年〜)

- **概要**
  - オープンソースコミュニティが中心となり、.NET Framework を Linux / macOS などでも動かせるようにしたプロジェクト
  - 公式ではないが「クロスプラットフォームで C# を使いたい」という需要に応える形で普及

### Xamarin (2011 年〜)

- Mono をベースにしたスマホ (iOS / Android) アプリ開発技術として発展
- 後にマイクロソフトが Xamarin 社を買収し、.NET の正式な一部に取り込まれていく

## 3. .NET Core の登場 (クロスプラットフォーム化)

### 2016 年: .NET Core 1.0

- **目標**
  - Windows に限らず、Linux や macOS でも動く軽量かつモジュール化された .NET ランタイムを提供
  - オープンソースとして開発し、コミュニティからの貢献を受けやすい体制を整える
- **ASP.NET Core 1.0** も同時リリースされ、軽量高速な Web フレームワークとして注目を集める

### 2017 年〜2019 年: .NET Core 2.x / 3.x

- **.NET Core 2.0 / 2.1**
  - 2.0 では **.NET Standard** を活用し、フレームワーク間の互換性が向上
  - 2.1 が初めての **LTS (Long Term Support)** 版となり、企業利用が本格化
- **.NET Core 3.0 / 3.1 (2019 年)**
  - Windows Forms / WPF の移植版が .NET Core に追加され、デスクトップアプリもクロスプラットフォームに移行可能に
  - 3.1 は LTS 版として安定し、開発者が本格的に .NET Core に移行する流れが加速

## 4. .NET 5 以降: 統合と発展

### 2020 年: .NET 5

- **“One .NET” のビジョン**
  - 従来の .NET Framework / .NET Core / Mono / Xamarin などを統合し、一つのプラットフォームとして扱う
  - フレームワーク名が **“.NET 5”** に変わり、バージョン番号も一本化
- **クロスプラットフォーム対応の強化**
  - Windows / Linux / macOS、さらにモバイルの Xamarin 技術も含まれる構想

### 2021 年: .NET 6 (LTS)

- **本格的な統合**
  - **.NET MAUI (Multi-platform App UI)** のプレビュー版が出始め、Xamarin.Forms の後継としてモバイル/デスクトップ統合 UI フレームワークが提案される
- **パフォーマンス最適化**
  - さらに高速化とメモリ効率向上が行われ、大規模 Web サービスやクラウド環境でも採用が拡大

### 2022 年: .NET 7

- **最新の C# 機能**（C# 11 など）が導入
- .NET MAUI が正式版として利用可能に
- 継続的なパフォーマンス向上やランタイムの最適化、AOT（Ahead-Of-Time コンパイル）の拡充

### 2023 年: .NET 8 (LTS)

- **.NET 8** が LTS 版として登場
- **最新の C# 機能**（C# 12 など）が導入
- **ネイティブ AOT (Ahead-Of-Time)** 強化
- パフォーマンスの最適化

### 2024 年: .NET 9

- **最新の C# 機能**（C# 13 など）が導入
- **Server GC** 改善
- **JSON / LINQ** の最適化

### 2025 年: .NET 10(予定)

- **.NET 10**は、LTS 版として登場予定
- **最新の C# 機能**（C# 14 など）が導入？
