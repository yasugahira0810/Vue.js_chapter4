# 4章 Vue Routerを活用したアプリケーション開発

## *はじめに*

### *注意事項*

- *このリポジトリは[[秋葉原] Vue.js入門 輪読会 4章 Vue Routerを活用した開発 (初心者歓迎！)](https://weeyble-js.connpass.com/event/105884/)の発表資料として用意したリポジトリです。*
- *書籍の要約は正体、担当者の理解で書いているところは斜体で記載しています。*
- *「サンプルあり」と記載のある箇所は、サンプルを動かしながら説明します。*
- *権利関係で問題があれば、対応するのでご指摘ください。*

### 概要

- Vue.jsはシンプルなビュー層のライブラリなので、アプリによってはVue.js単体では実装が難しいことがある。
- 公式プラグインであるVue Routerを使えば、シングルページアプリケーション（以降、SPA）をはじめとしたURL遷移を伴うアプリを実装できる。
- 4章ではVue.jsとVue Routerを使ったアプリの実装方法について述べる。

-------------------


## 4.1 Vue Routerによるシングルページアプリケーション

### 4.1.1 シングルページアプリケーションとルーティング

- SPA: 初めに1つのHTMLをロードし、以降はAjaxを使った非同期通信でページを更新する
- ルーター（ルーティングライブラリ）: SPA実装時の考慮点を担ってくれるモジュール。考慮点は書籍参照。

### 4.1.2 Vue Routerとは

- Vue Router: Vue.js公式のルーティングライブラリ
- 基本機能は、宣言的にページ遷移のルールを定義し、ページ遷移の際にルールに対応するVue.jsのコンポーネントをアクティブにすること
- その他の機能は書籍参照。

-------------------

## 4.2 ルーティングの基礎


### 4.2.1 ルーターのインストール

- Vue.js本体に続けて読み込む。CDN利用すると便利。

### 4.2.2 ルーティング設計

- ルーティングにはルートとルーターコンストラクタを用いる。
- ルーティング実装の流れ
  1. ルーターコンストラクタを作成
  2. ルーターコンストラクタのroutesオプションにルートを定義
  3. Vueインスタンス作成時にrouterインスタンスを設定
- HTML側の設定にはrouter-view要素とrouter-link要素を用いる。
  + router-view: ルート定義で書いたコンポーネントを実際に反映させる要素
  + router-link: リンクを表示してページ遷移を可能にする要素
- [コード](4.2_basics_of_routing.html)
- [デモ](https://s3-ap-northeast-1.amazonaws.com/introduction-to-vuejs/4.2_basics_of_routing.html)

-------------------

## 4.3 実践的なルーティングのための機能

- [コード](4.3_URL_param.html)
- [デモ](https://s3-ap-northeast-1.amazonaws.com/introduction-to-vuejs/4.3_URL_param.html#/)

### 4.3.1 URLパラメーターの扱いとパターンマッチング

- 例を用いて説明する。ユーザ詳細ページ「/user/123」のようにURLにユーザIDを含んでいて、これをパラメータから受け取りたいケースがある。
- このようなケースでは、path内のURLに「/user/:userId」のようにコロンを使用してパターンを記述する。
- マッチしたURL上のパラメータはコンポーネント内の$route.paramsからパターンに使用したパラメータ名と同じ名前で取得できる。（$routeは4.5.2参照）

### 4.3.2 名前付きルート

- 前項の「/user/:userId」を静的にHTML側に記載することはできない。

```
<router-link to="/users">ユーザー一覧ページ</router-link> <!-- OK -->
<router-link to="/user/:userId">ユーザー詳細ページ</router-link> <!-- NG -->
```

- このようなケースに対応するため、Vue Routerではルート定義の際にnameオプションを使うことで、ルート定義に名前を付与できる。HTML側ではrouter-linkのtoパラメータに名前を指定する。この時、同時にURLパターンへのパラメータも同時に指定できる。
- *名前付きルートの時, router-linkは「to」ではなく「:to」（v-bind:toの省略記法）であることに注意。*

```
// ルート定義
{
	path: '/user/:userId',
	name: 'user',
	component: {
		template: '<div>ユーザーIDは{{ $route.params.userId }}です。</div>'
	}
}
```

```
<router-link to="/users">ユーザー一覧ページ</router-link>
<!-- :toはv-bind:toの省略記法。2.9.2「v-bindの省略記法」参照。 -->
<router-link :to="{ name: 'user', params: { userId: 123 }}">ユーザー詳細ページ</router-link>
```

### 4.3.3 router.pushを使った遷移

- router.pushを使うと、router-link相当のことをプログラム上で実施できる。
- *ディベロッパーツールのコンソールから以下のコマンドを実行すると手軽に確認できる。*

```
router.push({ name: 'user', params: { userId: 456 }})
```

### 4.3.4 フック関数


-------------------

## 4.4 サンプルアプリケーションの実装

- [コード](4.4.0.html)
- [動作](https://s3-ap-northeast-1.amazonaws.com/introduction-to-vuejs/4.4.0.html)

### 4.4.1 リストページの実装

- [コード](./4.4.1.html)
- [動作](https://s3-ap-northeast-1.amazonaws.com/introduction-to-vuejs/4.4.1.html)

### 4.4.2 APIによるデータ通信

- [コード](4.4.2.html)
- [動作](https://s3-ap-northeast-1.amazonaws.com/introduction-to-vuejs/4.4.2.html)

### 4.4.3 詳細ページの実装

- [コード](4.4.3.html)
- [動作](https://s3-ap-northeast-1.amazonaws.com/introduction-to-vuejs/4.4.3.html)

### 4.4.4 ユーザー登録ページの実装

- [コード](4.4.4.html)
- [動作](https://s3-ap-northeast-1.amazonaws.com/introduction-to-vuejs/4.4.4.html)

### 4.4.5 ログイン・ログアウトの実装

- [コード](4.4.5.html)
- [動作](https://s3-ap-northeast-1.amazonaws.com/introduction-to-vuejs/4.4.5.html)

### 4.4.6 サンプルアプリケーションの全体像

-------------------

## 4.5 Vue Routerの高度な機能

### モジュールの使い方&同一のミューテーションタイプ

- ストアで管理する情報が増えた場合、ストアを分割することができる。このとき使うのがストアのモジュールオプション。
- ステートはそれぞれのモジュールで管理され、ミューテーションおよびアクションは同一のタイプが定義できる。この場合にコミットをするとすべてのステートに対してタイプが実行される。

### ネームスペース

- 前節で説明した同一のタイプが一括コミットされるのは便利だが、使い分けしたいこともある。
- この場合は、モジュールを定義する際にnamespacedオプションをtrueに設定すればよい。

### モジュールのネスト

### ネームスペース付きモジュールから外部へのアクセス

-------------------

## SECTION46 その他のストアやオプション

### ストアの状態を監視する

### Strictモードで開発する

### Vuexでホットリロードを使用する

-------------------
