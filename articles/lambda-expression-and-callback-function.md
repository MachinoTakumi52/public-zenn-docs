---
title: "ラムダ式とコールバック関数"
emoji: "📄"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["typescript"]
published: true
---

# はじめに

業務でソースコードを読む際によく登場するのが、「関数を関数に渡す」「その場で関数を書く」といった考え方であり、中でも **ラムダ式** や **コールバック関数** は、非常によく使われる重要な概念である。
本記事では、TypeScript を用いてこれらの基礎について丁寧に解説する。

# ラムダ式とは

MDN によるとラムダ式は以下のように定義されている。

> アロー関数式は、従来の関数式の簡潔な代替構文

つまり**無名関数（名前のない関数）を簡潔に書くための構文**ということ。
MDN に記載されている**アロー関数** とは、**JavaScript / TypeScript におけるラムダ式の構文（記法）** である。

## 基本の構文（アロー関数）

```ts
const sayHello = (name: string): void => {
  console.log(`こんにちは、${name}さん！`);
};

sayHello("はなこ");
```

## より簡潔な書き方（1 行の式の場合）

```ts
const square = (x: number): number => x * x;
console.log(square(5)); // 出力: 25
```

このように、ラムダ式は**その場限りのシンプルな関数**を定義したいときに非常に便利である。

# コールバック関数とは

MDN によるとコールバック関数は以下のように定義されている。

> **コールバック関数（callback function）** とは、**他の関数に引数として渡され、その中で呼び出される関数** のことである。

つまり**関数の引数として渡され、後から呼び出される関数**ということだ。

## TypeScript による例

`callback: () => void` は「引数なし・戻り値なしの関数型」である。
`greet`関数の中で引数に渡した関数を実行している。

```ts
const greet = (name: string, callback: () => void): void => {
  console.log(`こんにちは、${name}さん！`);
  callback(); // コールバック関数を実行
};

const done = (): void => {
  console.log("ご挨拶が完了した。");
};

greet("たろう", done());

// --------- 出力 ---------
// こんにちは、たろうさん！
// ご挨拶が完了した。
```

# ラムダ式 × コールバック関数（map 関数の例）

TypeScript では、**ラムダ式をコールバック関数として使用する**場面が多く存在する。  
その代表例が、配列の`map()`関数である。
`map()`関数は、**配列の各要素に関数を適用し、結果を新しい配列として返すメソッド**である。

### 例：配列の各要素を 2 倍にして新しい配列を作る

```ts
const numbers: number[] = [1, 2, 3, 4];

// ラムダ式を使ったコールバック関数を map に渡す
const doubled = numbers.map((n: number): number => n * 2);

console.log(doubled); // 出力: [2, 4, 6, 8]
```

ここで `(n: number): number => n * 2` は、**ラムダ式**であり、  
同時に `map()` に渡される**コールバック関数**でもある。

## まとめ

| 用語             | 定義                                                 |
| ---------------- | ---------------------------------------------------- |
| コールバック関数 | 他の関数に**引数として渡され、後で呼び出される関数** |
| ラムダ式         | **無名関数（名前のない関数）を簡潔に書くための構文** |

# 参考資料

https://developer.mozilla.org/ja/docs/Glossary/Callback_function
https://developer.mozilla.org/ja/docs/Web/JavaScript/Reference/Functions/Arrow_functions
