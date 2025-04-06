---
title: ".NET 配列操作(LINQ)"
emoji: "📄"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Csharp", "dotnet"]
published: true
---

# LINQ とは？

LINQ (Language Integrated Query) は、C# や Visual Basic などに統合されたデータ操作機能。  
SQL などを用いてデータベースにクエリを投げる場面が多いが、LINQ を使うと配列やリスト、XML、データベースなど、さまざまなデータソースに対して同じような書き方で検索や集計ができる。

- 配列や `List<T>` などのコレクションにもクエリを投げられる
- メソッドチェーン形式か SQL 風のクエリ形式で記述できる

# LINQ を使う準備（using System.Linq）

## using ディレクティブ

LINQ を使うには、まず `System.Linq` 名前空間をインポートする必要がある。  
C# のコードファイルの先頭あたりに次のように書く。

```csharp
using System;
using System.Linq;

class Program
{
    static void Main(string[] args)
    {
        // ここでLINQを利用できる
    }
}
```

プロジェクトによっては、最初から `using System.Linq;` が含まれている場合もある。

# メソッド構文とクエリ構文

## メソッド構文（メソッドチェーン構文）

LINQ を利用するときに最もよく使われるのが、拡張メソッドを連続呼び出しする書き方。これを **メソッド構文（メソッドチェーン構文）** と呼ぶ。

```csharp
var result = array
    .Where(x => x > 50)      // 50より大きい要素を抽出
    .Select(x => x * 10)     // 抽出した要素を10倍に変換
    .ToList();               // 結果をリストに変換
```

- `Where(...)` や `Select(...)` は LINQ の拡張メソッド
- `x => x > 50` はラムダ式（無名関数を簡潔に書く構文）

## クエリ構文（SQL ライクな構文）

SQL に似た構文で書けるのも LINQ の特徴。これを　**クエリ構文（クエリ式）**　と呼ぶ。

```csharp
var result =
    from x in array
    where x > 50
    select x * 10;
```

- 上記は同じ処理を SQL 風に表しているだけで、最終的には拡張メソッドに変換される
  実際の開発ではメソッド構文が多く使われる。読みやすさや好みに応じて使い分ける。

# よく使われる LINQ 拡張メソッド

以下に、LINQ の主な拡張メソッドをまとめる。  
`Where` と `Select` は特に使用頻度が高いので、まず覚えておくとよい。

## 4.1 Where / Select

### Where(条件式)

指定した条件を満たす要素だけを取り出すフィルタリング処理。

```csharp
int[] numbers = { 10, 20, 70, 80, 90 };

var filtered = numbers.Where(x => x >= 50);
// 結果: 70, 80, 90
```

### Select(変換式)

要素を別の形に変換するときに使う。  
例：整数を文字列にしたり、オブジェクトの特定フィールドだけ取り出したりできる。

```csharp
int[] numbers = { 1, 2, 3 };

var doubled = numbers.Select(x => x * 2);
// 結果: 2, 4, 6
```

## Sum / Average / Count / Max / Min

### Sum()

合計値を求めるメソッド。

```csharp
int[] scores = { 50, 80, 90, 100 };
int sum = scores.Sum();  // 320
```

### Average()

平均値を求めるメソッド。

```csharp
double avg = scores.Average();  // 80
```

### Count()

要素数を求めるメソッド。  
`Count(...)` には条件を指定してカウントする引数付きのオーバーロードもある。

```csharp
int countAll = scores.Count();               // 全要素 => 4
int countOver80 = scores.Count(x => x > 80); // 80より大きい要素 => 2
```

### Max() / Min()

配列やコレクションの最大値 / 最小値を返すメソッド。

```csharp
int maxVal = scores.Max();  // 100
int minVal = scores.Min();  // 50
```

## OrderBy / OrderByDescending / ThenBy / ThenByDescending

要素を並べ替える（ソート）ときに使う。

```csharp
int[] numbers = { 10, 5, 30, 20 };

var ascending = numbers.OrderBy(x => x);
// 結果: 5, 10, 20, 30 (昇順)

var descending = numbers.OrderByDescending(x => x);
// 結果: 30, 20, 10, 5 (降順)
```

### ThenBy / ThenByDescending

多重ソートをするときに使う。  
「まず年齢でソートし、次に名前でソート」など複数条件がある場合に便利。

```csharp
var multiOrdered = people
    .OrderBy(p => p.Age)
    .ThenBy(p => p.Name);
```

## Distinct / GroupBy など

### Distinct()

重複を除外して一意の要素だけを抽出。

```csharp
int[] numbers = { 10, 20, 10, 30, 20, 40 };
var distinctNumbers = numbers.Distinct();
// 結果: 10, 20, 30, 40
```

### GroupBy(キー)

キーを指定して要素をグルーピングし、グループごとに扱う。

```csharp
var grouped = people.GroupBy(p => p.Department);
foreach (var group in grouped)
{
    Console.WriteLine($"部門: {group.Key}");
    foreach (var person in group)
    {
        Console.WriteLine(person.Name);
    }
}
```

# LINQ の実行タイミングと注意点

## 遅延評価 (Deferred Execution)

LINQ のメソッドチェーンは、すぐに結果が評価されるわけではない。  
列挙（実行）されるタイミングは、以下のような操作が行われたとき。

- `foreach` で要素を取り出すとき
- `.ToList()`, `.ToArray()` などで実際のコレクションに変換するとき

これを**遅延評価 (Deferred Execution)** と呼ぶ。何度も列挙すれば、その都度クエリが再実行される可能性がある。

```csharp
var query = scores.Where(x => x >= 70);
// ここではまだ実行されない

foreach (var item in query)
{
    // このループが始まって初めて Where の処理が走る
}
```

## ToList() / ToArray() で即時評価

遅延評価を防ぎたい場合や、結果を確定させたい場合は、`ToList()` や `ToArray()` などを使って即時評価を行い、コレクション化するとよい。

```csharp
var resultList = scores
    .Where(x => x >= 70)
    .ToList();  // ここでクエリが実行される

// resultList は 実体を持ったリスト
```

## 破壊的メソッド (Destructive Methods) の注意点

C# の LINQ は基本的に要素を「読み取る」処理に特化しており、元のデータソースを直接変更する破壊的操作は行わない。

- `Where`, `Select`, `OrderBy` などの LINQ 拡張メソッドは元の配列やリストを変化させず、新しいシーケンス（`IEnumerable<T>`）を返す
- `List<T>` には `Remove()`, `RemoveAll()`, `Sort()` などの破壊的メソッドが用意されているが、これは LINQ ではなく `List<T>` クラス独自の機能

### 破壊的メソッドと非破壊的メソッドの例

破壊的メソッド: 呼び出すと元のコレクションが変更される  
非破壊的メソッド: 元のコレクションを変更せず、新しいオブジェクトを返す

| メソッド / 構文                     | 破壊的 or 非破壊的 | 説明                                                                                   |
| ----------------------------------- | ------------------ | -------------------------------------------------------------------------------------- |
| **List<T>.Remove(item)**            | 破壊的             | リストから指定要素を削除する。元のリストが書き換わる                                   |
| **List<T>.RemoveAll(predicate)**    | 破壊的             | 条件に合致する要素を削除する。元のリストが書き換わる                                   |
| **List<T>.Sort()**                  | 破壊的             | リストの要素をソートする。元のリストが書き換わる                                       |
| **Enumerable.Where(...)** (LINQ)    | 非破壊的           | 条件に合致する要素だけ抽出して新しいシーケンスを生成する。元のコレクションは変わらない |
| **Enumerable.Select(...)** (LINQ)   | 非破壊的           | 要素を変換して新しいシーケンスを生成する。元のコレクションは変わらない                 |
| **Enumerable.OrderBy(...)** (LINQ)  | 非破壊的           | 並び替えたシーケンスを新たに生成する。元のコレクションは変わらない                     |
| **Enumerable.ToList() / ToArray()** | 非破壊的           | シーケンスを List/配列に変換して返す。元のコレクションは変わらない                     |

#### 実行中にコレクションを変化させる場合のエラー

LINQ の遅延評価中や、`foreach` などでコレクションを列挙している最中に破壊的メソッドを呼ぶと `InvalidOperationException` が発生することがある。  
列挙中にコレクションを変更すると、一貫性が保てなくなるため。

```csharp
var list = new List<int> { 1, 2, 3, 4, 5 };

// こうすると実行時エラーになる可能性がある
foreach (var x in list)
{
    if (x == 3)
    {
        list.Remove(x);  // 列挙中にRemoveを呼ぶ
    }
}
```

要素を削除しながら走査したい場合は、必要な要素だけを先に集めておく、後ろからループする、別のコレクションに集めてまとめて削除するなどの工夫が必要。  
LINQ は元のコレクションを安全に読み取る仕組みなので、破壊的な変更が必要なときは別のロジックを使ったほうがよい場合が多い。
