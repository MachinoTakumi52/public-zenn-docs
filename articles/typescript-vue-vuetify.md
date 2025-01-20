---
title: "フロントエンド開発ドキュメント(vue/ts vuetify)"
emoji: "📄"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["node", "typeScript", "vue", "vuetify"]
published: true
---

# はじめに

`vue`,`ts`,`vuetify`のフロントエンド開発の知見がたまってきたためコード規約をまとめてみた。

:::message
ただし、このルールは中小規模の開発を前提としており、すべての開発規模や環境で役立つものではないことに注意してほしい。
あくまで一つの参考として活用してもらえればと思う。
:::

# 　開発環境
以下の開発環境を参考に(自身が作成した開発環境テンプレート)話を進めていく。
フォルダ構造等の情報は、`README`に記載。
https://github.com/MachinoTakumi52/TypeScriptWithVue

# 共通

## 基本ルール

- **原則公式サイトのルールに準ずる**
  基本の言語ルールは、公式サイトに準ずる。  
  その中で、以降記載のルールを定義する。

  [typescript 公式](https://www.typescriptlang.org/docs/)  
  [typescript 入門](https://typescriptbook.jp/)
  [typescript コーディング規約](https://typescript-jp.gitbook.io/deep-dive/styleguide)
  [Vue 公式](https://ja.vuejs.org/guide/introduction)
  [Vue コーディング規約](https://ja.vuejs.org/style-guide/)
  [Vuetify 公式](https://vuetifyjs.com/ja/getting-started/installation/)

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
- **エレメントイベントに対する関数名**
  通常の関数とエレメントイベントとの差別化のために`on{イベント名}〇〇`のように作成する。

  ```typescript
  // クリックイベント
  cosnt onClickSaveData = () =>{}
  ```

## カラー定義

- **16 進数カラーコードを使用する**
  色指定時は、16 進数カラーコード(HEXA)を使用する。

# 開発ドキュメント[Typescript]

## コメント
- **TODOの付け方**
  だれのタスクかをわかりやすくするため以下のように自身の名前を入れて記述する
  ```typescript
  /**
  * 文字列出力
  * @returns
  */
  const exportString = (): void => {
  // TODO:名前 後で処理を書く

  //固定文字列を返却
  return "Hello World";
  };
  ```

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

## 環境変数定義
- **`.env`ファイルに定義する**
  プロジェクトフォルダ内の以下ファイルが環境変数定義ファイル。
  `.env.development`：開発用
  `.env.production`：本番用用

  環境変数の型定ファイルは以下に配置してあるので、環境変数を定義したら合わせて追記するようにする。
  `types/env.d.ts`

  詳しい内容については、[公式](https://ja.vite.dev/guide/env-and-mode)を参照。

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

- **文字列結合はテンプレートリテラルを使用**
  可読性が上がるため。

  ```typescript
  // NG
  const sample = (text: string): void => {
    return "hello " + text;
  };

  // OK
  const sample = (text: string): void => {
    return "hello ${text}";
  };
  ```

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
  ネームスペース`Consts`で囲み、以下のように宣言する。
  このような宣言の利点は、`Object.values(Consts.XXX)` で値の配列取得可能だから。
  画面でのみ使用する共通化しない定数は不要。
  言語に準ずるのであれば、定数とわかるように大文字スネークケースするが
  日本人は大文字英語に慣れていなく可読性が少し落ちるためアッパーキャメルケースで定義する。

  ```typescript
  /** 定数 */
  namespace Consts {
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

## リアクティブオブジェクト

- **type 宣言はしない**
  `type` 宣言と `ref` の型付けの２つが存在するとみにくいため、ジェネリクスにのみ型を宣言する。

  ```typescript
  const sample = ref<string | null>(null);
  ```

- **`reacitive`オブジェクトは使用しない**
  リアクティブには、`ref`と`reactive`が存在するが以下の点から`ref`を使用し原則`reactive`を使用しない。
  1. [公式](https://ja.vuejs.org/guide/essentials/reactivity-fundamentals#limitations-of-reactive)でも述べられているようにいくつか制限がある。
  2. リアクティブオブジェクトと通常オブジェクトの判別がつきにくい。
  3. [公式](https://ja.vuejs.org/guide/essentials/reactivity-fundamentals.html#declaring-reactive-state-1)より Composition API では、`ref`を推奨している。

## 条件付きレンダリング

- **`v-if`と`v-for`を併用しない**
  同じ要素に`v-if`と`v-for`を付与すると`v-if`の方が先に評価されてしまうため併用しない。

## リストレンダリング

- **キー属性は必ず付ける**
  キー属性を付与しなければ、リアクティブなリスト値が変更された時、レンダリングの不整合がおきてしまうため。

  ```typescript
  // NG
  <script setup lang="ts">
  const samples = ref<string[]>(["あ","い","う"]);

  </script>
  <template>
    <div v-for="sample of samples">{{sample}}</div>
  </template>

  ```

  ```typescript
  // OK
  <script setup lang="ts">
  const samples = ref<string[]>(["あ","い","う"]);

  </script>
  <template>
    <div v-for="sample of samples" :key="sample">{{sample}}</div>
  </template>

  ```

- **キー属性に`index`を使用しない**
  上記記載内容と同様、レンダリングの不整合がおきてしまうため。

- **`for-of` を使用する**
  `for-in` でも `for-of` でもいけるが `typescript` と合わせるように `for-of` を使用する。

- **リアクティブな値の変更イベントは、watch を使用**
  `input`タグなどの`change`イベントで変更値を取得することができるが、原則`watch`でリアクティブな値を監視して処理を実行させる。
  リアクティブに値が入ってくるのに`change`イベントの変更値を取る必要がないため。

  ```typescript
  // NG

  // リアクティブ値
  <script setup lang="ts">
  const sample = ref<string | null>(null);

  // 変更イベント
  const changeInput = (changeValue: string): void => {
    // 処理
  }
  </script>
  <template>
    <input @change="changeInput" v-model="sample" />
  </template>
  ```

  ```typescript
  // OK

  <script setup lang="ts">
  // リアクティブ値
  const sample = ref<string | null>(null);

  // 変更イベント
  watch(sample, async (newValue, oldValue) => {
    // 処理
  })

  </script>
  <template>
    <input v-model="sample" />
  </template>
  ```

## ウォッチャー

- **`watchEffect`は原則禁止**
  内部の例アクティブ値の変更をすべて検知するため処理が追いづらい。

## コンポーネント

- **`defineProps` に `withDefaults` は使用しない**
  `withDefaults`は、`props`の初期値を設定できるが、使用せずに指定不要なら`optional`を使用して`undefined`の対応を内部で行う。

- **双方向バインディングの値は`defineModel`を使用する**
  `defineProps`内に定義できるが、他`props`との差別化と型推論が効きやすくなる観点から`defineModel`を使用する。
  `defineProps`は、基本必須で定義する。

- **defineExpose は原則禁止**
  コンポーネント間の結合度が高くなり変更が大変になる可能性があるため。

# 開発ドキュメント[Vuetify]

## リアクティブオブジェクト

- **入力系コンポーネントの `v-model` に渡す `ref` の型は必ず `null` を考慮する**
  clearable でのクリア時が null となるため `<xxx | null>` のような型を宣言する。

  ```typescript
  const textFiled = ref<string | null>(null);
  ```

  例外として v-file-input で multiple を指定する場合はクリア時は空の配列となるので以下のように初期化。

  ```typescript
  const files = ref<File[]>([]);
  ```

  ただし multiple を指定しない場合は通常通り null を考慮して以下のように初期化。

  ```typescript
  const files = ref<File[] | null>(null);
  ```

## バリデーション

- **`Rules` は基本的に `template` の内部で直接配列で定義**
  コンポーネントの検証をまとめたモジュールからバリデーションを呼び出し定義する。

  ```typescript
  <v-text-field :rules="[textFieldRules.required]"></v-text-field>
  ```

  検証を行うためには、以下のようにコンポーネントに`ref`を設定することで検証が可能になる。

  ```typescript
  <script setup lang="ts">
    // リアクティブ値
    const name = ref<string | null>(null);

    // Form
    const form = ref<VForm>();

    // クリックイベント
    const onClick = ():vold =>{
      // 入力値検証
      const validateResult = await form.value!.validate();
      if (validateResult.valid === false) {
        // エラー処理
        return;
      }
    }
  </script>
  <template>
    <v-btn @click="onClick">登録</v-btn>
    <v-form ref="form">
      <v-text-field
        v-model="name"
        :rules="[textFieldRules.required]"
      ></v-text-field>
    </v-form>
    </template>
  ```

## グリッドシステム

- **原則使用しない**
  ` <v-row>`や`<v-col> ` などのグリッドシステムは使用しない。
  基本的に flex でレイアウトを組んでいく。

## vuetify のスタイルクラス

- **原則使用しない**
  可読性を考慮するため、タグに直接 `style` を記述する。
  共通で使用するものに関しては、`class`を作成する。
