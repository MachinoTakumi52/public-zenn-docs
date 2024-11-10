---
title: "NSN判別システム"
emoji: "🔫"
type: "tech"
topics: ["azure", "node", "vue", "vuetify", "dotnet"]
published: false
---

# はじめに

古着が流行っているさなか自身はミリタリー系の古着が大好きだ。
ただ店に行ってトラウザーなどを物色するが、どこの国のアイテムでいつ頃に製造されたか、また偽物かどうかも判別がつかない。
幸にもミリタリー系には、NSN と呼ばれる識別番号がある。
この NSN を使用してシステムを開発していきたいと思う。

# NSN

NATO Stock Number または National Stock Number の略。
ミリタリーアイテムそれぞれの固有の番号であり、アメリカ国防省(DOD)のアメリカ国防兵站局(DLA)が管理している。
13 桁の数字からなっており、数字にはそれぞれ意味をもっている。

- FSCG
  最初の 4 桁は、物品の種類や分類を示す。  
   これにより、武器や通信機器、医療用品など、品目のカテゴリが特定される。
  最初の 2 桁：物資の大カテゴリ（Federal Supply Group：FSG）を示し、同種の物資を大まかにグループ。
  後ろの 2 桁：より細かいカテゴリ（Federal Supply Class：連邦供給分類） に分けられ、特定の品目に関する分類を示す。

- NIIN(NSB + item number)
  残りの 9 桁は NIIN と呼ばれ、具体的な品目を特定する番号。  
   この 9 桁はさらに次のようにカテゴライズされる。
  　前 2 桁：NATO 国コード(National Codification Bureau：NSB)
  　後 7 桁：特定のアイテムに固有の識別番号。

たとえば、「8415-99-1234567」であれば、以下のようになる。
8415:衣類および個人装備品(Clothing, spetial purpose)
99:イギリス(UK)
1234567:固有の識別番号(siriarize number)

# Contract Number

国防兵站局発注番号とよばれ、アメリカ国防総省および関連機関が物資やサービスを
調達する際に供給契約や調達契約ごとに付与される固有の識別番号。
この国防兵站局発注番号内にハイフンで区切られた２桁の数字が契約年度  
つまり製造年度になる。

# 要件

NSN 入力時、以下の内容を表示

- 物品の種類
- 製造国
- 固有の識別番号

Contract Number 入力時、以下の内容を表示

- 契約組織
- 契約年度
- 契約種別

# 環境

- 構成図
  ![](/images/serachForMilitaryType/struction.svg)

- 環境(フロント)
  Typescript
  Vue
  Vuetify

- 環境(バックエンド)
  C# dotnet webAPI

# 参考資料

https://www.gear-illustration.com/2015/11/22/nsn-reference-part-1-fsc-code/
https://www.nato.int/structur/AC/135/pdf/NCS_codes_chart.pdf
https://repmart.jp/user_data/surplus.php
https://dfm92431.hatenablog.jp/entry/2022/08/27/082300
