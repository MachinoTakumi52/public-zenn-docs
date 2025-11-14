---
title: "アロー関数と関数宣言のスコープの違い"
emoji: "🎯"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["javascript", "typescript"]
published: true
---

# はじめに

TypeScript（および JavaScript）でよく使われる「アロー関数（arrow function）」と「関数宣言（function）」は、どちらも関数を定義するための構文であるが、内部的な「スコープ（this の扱い）」が異なる。

本記事では、両者の違いを具体例とともに解説する。

# スコープとは

スコープ（scope）とは、変数や関数がどこまで有効であるかを示す範囲のことである。

```typescript
function test() {
  const message = "Hello";
  console.log(message); // ここでは使用できる
}

console.log(message); // エラー（スコープ外）
```

この場合、`message` は `test()` 関数内でのみ有効である。
このようにスコープは「関数の中だけ」や「ブロックの中だけ」など、変数の有効範囲を決めるルールである。

# 関数宣言とアロー関数の違い

## 関数宣言（function）

```typescript
function sayHello() {
  console.log(this);
}
```

`function` は「呼び出し方」によって `this` が変化する。
これは「動的スコープ（dynamic scope）」的な挙動である。

```typescript
const obj = { sayHello };

obj.sayHello(); // this → obj
sayHello(); // this → undefined（またはwindow）
```

呼び出した対象が「this」として扱われる点が特徴である。

## アロー関数（=>）

```typescript
const sayHello = () => {
  console.log(this);
};
```

アロー関数は「定義された場所の this をそのまま使う」という性質を持つ。
これは「レキシカルスコープ（lexical scope）」と呼ばれる。

```typescript
const obj = {
  name: "Machino",
  sayHello: () => {
    console.log(this.name);
  },
};

obj.sayHello(); // → undefined
```

アロー関数は「どこで定義されたか」によって `this` が決まるため、
`obj` 自身ではなく外側（グローバル）のスコープを参照する。

## 比較例

```typescript
const person = {
  name: "Machino",

  // 通常の関数
  normal: function () {
    console.log("normal:", this.name);
  },

  // アロー関数
  arrow: () => {
    console.log("arrow:", this.name);
  },
};

person.normal(); // → "normal: Machino"
person.arrow(); // → "arrow: undefined"
```

### 解説

- `normal` は呼び出されたときに `this = person` となる
- `arrow` は定義された時点のスコープの `this` を参照するため、`person` ではなく外側のスコープを見てしまう

## クラス内での違い

クラス内でもこの違いは重要である。

```typescript
class Counter {
  count = 0;

  start() {
    setInterval(function () {
      this.count++; // thisはundefinedとなる
    }, 1000);
  }
}

new Counter().start();
```

`function` では、`setInterval` に渡した関数の `this` が変化してしまうため、
`this.count` にアクセスできない。

アロー関数を使うと次のように解決できる。

```typescript
class Counter {
  count = 0;

  start() {
    setInterval(() => {
      this.count++; // thisはCounterインスタンスを指す
      console.log(this.count);
    }, 1000);
  }
}

new Counter().start();
```

アロー関数は外側スコープの `this` を保持しているため、
クラスのメソッド内でのコールバックなどに適している。

# まとめ

| 項目       | 関数宣言 (`function`)      | アロー関数 (`=>`)           |
| ---------- | -------------------------- | --------------------------- |
| this       | 呼び出し時に決まる（動的） | 定義時に決まる（静的）      |
| スコープ   | 呼び出し方によって変わる   | 外側スコープを引き継ぐ      |
| arguments  | 利用可能                   | 利用不可（Rest 引数を使用） |
| new 演算子 | 使用可能                   | 使用不可                    |
| 主な用途   | メソッド、イベントハンドラ | コールバック、クラス内関数  |
