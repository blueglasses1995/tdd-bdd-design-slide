---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://source.unsplash.com/collection/94734566/1920x1080
# apply any windi css classes to the current slide
class: 'text-center'
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# persist drawings in exports and build
drawings:
  persist: false
---

# TDD/BDD Software Design

設計経験のないエンジニアのための設計入門

<div class="pt-12">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10">
    Get Started <carbon:arrow-right class="inline"/>
  </span>
</div>

---

# Who Am I?

- **Name** -- 松熊利樹 (Toshiki Matsukuma)
- **Birthday** -- May 19th 1995
- **Birthplace** -- Tokyo
- **Occupation** -- Freelance Engineer
- **Location** -- Bangkok
- **Main Tech Stack** -- React.js, Next.js, Nest.js, GraphQL, Hasura, Prisma, AWS, GCP

<div class="w-60 relative mt-6">
  <div class="relative w-40 h-40">
    <img
      v-motion
      :initial="{ x: 800, y: -100, scale: 1.5, rotate: -50 }"
      :enter="final"
      class="absolute top-0 left-0 right-0 bottom-0"
      src="https://sli.dev/logo-square.png"
    />
    <img
      v-motion
      :initial="{ y: 500, x: -100, scale: 2 }"
      :enter="final"
      class="absolute top-0 left-0 right-0 bottom-0"
      src="https://sli.dev/logo-circle.png"
    />
    <img
      v-motion
      :initial="{ x: 600, y: 400, scale: 2, rotate: 100 }"
      :enter="final"
      class="absolute top-0 left-0 right-0 bottom-0"
      src="https://sli.dev/logo-triangle.png"
    />
  </div>
  <div
    class="text-5xl absolute top-14 left-40 text-[#2B90B6] -z-1"
    v-motion
    :initial="{ x: -80, opacity: 0}"
    :enter="{ x: 0, opacity: 1, transition: { delay: 2000, duration: 1000 } }">
    🐻
  </div>
</div>

<!-- vue script setup scripts can be directly used in markdown, and will only affects current page -->
<script setup lang="ts">
const final = {
  x: 0,
  y: 0,
  rotate: 0,
  scale: 1,
  transition: {
    type: 'spring',
    damping: 10,
    stiffness: 20,
    mass: 2
  }
}
</script>

<div
  v-motion
  :initial="{ x:35, y: 40, opacity: 0}"
  :enter="{ y: 0, opacity: 1, transition: { delay: 3500 } }">

[More About Me](https://toshiki-matsukuma.netlify.app/#/)

</div>

<style>
h1 {
  background-color: #2B90B6;
  background-image: linear-gradient(45deg, #4EC5D4 10%, #146b8c 20%);
  background-size: 100%;
  -webkit-background-clip: text;
  -moz-background-clip: text;
  -webkit-text-fill-color: transparent;
  -moz-text-fill-color: transparent;
}
</style>

---

# Who Is The Cource For?

<br>
<br>

- コードが書ける人
- 設計未経験の人
- 新規プロダクト開発に関心がある人

---

# Why TDD/BDD?

<br>

- コード経験者に取って設計に入るための入口となる
- 原要求をコードに落とし込むことで、機械的に要求の不備に気づけ、それをビジネスサイド（or クライアント）とともに詳細化できる

---

# なぜコード経験者に設計の強みがあるか？

<br>
<br>

- TDD/BDDを用いることで、原要求の詳細な不備に気づくことができるから
- 原要求と実装の間の乖離の勘所を体験してきたために、乖離を回避しやすい
    - 現状圧倒的大多数のシステムはオープン系（HTTP通信を必要とするシステム）
    - HTTP通信とオブジェクト指向は根本的に相入れない部分があり、設計ではそれを理解する必要がある
        - サーバー側のステートレス性: クライアント毎通信ごとにセッションが切れてしまい、物理的にはマイセッションごとに関数・コントロール内で新規のオブジェクトがされ直す
        - 一般的な人間の感覚では、ある対象に何かを依頼するときに、
        - インスタンスがいつ生成され、いつ破棄されるかなどの知見がないといけない
- 仮にコードがかけないとすると、上記2点に対応できない

---

# 本講義で使用する技術

<br>
<br>

- Typescript
- Nest.js
- Jest

---

# 本講義で使用する用語の定義

<br>
<br>

- オブジェクト指向プログラミング（OOP）
- モデリング
- ユースケース駆動開発
- 単体テスト
- E2Eテスト
- アジャイル（アジャイルソフトウェア開発宣言）
- MVP　(Minimum Viable Product)

---

# 本講義で使用する用語の定義
オブジェクト指向プログラミング（OOP）

- メッセージングとしてのオブジェクト指向
    - 人間が豊かになるために用いてきた「道具」に対するメンタルモデルをコンピュータに反映したもの
    - 全てがオブジェクトであるという原則と、大きなシステム（オブジェクト）は小さなシステム（オブジェクト）の組み合わせで作られるという「再帰的デザイン」によって、どんな複雑なシステムをデザインするときでも、覚えなくてはならない原則は少なくて済むようになる
- 抽象データ型としてのオブジェクト指向
    - 職業プログラマーにとって開発側の観点から仕事上のトラブルを少なくするといった、よりプラグマティックな理由から生まれた
    - カプセル化や継承、多態性（ポリモーフィズム）を特徴とする
    - 静的な抽象データ型としてのクラスをベースとする

<br>

→今回はより一般的な後者の意味で用いる

---

# 本講義で使用する用語の定義
モデリング

- あるビジネスにおける複数の機能における、オブジェクトとその関係を図で表したもの

<br>

- 本スライドにおける意味は、データベースのモデリングとは意味が異なるので注意

---

# 本講義で使用する用語の定義
ユースケース駆動開発

<iframe src="//www.slideshare.net/slideshow/embed_code/key/iTMp3UxCSJpfks" width="595" height="485" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="//www.slideshare.net/TomoeTeshima/ss-250804582" title="ユースケース駆動開発のワークショップやってみた！" target="_blank">ユースケース駆動開発のワークショップやってみた！</a> </strong> from <strong><a href="//www.slideshare.net/TomoeTeshima" target="_blank">Tomoe Sawai</a></strong> </div>

---

# 本講義で使用する用語の定義
単体テスト

- プログラムを構成する比較的小さな単位（ユニット）が個々の機能を正しく果たしているかどうかを検証するテスト
- 一般的に、ユニットは関数やメソッドとなる
- モジュールが結合される前の段階でテストが実施されるため、問題の原因の特定や修正が容易
- 開発者にかかるテストの工数面、スキル面の負担が大きくなりやすい

---

# 本講義で使用する用語の定義
E2Eテスト

- ソフトウェアで利用されるコンポーネントやレイヤーを全て結合した状態で、レイヤーの初めから終わりまでするテスト
- 壊れやすいため、メンテナンスコストがかかる
- 単体テストと比較すると工数対効果が高いことが多い

---
layout: iframe-right
url: https://agilemanifesto.org/iso/ja/manifesto.html
---

# 本講義で使用する用語の定義
アジャイル（アジャイルソフトウェア開発宣言）

- 優先度の高い要件から順に開発を進めていき、<br>『計画→設計→実装→テスト』といった開発工程を機能単位の小さいサイクルで繰り返し、<br>開発した各機能の集合体として1つの大きなシステムを形成する開発手法
- ビジネス環境の変化に伴う仕様変更に比較的柔軟に対応できる

---

# 本講義で使用する用語の定義
MVP　(Minimum Viable Product)

- 実用に足る最小限の製品
- 『Lean Startup』の著者Eric Riesとその師匠Steve Blankにより提唱された
- 無駄なコストをかけずに迅速にその製品が市場に受け入れられるかどうか調査することができる
- 販売による収益化の時期も早める
- 市場の反応を確認しながら製品の改良をしていく

---

# TDD （Test Driven Devemopment） とは？

<br>
<br>

<h3>プログラムの実装前にテストコードを書き、<br>そのテストコードに適合するように、<br>実装とリファクタリングを進めていく「開発」手法</h3>

一般的には設計手法ではなく開発手法として認識されている

---

# TDDのプロセス

<br>
<br>

1. **テストケースを作成**
-- 要件に基づいて、自動テストケースを作成
2. **すべてのテストケースを実行**
-- 現在開発されているコードでこれらの自動テストケースを実行
3. **そのテストケースのコードを開発**
-- テストケースが失敗した場合は、そのテストケースが期待どおりに機能するようにコードを記述
4. **テストケースを再度実行**
-- テストケースを再度実行し、これまでに開発したすべてのテストケースが実装されているかどうかを確認
5. **コードをリファクタリング**
-- コードをリファクタリングして、読みやすく再利用できるようにする
6. **新しいテストケースについて、手順1〜5を繰り返す**
-- すべてのテストケースが実装されるまで、他のテストケースに対してこのサイクルを繰り返す

---

# TDD用語説明

<br>
<br>

- **Red**: 実装した機能の要件を元に失敗するテストコードを書く
- **Green**: どんな方法でも良いのでテストが成功するコードを書く
- **Refactoring**: テストが成功する状態を維持しつつ簡潔・明快なコードにする

---

# TDDのコツ

<br>

『[テスト駆動開発入門](https://www.amazon.co.jp/%E3%83%86%E3%82%B9%E3%83%88%E9%A7%86%E5%8B%95%E9%96%8B%E7%99%BA-%EF%BC%AB%EF%BD%85%EF%BD%8E%EF%BD%94%EF%BC%A2%EF%BD%85%EF%BD%83%EF%BD%8B-ebook/dp/B077D2L69C/ref=sr_1_2?__mk_ja_JP=%E3%82%AB%E3%82%BF%E3%82%AB%E3%83%8A&crid=3L17W5LFPD06T&keywords=%E3%83%86%E3%82%B9%E3%83%88%E9%A7%86%E5%8B%95%E9%96%8B%E7%99%BA%E5%85%A5%E9%96%80&qid=1651309332&sprefix=%E3%83%86%E3%82%B9%E3%83%88%E9%A7%86%E5%8B%95%E9%96%8B%E7%99%BA%E3%81%AB%E3%82%85%E3%81%86m%2Caps%2C508&sr=8-2)』 by Kent Beck

> 自動テストが失敗した場合だけ、新しいコードを書く。重複を取り除く。2つの規則はプログラミングのタスクにおける順番を意味する。
> 1. レッド：動作しないテストを少しだけ作成する。おそらく最初はコンパイルできない
> 2. グリーン：テストをすぐに動作させる。そのためには、どのようなコードでもよい
> 3. リファクタリング：テストを動作させるためだけに作成された重複を全て取り除く

『[Clean Code](https://www.amazon.co.jp/Clean-Code-%E3%82%A2%E3%82%B8%E3%83%A3%E3%82%A4%E3%83%AB%E3%82%BD%E3%83%95%E3%83%88%E3%82%A6%E3%82%A7%E3%82%A2%E9%81%94%E4%BA%BA%E3%81%AE%E6%8A%80-Robert-C-Martin/dp/4048930591/ref=sr_1_1?__mk_ja_JP=%E3%82%AB%E3%82%BF%E3%82%AB%E3%83%8A&crid=2HKHCVN37Y5PF&keywords=clean+code&qid=1651309295&sprefix=clean+co%2Caps%2C754&sr=8-1)』 by Robert.C.Martin

3つの原則を守りながら実装を進める。

> 1. 失敗するテストができるまでプロダクトを書いてはいけない
> 2. 失敗するテストがある場合にはそれ以上テストを追加してはいけない
> 3. テストを成功させるプロダクトがある場合にはそれ以上プロダクトを追加してはいけない

---

# BDDとは

<br>
<br>

- BDDはTDDを拡張し、システムの予想される動作を確認するためのテストに重点を置いた開発手法。
- TDDを進めていく中で、「何をテストすべきか」「テストとは何か」といった問いに対しての説明として「Test」ではなく「Behavior」という言葉を使って説明する方が開発者にとって理解しやすかった、というDan North氏の経験からBDDは生まれた。
- 根本的にTDDとBDDは違わず、それまでのTDDの語彙では伝わりにくかった部分を言い換えたり、テンプレートを与えることで幾分かハードルを下げるという役割を持っている。
- BDDはTDDの表現方法やプロセスを特化させた方法論であり、相入れないものや根本的に異なるものではない。

---

# BDDのプロセス

<br>

1. **アプリケーションの動作を記述**
-- アプリケーションの動作は、製品の所有者、ビジネスアナリスト、またはQAによって、自然言語あるいは類似のシンプルな言語で記述される
2. **自動化されたスクリプトを作成**
-- このシンプル言語は、プログラミングテストに変換されます。
3. **関数型コードを実装**
-- 次に、動作の基礎となる関数型コードが実装されます。
4. **動作が成功したかどうかを確認**
-- ビヘイビアーを実行し、成功するかどうかを確認します。成功した場合は次の動作に進み、そうでない場合は関数型コードのエラーを修正してアプリケーションの動作を実現します。
5. **コードをリファクタリングまたは整理**
-- コードをリファクタリングまたは整理して、読みやすく再利用できるようにします。
6. **新しい動作について、手順1〜5を繰り返す**
-- 手順を繰り返して、アプリケーションにさらに多くの動作を実装します。

---

# BDD用語説明

- **ストーリー**
    - タイトル: 明示的なタイトル
    - 物語: 次の構造の短い紹介セクション
        - AS A [user]: ユーザー/役割として
        - I WANT [function]: 機能/性能ができる
        - SO THAT [value]: それにより価値がもたらされる
- **シナリオ**
    - 合否基準: 次の構造の物語の各特定のシナリオの説明
        - GIVEN: 前提・初期コンテキスト
        - WHEN: シナリオをトリガーする条件
        - THEN: 期待される結果

---
layout: iframe-right
url: https://shiftasia.com/ja/column/%E3%82%A2%E3%82%B8%E3%83%A3%E3%82%A4%E3%83%AB%E9%96%8B%E7%99%BA%E3%81%AB%E3%81%8A%E3%81%91%E3%82%8Btdd%E3%81%A8bdd/#:~:text=BDD%E3%81%AFTDD%E3%82%92%E6%8B%A1%E5%BC%B5,%E3%81%A7%E3%81%8D%E3%82%8B%E3%82%88%E3%81%86%E3%81%AB%E3%81%AA%E3%82%8A%E3%81%BE%E3%81%99%E3%80%82
---

# BDD例

- **ストーリー**
    - タイトル: 明示的なタイトル
    - 物語: 次の構造の短い紹介セクション
        - AS A [user]: ユーザー/役割として
        - I WANT [function]: 機能/性能ができる
        - SO THAT [value]: それにより価値がもたらされる
- **シナリオ**
    - 合否基準: 次の構造の物語の各特定のシナリオの説明
        - GIVEN: 前提・初期コンテキスト
        - WHEN: シナリオをトリガーする条件
        - THEN: 期待される結果

---

# モデリングとTDD/BDD: 概要

- モデリングとTDD/BDDは同一の目的を有する
    - ビジネスサイドの要求をコードにマッピングさせるための橋渡し

<br>
<br>

|  ツール   |  メリット   | デメリット |
| --- | --- | --- |
| <kbd>モデリング</kbd> | 可読性が高い / 必要工数が少ない | 厳密性には高度な技術が必要 |
| <kbd>TDD</kbd>  / <kbd>BDD</kbd> | コードが厳密性を担保する | コード全体の構造の可読性が低い / 必要工数が大きい |

---

# モデリングとTDD/BDD: 実務での使い分け

<br>
<br>

- 結論、どちらも使う
    1. 先にモデリングで顧客の要求をざっくりとオブジェクトとその関係に変換する
    2. その後、マッピングを参考にTDD/BDDを実行し、より厳密なオブジェクトとその関係を発見する
    3. 発見したTDD/BDDをもとに、モデリングを修正する
    4. 修正されたモデリングを参考にTDD/BDDを実行し、より厳密なオブジェクトとその関係を発見する
    5. 以降、3と4の繰り返し
- 開発者のモデリング技術、PJの工数と要求される堅牢性に依存して両者の割合を変える
    - 初期段階において厳密なモデリングができるのに越したことはない
    - ここでネックになリやすいのは、基本的には開発者のモデリング経験の欠如

---

# モデリングとTDD/BDD: 学習ロードマップ

<br>
<br>

- 設計経験が乏しい場合、モデリングに比してTDD/BDDの割合を高く設計を行う
- 少しずつ、初期段階のモデリングの精度が高まってくる
- 徐々にモデリングの割合を増やしていく

---
layout: iframe-right
url: https://docs.nestjs.com/
---

# ハンズオン開始<br>Nest.jsインストール

<br>
<br>

---

# ディレクトリ構成の確認

<iframe style="border: 1px solid rgba(0, 0, 0, 0.1);" width="800" height="450" src="https://stackblitz.com/edit/nestjs-typescript-starter-irhnsv?embed=1&file=README.md" allowfullscreen></iframe>

---
layout: iframe-right
url: https://jestjs.io/ja/docs/using-matchers
---

# テストファイル＆用語確認

- src/app.controller.spec.ts
- test/app.e2e-spec.ts

- テスト実行コマンドは？
    - package.jsonを読んでみよう

---
layout: iframe-right
url: https://github.com/blueglasses1995/tdd-bdd-design/commit/3ce836c68e21499ead2b851c2034dcda1225c9af
---

# テストのテスト
正常系と異常系

<br>
<br>

- 正常系
    - 以下を実行してみよう
```
yarn test
```

<br>
<br>

- 異常系
    - matcherの引数を変更して以下を再実行してみよう
```
yarn test
```

---
layout: image
image: assets/blue_bank.jpeg
---

# 目標成果物
### 青い銀行の基幹システム

---

# 要件を洗い出し
ユースケース駆動開発風

<iframe width="768" height="400" src="https://miro.com/app/live-embed/uXjVO4wIHjI=/?moveToViewport=-649,-424,2010,1123" frameBorder="0" scrolling="no" allowFullScreen></iframe>

---

# 要件を洗い出し(例)
ユースケース駆動開発風

- 顧客の入出金を閲覧できる
- 顧客が振込を依頼したら、依頼された宛先に振り込みができる
- 海外送金時は特定のレートで変換され送金できる
    - 100万円以上の振り込みは拒否される
- 行員が顧客の新規登録ができる
- 顧客の新規登録審査状況を管理できる
- 顧客が全国のATMから入出金できる
- 行員がログインできる

---

# MVP　(Minimum Viable Product)を決定

- 基幹システムとは
    - **顧客の入出金を閲覧できる**
    - **顧客が全国のATMから入出金できる**
    - 顧客が振込を依頼したら、依頼された宛先に振り込みができる
    - 海外送金時は特定のレートで変換され送金できる
        - 100万円以上の振り込みは拒否される
    - 行員が顧客の新規登録ができる
    - 顧客の新規登録審査状況を管理できる
    - 行員がログインできる

---

# モデリング準備

- ビジネスロジックに登場するオブジェクトを探す

<br>
<br>

- **基幹システム**とは
    - **顧客**の入出金を閲覧できる
    - 顧客が全国の**ATM**から入出金できる

---

# モデリング

<iframe style="border: 1px solid rgba(0, 0, 0, 0.1);" width="800" height="450" src="https://www.figma.com/embed?embed_host=share&url=https%3A%2F%2Fwww.figma.com%2Ffile%2Foh7KV23xHHszF3kdPRzKDW%2Ftdd-bdd-design-mapping" allowfullscreen></iframe>

---

# TDD開始（基本）

- エラーを出すメソッドは関数でラップしないといけない
    - expectは内部的にTry Catchしているが、関数でラップしないと最初にリードされた瞬間に実行されてしまう

---

# ロジック実装準備

- オブジェクト指向を意識する
    - オブジェクト指向におけるクラス（型）というのは「ある条件を満たした時に期待する結果が返ってくること」を保証する責務を負うもの
        - プリミティブ型では広範に使える一方保証してくれることが少なすぎる
        - 以下などビジネスロジックが散乱しないようにクラスを定義する
            - 「必須かつ一意であること」あるいは「任意であること」
            - 「ある文字数以上、ある文字数以内であること」
            - 「ある文字を含んでいないこと」
            - 「データベースへの通信時には暗号化されること」

---

# ロジック実装準備

- オブジェクト指向を意識する
    - オブジェクト指向におけるクラス（型）というのは「ある条件を満たした時に期待する結果が返ってくること」を保証する責務を負うもの
        - プリミティブ型では広範に使える一方保証してくれることが少なすぎる
        - ビジネスロジックが散乱しないようにクラスを定義する
- なぜ必要？
    - クラス（オブジェクト）が情報とロジックをもつ代わりに、（バリデーションなど）責務を全うさせることで、外部から詳細なロジックがわからなくても期待通りの結果が返ってくる。
    - 上記により以下が達成される
        - 工数削減: 色々なところであろオブジェクトのテスト・バリデーションなどを書かなくてすむようになる
        - 堅牢性: テスト・バリデーションの漏れを防ぎやすい

---

# オブジェクト指向に則りながら、<br>動くコードを作るテクニックの導入

- Givenはネットワークなど物理層も含みうるが、省略されることが多い

---

# 基本ロジック実装開始

---

# モデリングの見直し

ロジックを書かなくてもモデリングを進化させられる

- 口座オブジェクト
- 銀行オブジェクト

<iframe style="border: 1px solid rgba(0, 0, 0, 0.1);" width="800" height="350" src="https://www.figma.com/embed?embed_host=share&url=https%3A%2F%2Fwww.figma.com%2Ffile%2F6g5JuDRyj5OdfmgMexYsUF%2Ftdd-bdd-design-mapping-revised%3Fnode-id%3D0%253A1" allowfullscreen></iframe>

---

# 詳細ロジック実装開始

- 原則的には各クラスに対応したテストコードを書くべきであるが、場合によっては各クラスのテストのGiven部分の実装のために他クラスをコントロールできるようにしたい
    - とりわけ冪等性（メソッドと引数が同一な時にどんなタイミングでも同じ結果が返ってくること）が担保されていない部分に強引に冪等性を付与して、他クラスでテストしたい
- 本来の仕様として外部からコントロールできないようにしたいが、テストのために外部からコントロールできるようにする方法: インターフェースとFixtureクラス
    - 主に外界とのやりとりを行う部分をインターフェースに書き換える
        - プロパティを持たない
    - テスト用のFixtureクラスを用意する
        - プロパティはパブリックで良い
    - 実務で使用するクラスは別途作る
        - 当該クラスに実際に依存する（例: 当該クラスを所有するクラス）クラス内では、プロパティを持たないインターフェースを使用することで安全性が保たれる

---

# 全ての機能が完成

- Red→Greenまで完了

<br>
<br>

<h1>🎉🎉🎉🎉🎉</h1>
<h1>🎉🎉🎉🎉🎉</h1>
<h1>🎉🎉🎉🎉🎉</h1>
<h1>🎉🎉🎉🎉🎉</h1>
<h1>🎉🎉🎉🎉🎉</h1>

<br>
<br>

- 完成していない気がするが。。。

---

# 今後

- リファクタリング
    - コードとテストコードをリーダブルに
        - DRY
        - メソッド名・プロパティ名を美しく
        - テストコードのGiven, When, Thenをそれぞれひとかたまりにする
    - 理想: テストコードをビジネスサイドがざっくり読めるレベルにまでリーダブルにすることで、ビジネスサイドとの設計段階の協調ができる
- 要求の詳細化
    - TDD/BDD実施時に仕様の考慮もれに気がつき、ビジネスサイドと協調して仕様を決定する

---

# 今後

- 「堅牢性」と「開発効率」を両立するテスト
    - テストの抜け漏れを減らす2つの方法（QAの知識）
        - 探索的テスト
        - カバレッジ
    - テストの効率を高める
        - ロジックがシンプルなクラスには一般的にはテストコードを当てないことが多い
        - リファクタリングが必要な時のみ追加する
- 機能の追加

---

# Tips: ビジネスサイドとのコミュニケーション

<br>
<br>

- ビジネスサイドが提出する初期段階の要求は一般的にざっくりしている
    - ざっくり要求: エピック by ThoughtWorks (マーチン・ファウラーの会社)
    - エピックはテスト可能でないため、ユーザーストーリーと受け入れ条件に分解してテスト可能にする
    - 結果、ユーザーストーリーと受け入れ条件の文言が直接テスト文章として用いられ、テストコードは文言をコードにマッピングしただけのものとなる
    - 例: 顧客の入出金
- 他社ベンダーと協力関係にある場合、自社エンジニアが原要求をユーザーストーリーと受け入れ条件まで分解してベンダーに共有することで意思疎通の齟齬を減らせる

---

# Tips: BDDってどのクラスのテストから始める？

<br>
<br>

- どのクラスから始めても良い
- ただ、実務的にはサービスの根幹となる機能に責務をもつクラスから始めれば良い

---

# まとめ

- TDD/BDDはお客さんの要求をコードにマッピングする作業
- 流れ(一例)
    - ビジネスサイドのエピックをユーザーストーリーと受け入れ条件に分解する
    - MVPの選択: ユーザーストーリーと受け入れ条件のうち、早急に実装する必要のみを選択する
    - MVPを元に簡単なモデリングの実施
    - テストコードの追加
    - 基本ロジックの実装
    - モデリングの更新
    - 詳細ロジックの実装(Greenになるまで続ける)
    - コードとテストコードのリファクタリング
    - 要求の詳細化や機能の追加（上記の繰り返し）

---

# まとめ

<br>
<br>

- TDD/BDDはお客さんの要求をコードにマッピングする作業
- オブジェクト指向におけるクラス（型）というのは「ある条件を満たした時に期待する結果が返ってくること」を保証する責務を負うもの
- 原要求をコードに落とし込むことで、<br>機械的に要求の不備に気づけ、<br>それをビジネスサイド（or クライアント）とともに詳細化できる<br>（アジャイルソフトウェア開発宣言と合致）
