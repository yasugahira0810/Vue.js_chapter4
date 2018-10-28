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

- Vue Routerでは、ページ遷移が実行される前後に処理を追加できるフック関数が3種類提供されている。
  + グローバルのフック関数
  + ルート単位のフック関数
  + コンポーネント内のフック関数

#### グローバルのフック関数

- 全てのページ遷移に対して設定できるフック関数
- router.beforeEachに関数をセットすると、ページ遷移が起こる直前にその関数が実行される
- *テキストを参考にした以下のコードで、ログ出力&リダイレクトを確認する。*

```
        router.beforeEach(function (to, from, next) {
            console.log('グローバルのフック関数が呼ばれたよ！' + 'from: "' + from.path + '", to: "' + to.path + '"')
            // global_hookへアクセスした時に/topへリダイレクトする例
            if (to.path === '/global_hook') {
                next('/top')
            } else {
                // 引数なしでnextを呼び出すと通常通りの遷移が行われる
                next()
            }
        })

```

#### ルート単位のフック関数

- Vue Router初期化時のルート定義において、beforeEnterオプションを設定することで、ルート単位でフックを追加することができる
- *テキストを参考にした以下のコードでログ出力&リダイレクトを確認する。*

```
                {
                    path: '/route_hook',
                    component: {
                        template: '<div>route_hook?redirect=trueでアクセスするとルート単位のフック関数によりtopへリダイレクトされるよ</div>'
                    },
                    beforeEnter: function (to, from, next) {
                        // /route_hook?redirect=trueでアクセスされた時だけtopにリダイレクトするフック関数を追加
                        if (to.query.redirect === 'true') {
                            console.log('ルート単位のフック関数が呼ばれたよ！')
                            next('/top')
                        } else {
                            next()
                        }
                    }
                },
```


#### コンポーネント内のフック関数

- ルート定義時ではなく、コンポーネント側でフック関数を定義することもできる
- *テキストを参考にした以下のコードでログ出力を確認する。*

```
        var UserList = {
            template: '#user-list',
            data: function () {
                return {
                    loading: false,
                    users: function () { return [] },
                    error: null
                }
            },

            // 「ページ遷移が行われて、コンポーネントが初期化される前」に呼び出される
            beforeRouteEnter: function (to, from, next) {
                console.log('コンポーネント内のフック関数が呼ばれたよ！' + 'from: "' + from.path + '", to: "' + to.path + '"')
                getUsers((function (err, users) {
                    if (err) {
                        this.error = err.toString()
                    } else {
                        // nextに渡すcallbackでコンポーネント自身にアクセス可
                        next(function (vm) { vm.users = users })
                    }
                }).bind(this))
            }
        }
```

-------------------

## 4.4 サンプルアプリケーションの実装

- 実践的なSPAとして、簡易的なユーザー情報登録・閲覧が可能なアプリを構築する
- アプリの詳細は書籍参照
- [コード](4.4.0.html)
- [動作](https://s3-ap-northeast-1.amazonaws.com/introduction-to-vuejs/4.4.0.html)

### 4.4.1 リストページの実装

- ユーザー一覧のためのリストページを実装する
- 本項までの内容で、簡単なページ遷移をするSPAが完成する
- [コード](./4.4.1.html)
- [動作](https://s3-ap-northeast-1.amazonaws.com/introduction-to-vuejs/4.4.1.html)

### 4.4.2 APIによるデータ通信

- Vue Routerで非同期にデータを取得する場合、Vue.jsのコンポーネントのcreatedとwatchで実装するのが一般的。
  + watch: 算出プロパティを汎化したVueコンポーネントのオプション。ここでは$routeを監視して変更のたびに処理を呼び出している。これは頻出パターン。
- Web API経由でデータを取得するところは関数getUserで擬似的に行う。
- [コード](4.4.2.html)
- [動作](https://s3-ap-northeast-1.amazonaws.com/introduction-to-vuejs/4.4.2.html)

### 4.4.3 詳細ページの実装

- /users/:userIdで詳細ページに遷移できるよう実装する
- 4.4.2で作成したgetUserを改修する
- [コード](4.4.3.html)
- [動作](https://s3-ap-northeast-1.amazonaws.com/introduction-to-vuejs/4.4.3.html)

### 4.4.4 ユーザー登録ページの実装

- /users/newにアクセスした時にユーザーの情報を追加できるページを作成する
- /users/newは/users/:userIdの前に配置する必要がある点に注意。ルーターの解釈は配列の先頭から行われるため
- ServerへPOSTリクエストを行うところは関数postUserで擬似的に行う
- ユーザーを追加した後ページをリロードすると、追加したユーザーのデータは消えることが確認できる
- [コード](4.4.4.html)
- [動作](https://s3-ap-northeast-1.amazonaws.com/introduction-to-vuejs/4.4.4.html)

### 4.4.5 ログイン・ログアウトの実装

- ルート単位のフック関数を使用して、簡易認証機能を実装する。
- HTML5のローカルストレージを用いて認証状態を保持する。*（ユーザー情報はローカルストレージに保持しないので、リロードして消えるところは変わらない。）*
- ユーザー登録ページのルート定義でフック関数を使用し、未認証の場合はログインページにリダイレクトするように実装する。
- v-showを使用して、ログイン状態でログアウトメニュー、ログアウト状態でログインメニューを表示する。
- [コード](4.4.5.html)
- [動作](https://s3-ap-northeast-1.amazonaws.com/introduction-to-vuejs/4.4.5.html)

### 4.4.6 サンプルアプリケーションの全体像

- 前項までの内容と重複するので省略。書籍にはjsfiddleのURLが載っているので、動作確認にはここが使える。

-------------------

## 4.5 Vue Routerの高度な機能

### 4.5.1 RouterインスタンスとRouteオブジェクト

- 4章の中で出てきていた$router, $routeについて解説する。
  + $router: Routerインスタンス。アプリ全体に対して1つ存在し、全般的なRouter機能を管理する。
  + $route: Routeオブジェクト。ページ遷移によるルーティングが発生するごとに生成される。現在アクティブなルートの状態を保持したオブジェクトで、現在のパスやURLパラメーターなどの情報を取得できる。
- それぞれの代表的な機能は書籍参照。

### 4.5.2 ネストしたルーティング

- Vue Routerでは、任意のコンポーネントに対して入れ子となるコンポーネントのルート定義ができる。（ネストされたルート）
- ここでは例として、ページ内容は/users/:userIdを基本としつつ、/users/:userId/postsのときはポスト情報を、/users/:userId/profileのときはプロフィール情報を表示する例を挙げる。
- 入れ子部分の設定のため、コンポーネント定義でrouter-view要素、ルート定義でchildrenオプションを使う。
- [コード](4.5.2.html)
- [動作](https://s3-ap-northeast-1.amazonaws.com/introduction-to-vuejs/4.5.2.html)

### 4.5.3 リダイレクト・エイリアス

- 一般的なアプリのリダイレクトに相当するVue Routerの機能として、リダイレクトとエイリアスがある。
  + リダイレクト: 実行した時にURLを書き換える
  + エイリアス: URLは書き換えずルーティング処理を実行する

#### リダイレクト

- [コード](4.5.3_redirect.html)
- [動作](https://s3-ap-northeast-1.amazonaws.com/introduction-to-vuejs/4.5.3_redirect.html)

#### エイリアス

- [コード](4.5.3_alias.html)
- [動作](https://s3-ap-northeast-1.amazonaws.com/introduction-to-vuejs/4.5.3_alias.html)

### 4.5.4 履歴の管理

- SPAではサーバー側のルーティングを介していないため、ブラウザの戻る・進むボタンを押した時の履歴操作もクライアント側で管理する必要があり、実現方法は「URL Hash」と「HTML5 History API」の２通りがある。

#### URL Hash

- URLの末尾に#/が付与され、ルーティングのパスを管理する
- デフォルト
- ブラウザの戻る・進むボタンを押した際には、内部的にhashchangeイベントを使って処理が行われる
- URL直打ちでも問題ない。

#### HTML5 History API

- #/は付与されず、通常のサーバサイドの遷移と同じ形式になる
- URL直打ちに対応するためには、サーバ側の設定が必要
- Vue Routerインスタンス生成時にmodeオプションとして'history'を指定すると切り替えられる