# Vue でアプリ開発するためのTips

## Vue
- Create Vue application
- Vuex
- Router
- axios
- (TypeScript)


### Create Vue application

shellでvue appを作成

```shell
$ vue create app
```

Manually select features を選択

```shell
Vue CLI v3.7.0
? Please pick a preset:
  vuex (vue-router, vuex, babel, eslint)
  default (babel, eslint)
❯ Manually select features
```

TypeScript + Router + Vuex ( + Babel + Linter / Formatter ) を選択する

```shell
Vue CLI v3.7.0
? Please pick a preset: Manually select features
? Check the features needed for your project:
❯◉ Babel
 ◉ TypeScript
 ◯ Progressive Web App (PWA) Support
 ◉ Router
 ◉ Vuex
 ◯ CSS Pre-processors
 ◉ Linter / Formatter
 ◯ Unit Testing
 ◯ E2E Testing
```

ヒストリーモードを選択(Y)すると、  
SPA にて、URL遷移にページのロードを必要としない
Y を選択する  

```shell
Vue CLI v3.7.0
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, Router, Vuex, Linter
? Use history mode for router? (Requires proper server setup for index fallback in production) (Y/n)
```

(とりあえず)一番上を選択 (2番目も良さそう)

```shell
Vue CLI v3.7.0
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, Router, Vuex, Linter
? Use history mode for router? (Requires proper server setup for index fallback in production) Yes
? Pick a linter / formatter config: (Use arrow keys)
❯ ESLint with error prevention only
  ESLint + Airbnb config
  ESLint + Standard config
  ESLint + Prettier
```

一番目が好み

```shell
Vue CLI v3.7.0
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, Router, Vuex, Linter
? Use history mode for router? (Requires proper server setup for index fallback in production) Yes
? Pick a linter / formatter config: Basic
? Pick additional lint features: (Press <space> to select, <a> to toggle all, <i> to invert selection)
❯◉ Lint on save
 ◯ Lint and fix on commit
```

configファイルを置く場所を設定する  
package.jsonのみか、小分けにするか  
判断の指標を調べる

```shell
Vue CLI v3.7.0
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, Router, Vuex, Linter
? Use history mode for router? (Requires proper server setup for index fallback in production) Yes
? Pick a linter / formatter config: Basic
? Pick additional lint features: (Press <space> to select, <a> to toggle all, <i> to invert selection)Lint on save
? Where do you prefer placing config for Babel, PostCSS, ESLint, etc.? (Use arrow keys)
❯ In dedicated config files
  In package.json
```

今までのテンプレートを保存するか否かを選択する

```shell
Vue CLI v3.7.0
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, Router, Vuex, Linter
? Use history mode for router? (Requires proper server setup for index fallback in production) Yes
? Pick a linter / formatter config: Basic
? Pick additional lint features: (Press <space> to select, <a> to toggle all, <i> to invert selection)Lint on save
? Where do you prefer placing config for Babel, PostCSS, ESLint, etc.? In dedicated config files
? Save this as a preset for future projects? (y/N)
```

アプリケーションの作成完了後に  
サーバーを起動

```shell
$ cd app
$ npm run serve
```


参考 : ) [Vue CLI 3の変更点・使い方(vue-cliから@vue/cliにアップデート)](https://qiita.com/Junpei_Takagi/items/603d44f7885bd6519de2)

### Router + Vuex

###### Router  
下記さえ意識すれば、勝手に作られるファイルの書き方で理解できる

```
.
├── README.md
├── babel.config.js
├── node_modules
├── package-lock.json
├── package.json
├── postcss.config.js
├── public
│   ├── favicon.ico
│   └── index.html
└── src
    ├── App.vue
    ├── assets
    │   └── logo.png
    ├── components           <-------------- 部品単位
    │   └── HelloWorld.vue
    ├── main.js
    ├── router.js            <-------------- src/views/hogeのルーティングを設定する
    ├── store.js
    └── views                <-------------- 画面単位
        ├── About.vue        
        └── Home.vue
```

src/router.js にて下記のように```path :'*'``` をしていておくと  
登録の内URLにアクセスしたときにredirect してあげるURLを設定できる
```javascript
Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '*',
      redirect: 'signin',
    },
  ]
})
```

###### Vuex
状態管理をするライブラリ


Vuexは```store.js```内で管理する

- state  
状態のこと

```javascript
export default new Vuex.Store({
  state: {
    isAdmin: true,
  },
})
```

みたいなことを書くとどのコンポーネントでも下記のように使えるようになる

```javascript
if ($store.state.isAdmin === true) {
  console.log('hoge!');
}
```

- mutations  
stateを変更できる唯一のメソッド  
-> これをコンポーネントで使うことでstateを変更できる

```javascript
const store = new Vuex.Store({
  // ...
  mutations: {
    mutationType( state, payload ) { // payloadって名前じゃなくても配列でもオブジェクトでも良い
      state.count = payload;
    },
  },
})
```
こう書く(commit)ことでコンポーネントで使える
```javascript
this.$store.commit('mutationType', 3 ); // 3 は payloadに代入される
```

- actions  
API通信や非同期処理はこのactionsに記述する

```javascript
export default new Vuex.Store({
  // ...
  actions: {
    increment: ({ commit }) => {
      setTimeout(() => {
        commit('increment');
      }, 5000);
    },
  },
})
```

コンポーネントでmutationsを呼び出すように書く(commit)  
actions内の処理を呼び出すには、dispatchメソッドを利用する

```javascript
this.$store.dispatch('increment');
```

コンポーネントでcommitやdispatch をするためにいちいちただのマッピングメソッドを書かなければならないが、  
それをオートでマッピングしてくれるmapMutationsというものがある
他にも便利な関数が用意されている(ヘルパー関数という)  

参考 : ) [Vuex のマップ系ヘルパーの書き方決定版！](https://qiita.com/POPOPON/items/37ef8833a76a2e87dea9)  
参考 : ) [Vue CLI 3 ではじめるVue RouterとVuex入門](https://noumenon-th.net/programming/2019/02/15/vue-cli-3/)


###### axios

axiosのインストール(vueアプリのディレクトリ内で)
```shell
$ npm install --save axios
```

使用したいコンポーネントでaxiosをインポートし、  
axiosでapiレスポンスを受け取る
```javascript
<script>
import axios from 'axios';
export default {
  name: 'hoge',
  data () {
    return {
      info: null,
    };
  },
  mounted () {
    axios
      .get('https://api.coindesk.com/v1/bpi/currentprice.json')
      .then(response => (this.info = response.data.bpi));
  },
}
</script>
```

templateで使えるようになる

```javascript
<template>
  <div class="hello">
    {{ info.USD.code }} : {{ info.USD.rate }} |
    {{ info.GBP.code }} : {{ info.GBP.rate }} |
    {{ info.EUR.code }} : {{ info.EUR.rate }}
  </div>
</template>
```

golang apiを呼び出す際  
CORS エラーが出るのでサーバー側で設定をする必要がある

CORS対策前(ここではgorillaを使用)
```go
package main

import (
    "log"
    "net/http"
    "github.com/gorilla/mux"
)

func public(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("hello public!\n"))
}

func main() {
    r := mux.NewRouter()
    r.HandleFunc("/public", public)
    log.Fatal(http.ListenAndServe(":8000", r))
}
```
CORS対策後
```go
package main

import (
    "log"
    "net/http"
    "github.com/gorilla/mux"
    "github.com/gorilla/handlers" // 追加!!
)

func public(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("hello public!\n"))
}

func main() {
    allowedOrigins := handlers.AllowedOrigins([]string{"http://localhost:8080"}) // 追加!!
    allowedMethods := handlers.AllowedMethods([]string{"GET", "POST", "DELETE", "PUT"}) // 追加!!
    allowedHeaders := handlers.AllowedHeaders([]string{"Authorization"}) // 追加!!
    r := mux.NewRouter()
    r.HandleFunc("/public", public)
    log.Fatal(http.ListenAndServe(":8000", handlers.CORS(allowedOrigins, allowedMethods, allowedHeaders)(r))) // 変更!!
}
```

go run main.go を忘れずに...

参考 : ) [axios を利用した API の使用](https://jp.vuejs.org/v2/cookbook/using-axios-to-consume-apis.html)  
参考 : ) [「Vue.js + Go言語 + Firebase 」で始める! Frontend & Backend API 両方で認証するセキュアなSPA開発ハンズオン!](https://qiita.com/po3rin/items/d3e016d01162e9d9de80)

###### TypeScript

参考 : ) [TypeScriptを導入する前に『覚悟』したほうが良いこと 4項目](https://hachibeechan.hateblo.jp/entry/I-cannot-say-typescript-is-easy)  
参考 : ) [TypeScriptの型入門](https://qiita.com/uhyo/items/e2fdef2d3236b9bfe74a)
