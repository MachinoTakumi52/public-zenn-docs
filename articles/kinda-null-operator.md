---
title: "null演算子"
emoji: "📄"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["csharp", "typescript"]
published: true
---

# はじめに

プログラミングにおいて`null`は避けて通れない存在である。特に、オブジェクトや変数が未定義であることによって生じるバグは、開発の現場で非常に多く発生する。  
そのため、C#や TypeScript では、`null` を安全に扱うための演算子や型の仕組みが用意されている。
この記事では、以下の `null` 関連機能について、C#と TypeScript の両方で解説する。

# null 合体演算子（??）

**null 合体演算子（`??`）** は、左辺の値が **`null`** または **`undefined`** の場合に、**右辺の値を返す**演算子である。`null` や未定義値に対するフォールバック（代替値）を簡潔に記述できる。

## 使用例

### C#

```csharp
string? name = null;
// nameがnullなら"ゲスト"が代入される
string displayName = name ?? "ゲスト";
```

### TypeScript

```ts
const name: string | null = null;
// nameがnullまたはundefinedなら"ゲスト"
const displayName = name ?? "ゲスト";
```

# null 合体代入演算子（??=）

**null 合体代入演算子（`??=`）** は、変数が **`null`** または **`undefined`** の場合に、**右辺の値を代入する**演算子である。値が未設定のときだけ初期化したい場合に便利である。

## 使用例

### C#

```csharp
string? name = null;

name ??= "ゲスト";
```

### TypeScript

```ts
const name: string | null = null;
// nameがnullなので"ゲスト"が代入される
name ??= "ゲスト";
```

# null 条件演算子（?.）

**null 条件演算子（`?.`）** は、オブジェクトが **`null`** や **`undefined`** でなければ **プロパティやメソッドにアクセス**し、そうでなければ **`undefined`（C#では `null`）を返す** 演算子である。null 参照例外を避けるために使われる。

## 使用例

### C#

```csharp
Person? person = null;
// personがnullなので、nameもnull
string? name = person?.Name;
```

### TypeScript

```ts
const person: Person | null = null;
// personがnullなので、nameはundefined
const name = person?.name;
```

# null 条件インデクサ（?[]）

**配列や辞書、連想配列**などの **インデクサ（[]を使ったアクセス）** に対して、**null チェック** を安全に行うための演算子

## 使用例

### C#

```csharp
Dictionary<string, string>? dict = null;
// dictがnullならvalueもnull
string? value = dict?["key"];
```

### TypeScript

```ts
const dict: { [key: string]: string } | null = null;
// dictがnullなのでvalueはundefined
const value = dict?.["key"];
```

# null 許容抑制演算子（!）

**null 許容抑制演算子（`!`）**　は、開発者が **この変数は `null` ではない** と明示的に断言するための演算子である。型チェック上は `null` の可能性があっても、強制的に `non-null` として扱う。

## 使用例

### C#

```csharp
string? name = null;
// コンパイル時はOK、実行時にnullだと例外
Console.WriteLine(name!);
```

### TypeScript

```ts
let name: string | null = null;
// TypeScript上ではnon-null扱いされる
console.log(name!);
```

# null チェック（if 文/型ガード）

**null チェック**は、変数の値が `null` かどうかを明示的に確認する方法である。C#と TypeScript どちらでも、通常の if 文で `null` を判定し、その中で安全に処理を行うことができる。`null`でない場合は **null 許容抑制演算子**を使用し null 出ないことを示すと良い。

## 使用例

### C#

```csharp
if (name != null)
{
    // null 許容抑制演算子を使用しnull出ないことを示す
    Console.WriteLine(name!.Value.Length);
}
```

### TypeScript

```ts
if (name !== null) {
  // null 許容抑制演算子を使用しnull出ないことを示す
  console.log(name!.length);
}
```

# null 許容型（nullable types）

**null 許容型**とは、変数が **`null` の値を持つ可能性がある型** のことを指す。C#では`?`、TypeScript では`| null`を使って明示的に表現する。

## 使用例

### C#

```csharp
string? name = null;
int? age = null;
```

### TypeScript

```ts
let name: string | null = null;
let age: number | null = null;
```

# まとめ

C#と TypeScript の両言語では、null 安全性を高めるための機能が豊富に用意されている。文法こそ似ているためわかりずらいが、一つ一つに意味があるので少しづつ覚えていくと良いだろう。注意点として、無闇矢鱈に使用するとバグの元になりかねないため、しっかりと理解した上で使用することをおすすめする。
