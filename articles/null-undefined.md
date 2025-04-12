---
title: "JS/TSにおける null と undefined"
emoji: "📄"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["TypeScript", "JavaScript"]
published: true
---

# はじめに

TypeScript や JavaScript の学習を進めると、早い段階で「`null`」と「`undefined`」という 2 つの特殊な値に出会うこととなる。  
これらは一見似ているようでありながら、意味や使われ方には明確な違いが存在する。  
今回は、これらの違いについてみていこう。

# `undefined`とは何か

`undefined` は、「**値がまだ定義されていない状態**」を示す。これは、JavaScript および TypeScript において、変数が宣言されたが値が代入されていない場合や、関数が明示的な戻り値を返さない場合などに自動的に割り当てられる。

```ts
const age;
console.log(age); // undefined
```

関数が値を返さない場合にも `undefined` となる。

```ts
greet = (): void => {
  console.log("こんにちは");
};

const result = greet(); // resultはundefined
```

# `null`とは何か

一方、`null` は「**値が存在しないことを明示的に示す**」ために、開発者によって代入される値である。  
これは、将来的に値が設定される予定だが、現時点では「空」であることを示す際などに用いられる。

```ts
const user: string | null = null;
```

`null` は開発者が意図的に設定する値であり、意味が明確であることが利点である。

# `null` と `undefined` の違い

| 値          | 意味                             | 付与主体                 |
| ----------- | -------------------------------- | ------------------------ |
| `undefined` | 値がまだ設定されていない状態     | JS/TS（システム側）      |
| `null`      | 値が存在しないことを明示的に示す | 開発者（コード上で明示） |

簡潔に言えば、`undefined` は「未設定」、`null` は「空を明示的に設定」と覚えるとよい。

# 型としての使い分け

TypeScript では、変数に `null` や `undefined` を許容するためにはユニオン型を用いる必要がある。

```ts
const name: string = null; // エラー
const name: string | null = null; // OK
const age: number | undefined = undefined; // OK
```

# 実用例

### 関数の引数が任意の場合（オプショナルパラメータ）

```ts
const sayHello = (name?: string): void => {
  console.log("こんにちは、" + (name ?? "ゲスト") + "さん！");
};

sayHello(); // こんにちは、ゲストさん！
sayHello("太郎"); // こんにちは、太郎さん！
```

`name?: string` は、`string | undefined` と同義である。

### 値の存在チェック

`userName`に対して、`null` かどうかを確認している。

```ts
const userName: string | null = null;

if (userName === null) {
  console.log("ユーザー名が設定されていない");
}
```

### null 合体演算子（Nullish Coalescing Operator）

`??` を用いることで、`null` または `undefined` の場合にデフォルト値を設定することが可能である。

```ts
const value: string | null = null;
const result = value ?? "デフォルト値";
console.log(result); // "デフォルト値"
```

# おわりに

TypeScript における `null` と `undefined` は、データの存在や未設定状態を正確に表現するために重要な要素である。  
明確な目的を持って使い分けることで、バグの少ない堅牢なコードを書くことが可能となる。  
初学者にとっては少々とっつきにくい概念かもしれないが、実際のコードに触れながら理解を深めていくとよいだろう。
