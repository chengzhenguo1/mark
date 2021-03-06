## VUE3

### 组件外使用路由和vuex

```` json
`导入store和router`
import store from '@store/index.js'
import router from '@router/index.js'

`使用vuex`
store.dispatch(....)

`使用router`
router.push(...)
            
`使用route`
router.currentRoute.value.query
````

### 使用emoji表情组件

```` json
下载https://www.npmjs.com/package/emoji-mart-vue-fast-japanese
<template>
  <div class="row">
    <img alt="Vue logo" src="./assets/logo.png" />
  </div>
  <div class="row">
    <Picker :data="emojiIndex" set="twitter" @select="showEmoji" />
  </div>
  <div class="row">
    <div>
      {{ emojisOutput }}
    </div>
  </div>
</template>

<script>
import data from "emoji-mart-vue-fast/data/all.json";
// Note: component needs to be imported from /src subfolder:
import { Picker, EmojiIndex } from "emoji-mart-vue-fast/src";
import "emoji-mart-vue-fast/css/emoji-mart.css";

let emojiIndex = new EmojiIndex(data);

export default {
  name: "App",
  components: {
    Picker
  },

  data() {
    return {
      emojiIndex: emojiIndex,
      emojisOutput: ""
    };
  },

  methods: {
    showEmoji(emoji) {
      this.emojisOutput = this.emojisOutput + emoji.native;
    }
  }
};
</script>
````



### cookie登录以及退出逻辑

```` json
`store中`
import { loginApi, logOutApi } from '@api/user.js'
import { getToken, setToken, removeToken } from '@/utils/auth'
export default {
  namespaced: true,
  state: {
    token: getToken()
  },
  getters: {
  },
  mutations: {
    setToken (state, token) {
      state.token = token
    }
  },
  actions: {
    // 用户登录
    login ({ commit }, data) {
      return new Promise((resolve, reject) => {
        loginApi(data).then(res => {
          commit('setToken', res.data)
          setToken(res.data)
          resolve()
        }).catch(err => reject(err))
      })
    },
    // 用户退出
    logOut ({ commit }) {
      console.log(document.cookie)
      return new Promise((resolve, reject) => {
        logOutApi().then(() => {
          commit('setToken','')
          removeToken()
          resolve()
        }).catch(err => reject(err))
      })
    }
  }
}

`auth.js中`
import Cookies from 'js-cookie'

const TokenKey = 'Admin-Token'

export function getToken () {
  return Cookies.get(TokenKey)
}

export function setToken (token) {
  return Cookies.set(TokenKey, token)
}

export function removeToken () {
  return Cookies.remove(TokenKey)
}


````



### element ui-滑动条

```` json
官方文档没有写,需要自己引入
<el-scrollbar wrap-class="scrollbar-wrapper">
</el-scrollbar>

//需要设置高度,默认没有高度
.el-scrollbar {
   height: 100%;
   overflow: hidden;
}
//自定义样式
.scrollbar-wrapper {
  overflow-x: hidden !important;
}
````

### 解决element-ui动态导航栏文字不隐藏问题

```` scss
    /* 动态导航栏文字不隐藏问题 */
    .el-menu--collapse {
        .el-submenu .el-submenu__title {
            span {
                display: none !important;
            }
        }
    }
````

### 组件内修改样式不起作用问题

```` json
在我们使用Vue搭建项目的时候，我们经常会用到一些UI框架，如Element，iView，但是有时候我们又想去修改UI框架的样式，当我们修改样式失败的时候，可以尝试一下/deep/，亲测有效。

那失败的原因是什么呢？ UI框架的样式是定义在全局中，我们使用scoped时，局部样式会被全局样式所覆盖（vue默认全局样式覆盖局部样式）。

有多种解决方法，推荐使用/deep/。

当希望scoped样式中的选择器“深入”，即影响子组件，则可以使用>>>组合器，/deep/是它的别名。 有些像 Sass 之类的预处理器无法正确解析 >>>，使用 /deep/ 操作符取而代之。

用法如下：
/deep/ .sg-select-selection {       border: 1px solid #0095fe;       background: none;       .sg-select-selected-value {         color: #fff;       }       > .iconfont {         color: #0095fe;       }     }
````

### 封装element面包屑

```` json
<template>
  <el-breadcrumb class="app-breadcrumb" separator="/">
    <transition-group name="breadcrumb">
      <el-breadcrumb-item v-for="(item, index) in levelList" :key="item.path">
        <!-- 当前页面 -->
        <span v-if="item.redirect === 'noRedirect' || index == levelList.length - 1" class="no-redirect">{{ item.meta.title }}</span>
        <!-- 前面的路径 -->
        <a v-else @click.prevent="handleLink(item)">{{ item.meta.title }}</a>
      </el-breadcrumb-item>
    </transition-group>
  </el-breadcrumb>
</template>

<script setup>
import { watchEffect } from 'vue'
import { useRoute, useRouter } from 'vue-router'

ref: route = useRoute()
ref: router = useRouter()

ref: levelList = null
// 动态生成面包屑
const getBreadcrumb = () => {
  // 判断有没有meta属性 
  // $route.matched一个数组，包含当前路由的所有嵌套路径片段的路由记录
  let matched = route.matched.filter((item) => item.meta && item.meta.title)
  // 判断第一个标签是不是首页,不是的话添加进去
  const first = matched[0]
  if (first.path !== "/") {
    matched = [{ path: "/home", meta: { title: "首页" } }].concat(matched)
  }
  // 最后过滤
  levelList = matched.filter(
    (item) => item.meta && item.meta.title && item.meta.breadcrumb !== false
  );
}

// 跳转页面
ref: handleLink = (item) => {
  const { redirect, path } = item
  if (redirect) {
    router.push(redirect)
    return;
  }
  router.push(path)
}

// 监视路由变化
watchEffect(route, getBreadcrumb)

</script>

<style lang="scss" scoped>
.app-breadcrumb.el-breadcrumb {
  display: inline-block;
  font-size: 14px;
  line-height: 50px;
  margin-left: 8px;

  .no-redirect {
    color: #97a8be;
    cursor: text;
  }
}
</style>
````



### 子父组件v-model修改

```` json
1.prop里定义 modelValue
2.使用计算属性
3.计算属性的get为
	()=> props.modelValue
4.计算属性的set为 
	()=> {context.emit('update:modelValue',val)}

`父组件`
 <message-input v-model="message" @send="sendMessage" />

`子组件`
  props: {
    modelValue: {
      type: String,
      default: '',
      required: true
    }
  },

 const message = computed({
      get () {
        return props.modelValue
      },
      set (val) {
        context.emit('update:modelValue', val)
      }
    })
````

### 配置css公共变量

``` json
// vue.config.js
module.exports = {
  chainWebpack: config => {
    const oneOfsMap = config.module.rule('scss').oneOfs.store
    oneOfsMap.forEach(item => {
      item
        .use('sass-resources-loader')
        .loader('sass-resources-loader')
        .options({
          // Provide path to the file with resources
          resources: './path/to/resources.scss',
 
          // Or array of paths
          resources: ['./path/to/vars.scss', './path/to/mixins.scss']
        })
        .end()
    })
  }
}
```

### 封装引入插件

```` json
`创建一个plugin.js文件`
import { Button, Empty, Form, Field, Tabbar, TabbarItem, List, NavBar, Icon, PullRefresh, Image as VanImage, Lazyload } from 'vant'


const plugins = [Button, Empty, Form, Field, Tabbar, TabbarItem, List, NavBar, Icon, PullRefresh, VanImage, Lazyload]

export default function (app) {
  for (const plugin of plugins) {
    app.use(plugin)
  }
}

`在main.js中导入`
import plugins from './plugins/index'
app.use(plugins)
````



### vuex模块化

```` json
创建一个user.js
`导出文件`
export default {
  namespaced: true, // 解决模块名称冲突问题
  state: {
    token: 'my token'
  },
  mutations: {
    resetToken (state) {
      state.token = ''
    }
  },
  actions: {
    // 清除token
    resetToken ({ commit }) {
      // 请求退出接口
      commit('resetToken')
    }
  }
};

`index.js中引入`
modules: {
    user
}

`在其他文件中使用方法`
import store from '@store/index.js'
//获取 state变量
const token = store.state.user.token
//触发action
store.dispatch('user/resetToken')

````



### vite项目以及ref-sugar用法

#### 配置别名

```` json
import vue from '@vitejs/plugin-vue'
import { defineConfig } from 'vite'
import path from 'path'

/**
 * https://vitejs.dev/config/
 * @type {import('vite').UserConfig}
 */
export default defineConfig({
  alias: {
    '@': path.resolve(__dirname, 'src'),
    '@c': path.resolve(__dirname, 'src/components'),
    '@views': path.resolve(__dirname, 'src/view'),
    '@api': path.resolve(__dirname, 'src/api'),
    '@router': path.resolve(__dirname, 'src/router'),
    '@store': path.resolve(__dirname, 'src/store'),
    '@styles': path.resolve(__dirname, 'src/styles')
  },
  plugins: [vue()]
})

````

`不需要export导出`

#### 声明变量以及使用

```` json
ref: counter
`不需要.value`
function add(){
    couner++
}
`计算属性`
ref: doubled = computed(() => couner * 2)

 const { id, name } = toRefs(props);
    watch(id, () => {
      console.log('id change');
    });

 const count = ref(0);
    const myStop = watch(
      count,
      (val, oldVal, onInvalidate) => {
        onInvalidate(() => {
          console.log('watch-clear...');
        });
        console.log('watch-val :>> ', val);
        console.log('watch-oldVal :>> ', oldVal);
      },
      { immediate: true }
    );



````

#### 父子组件通信

```` json
接收父组件传来的`props`
defineProps({
  testData: {
    type: String,
    required: true
  }
})


子组件往父组件触发emit事件
`定义事件`
const emit = defineEmit(['myclick'])
`1.行内触发事件`
@click="emit('myclick','我是子组件的数据')"
`2.定义事件触发`
@click="onclick"
const onclick = ()=>{
  emit('myclick','我是子组件的数据')
}
`3.上下文传递`
const ctx = useContext() //获取上下文
ctx.emit('myclick', '我是子组件的数据啊')


暴露接口事件
`需要暴露的组件`
ctx.expose({
  someMethod() {
    console.log('some message from Test.vue')
  }
})
`调用的组件`
获取ref
const child = ref(null)
调用暴露出来的函数
child.value.someMethod()


````

#### 安装mock

```` json
在项目根目录创建一个mock文件夹

`安装插件`
npm i mockjs -S
npm i vite-plugin-mock cross-env -D

`配置，vite.config.js`
import { viteMockServe } from 'vite-plugin-mock'

export default {
  plugins: [ viteMockServe({ supportTs: false }) ] //关闭ts模板
}

`设置环境变量，package.json`
{
  "scripts": {
    "dev": "cross-env NODE_ENV=development vite",
    "build": "vite build"
  },
} 
````

#### 导入sass

```` json
`安装`
npm i sass -D

`styles目录保存各种样式`
index.scss ....

`在main.js中导入主模块css`
import '@styles/index.scss'

`index.scss作为出口组织这些样式，同时编写一些全局样式`
在index.scss中
@import './element-ui.scss'
@import './global.scss'
等等
````

#### 按需引入插件

```` json
`创建plugins文件夹`
`在index中引入需要的插件`
import { ElButton, ElSelect } from 'element-plus'
import 'element-plus/lib/theme-chalk/el-button.css'
import 'element-plus/lib/theme-chalk/el-select.css'

`创建对应的插件关系表`
const plugins = [ElButton, ElSelect]

`循环使用插件`
export default function (app) {
  for (const plugin of plugins) {
    app.use(plugin)
  }
}

`main中引入该文件`
import plugins from '@/plugins/index.js'
`最后.use即可`
````

#### router-view的使用方法

```` html
    `v-slot Component会动态解析上下文子路由`

	<router-view v-slot="{ Component }">
      <transition name="fade" mode="out-in">
        <component :is="Component" />
      </transition>
    </router-view>
````

#### 模板中使用css变量

注意：`sass`文件导出变量解析需要用到`css module`，因此`variables`文件要加上`module`中缀。

```` json
创建global.module.scss文件    `module`

`要导出的变量`
$menuText:#bfcbd9;

$sideBarWidth: 210px;

// the :export directive is the magic sauce for webpack
// https://www.bluematador.com/blog/how-to-share-variables-between-js-and-sass
:export {
  menuText: $menuText;
  sideBarWidth: $sideBarWidth;
}

`组建中导入`
import variables from '@styles/global.module.scss'
`使用变量名即可`
<p :class="variables.menuBg"></p>
````

### 配置环境变量

```` json
根目录添加配置文件：.env.development
`VITE_BASE_API=/api`

其他文件使用
`import.meta.env.VITE_BASE_API`
````

