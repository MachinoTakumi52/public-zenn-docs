---
title: "フロントエンド開発ドキュメント(vue/ts vuetify)"
emoji: "📄"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["node", "typeScript", "vue", "vuetify"]
published: true
---

# はじめに

Web アプリケーション開発に携わり始めてから、気づけば 4 年が経とうとしている。この間、自分なりの開発スタイルや進め方が徐々に固まってきた。開発を効率化するためのテンプレートは用意しているものの、開発ルールをドキュメント化することなく、主に口頭で共有してきたのが現状だ。

その結果、レビュー時に開発者との認識のズレを埋めるため、余計な工数が発生してしまう場面が多々あった。この課題を解決するため、自分の経験と考えをもとにフロントエンド開発のルールをドキュメント化することにした。
:::message
ただし、このルールは中小規模の開発を前提としており、すべての開発規模や環境で役立つものではないことに注意してほしい。
あくまで一つの参考として活用してもらえればと思う。
:::

# 　開発環境

以下のリンク内の README を参照してほしい。
ちなみにリンクは、vue/TS/vuetify の開発環境テンプレートの git。
`TODO:自身のフロントの開発環境gitのパスを貼る`

# 共通

## 命名規則

- **原則開発言語のルールに準ずる**
  だれでも開発しやすいように使用する言語の命名規則にしたがって開発していく。
- **品詞を意識**
  日本語を入れることは原則禁止し、品詞を意識しながら開発する。
  省略した言葉は使用せず不要な単語は入れない。
  ただ、不要な単語を入れないように意識しすぎて可読性が下がることがないようにする。
  それにより多少名前が長くなることは問題ない。
- **配列,リスト名**
  `〇〇List`のようにするのではなく`〇〇s`とする。

# 開発ドキュメント[Typescript]

## コメント

- **変数/関数には、doc コメント を使用**
  変数、関数を定義した際のコメントは必ず doc コメントで書く。
  関数内の処理は普通のコメントで書く。
  関数の doc コメントに `@param` は不要。
  `@returns` は明記する。

```typescript
/**
 * 文字列出力
 * @returns
 */
const exportString = (): void => {
  //固定文字列を返却
  return "Hello World";
};
```

## 変数定義

- **`var` を使わない**  
  `const` または `let` を使用する。
  使用しない理由として、再宣言が可能なため意図しない処理をする可能性があるため。

- **型定義を書く**
  以下のように必ず型定義を書く。

```typescript
/**
 * 文字列型宣言
 */
const sampleString: string = "Hello world";
```

:::message
vue の開発ドキュメントで書くが、`ref`オブジェクトの際は、ジェネリクスにのみ型を宣言する。
:::

## 関数定義

- **`function` を使わずアロー関数を使用**
  **使用しない理由**
  - `this` のバインディング
    アロー関数は周囲のスコープの `this` を継承し、意図しない `this` の変更を防ぐ。
  - クラスメソッドでの安定性
    クラス内で意図しない `this` の問題を防ぐ。
  - 簡潔な構文
    記述が短くなり、コードの可読性が向上する。
- **引数はオブジェクト形式で定義する**  
   引数をオブジェクト形式で受け取ることで、名前付き引数のようにわかりやすく指定できる。
  呼び出すときに名前付きで指定できるので分かりやすい。
  オプショナルにするときは `?` を用いて、関数内で `undefined` の対応を行う。
  ```typescript
  const sample = (argument: {
    /** ID */
    id: number;
    /** 名前 */
    name: string?;
  }): void => {
    // nameのundefined対応
  };
  ```
- **戻り値は必ず指定する**
  `void` でも必ず指定する。
  非同期関数の場合は `Promise<型>`。

  ```typescript
  const sample = async (): Promise<void> => {
    // 非同期処理
  };
  ```

## 定数定義

- **宣言方法**
  ネームスペース`Constants`で囲み、以下のように宣言する。
  このような宣言の利点は、`Object.values(Consts.XXX)` で値の配列取得可能だから。
  画面でのみ使用する共通化しない定数は不要。

  ```typescript
  /** 定数 */
  namespace Constants {
    /** 共通で使用する定数 */
    export const sample1 = {
      AAA: "aaa",
      BBB: "bbb",
      CCC: "ccc",
    } as const;
    export type Role = (typeof Role)[keyof typeof Role];

    /** 画面でのみ使用する共通化しない定数 */
    const sample2 = {
      AAA: "aaa",
      BBB: "bbb",
      CCC: "ccc",
    } as const;
  }
  ```

## 反復処理

- **ループは基本 for-of に統一**
  `foreach` はループ内で `await` が使用できないため使用しない。
  `index` が欲しい場合は `for-in`。

  ```typescript
  const samples = [1, 2, 3, 4, 5];
  // for-of
  for (let item of samples) {
    console.log(item);
  }

  // for-in
  for (const key in obj) {
    console.log(key, obj[key]);
  }
  ```

## 比較演算

- **厳密比較演算子を使用する**
  値または型が異なるかを比較するため、より厳格な比較を行える。
  `boolean` 型を `true` 判定する場合でも `=== true` とする。

  ```typescript
  console.log(1 != "1"); // false (型変換が行われるため)
  console.log(true != 0); // true
  console.log(1 !== "1"); // true (型が異なるため)
  console.log(true !== 1); // true
  ```

- **違う型同士で比較しない**
  以下のように数値と文字列の比較などができるが、予期しない結果が出てくる可能性があるため原則禁止。

  ```typescript
  console.log(10 > "5");
  console.log("10" < 20);
  ```

- **論理否定演算子を使用しない**
  可読性がおちるため。

  ```typescript
  // NG
  if (!isExists) {
  }

  // OK
  if (isExists === true) {
  }
  ```

## 日付

- **Date 型の比較は getTime で比較**
  比較演算子は問題なく動作するが、等価と不等価演算子の挙動がおかしくなる。
  理由として、日付型は値型ではなくオブジェクト型であるため。
  なので、`Date` 型をタイムスタンプ（`number` 型）に変換して比較する。

  ```typescript
  const date1 = new Date("2025-01-01");
  const date2 = new Date("2025-01-01");

  console.log(date1.getTime() === date2.getTime()); // true
  console.log(date1.getTime() !== date2.getTime()); // false
  ```

## 未定義と値なし

- **未指定値は原則 null**
  `undefined` と `null` の違いをしっかりと理解した上で使用する。

## 例外処理

- **エラーは握り潰さない**
  `catch` して `false` を返すなどで例外を握りつぶさない
  共通のエラーハンドリングが存在するため、例外は`throw` する
  `catch` で `throw` しか行わないのであれば `catch` 自体不要

## オプショナルチェイニング

- **原則使用しない**
  データが存在しない場合もエラーが発生しないため、意図的なのかがわからなくなる。

## `Null` 合体演算子

- **原則使用しない**
  便利だが可読性がさがる。
  三項演算子で良い。

## `Null` 合体代入演算子

- **原則使用しない**
  便利だが可読性がさがる。
  三項演算子で良い。

## 非 null アサーション演算子

- **むやみやたらに使用しない**
  `null` でないことを確認して使用する。

  ```typescript
  const value: string | null = null;

  if (value !== null) {
    console.log(value!.toUpperCase());
  }
  ```

## モジュール呼び出し

- **相対パスは使用しない。**
  パッケージ以外の `import` は `@` を利用する

```typescript
import { getSamples } from "@/services/sampleService";
```

- **`default export` の禁止**
  `default export` は各所で名前を統一することが難しい。
  原則、名前付き `export` とする。

# 開発ドキュメント[Vue]

TODO

# 開発ドキュメント[Vuetify]

TODO
