# Starting the Vue project

## vue のプロジェクトを作成

```shell
vue create client
```

または

```shell
mkdir client
cd client
vue create .
```


## Bootstrap の導入

```shell
npm install vue bootstrap-vue bootstrap
```

warning を解消

```shell
npm WARN bootstrap@4.3.1 requires a peer of jquery@1.9.1 - 3 but none is installed. You must install peer dependencies yourself.

$ npm install jquery@1.9.1
+ jquery@1.9.1
added 1 package from 1 contributor and audited 24250 packages in 7.214s
found 2 vulnerabilities (1 moderate, 1 high)
  run `npm audit fix` to fix them, or `npm audit` for details

$ npm audit
                       === npm audit security report ===
# Run  npm install jquery@3.4.1  to resolve 2 vulnerabilities
SEMVER WARNING: Recommended action is a potentially breaking change

$ npm install jquery@3.4.1
+ jquery@3.4.1
updated 1 package and audited 24250 packages in 7.248s
found 0 vulnerabilities
````

`src/main.js`を修正

```javascript
import Vue from 'vue'
import App from './App.vue'
import BootstrapVue from 'bootstrap-vue'
import 'bootstrap/dist/css/bootstrap.css'
import 'bootstrap-vue/dist/bootstrap-vue.css'

Vue.use(BootstrapVue);
Vue.config.productionTip = false;

new Vue({
  render: h => h(App),
}).$mount('#app');
```

[Bootstrap-Vue : Getting Started](https://bootstrap-vue.js.org/docs/)

## font-awesome の導入

[font-awesome](https://www.npmjs.com/package/@fortawesome/vue-fontawesome)

## axios の導入

```shell
npm install axios
```

`src/components/Hello.vue`を修正

```javascript
<script>
import axios from "axios";
...
</script>
```


## Unexpected console statement の修正

```package.json
    "rules": {
      "no-console": "off"
    },
```

## github pages 用

`vue.config.js`を追加

```vue.config.js
module.exports = {
    outputDir: 'docs',
    publicPath: process.env.NODE_ENV === 'production'
        ? '/portfolio/'
        : '/'
}
```
