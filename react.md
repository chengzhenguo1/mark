

# React

#### 改变变量

##### 需要调用setState

```` jsx
跟vue中直接改变不一样，需要手动调用  this.setState({msg: 'msg'});
this.setState({
      msg: '改变了啊'
})
````

##### setState是异步的

```` jsx
this.setState({
      msg: '改变了啊'
})
console.log(this.state.msg) //获取到是之前的数据

// 我们可以在回调函数中获取到更新后的数据，类似于vue中的nextTick
this.setState({
      msg: '改变了啊'
    }, () => {
      console.log(this.state.msg)
})

//也可以在更新生命周期函数获取
componentDidUpdate(prevProps, provState,snapshot){
    console.log(this.state.msg)
}
````



### JSX

##### 三目运算符

````jsx
{ flag ? 1 : 2 }
````

#### for循环

```` jsx
{/* 第一种外部 */}
const listArr = []

arr.forEach((e)=>{
     listArr.push(e.name)
})

<h2>{listArr}</h2>

{/* 第二种 */}
{arr.map(e=> <li>{e.name}</li>)}
         

{/* 过滤for循环 */}
arrList: [7,8,1,2,4,5,7,3,6,6] 
 {
       arrList.filter(e => e>5).map(e=><li>{e}</li>)
 }
                                    
{/* 截取for */}
 {
      arrList.slice(0, 4).map(e =><li> {e} </li>)
 }
````

#### 绑定事件

```` jsx
{/* 显示绑定 */}
onClick={this.changeMsg.bind(this)}
<button onClick={this.changeMsg.bind(this)}>修改</button>

{/* 隐式绑定,在constructor绑定好this */} 
<button onClick={this.btnClick}>-1</button>   
constructor(props){
        super(props)
        this.btnClick = this.btnClick.bind(this)
}

{/* 箭头函数绑定this */}
btnClick = ()=>{
      console.log(this.state)
}

{/* 直接传入箭头函数，隐式绑定this */}
<button onClick={()=>{this.btnClick()}}>++</button>

{/* 事件对象Event */}
<button  onClick={ (e) =>{ this.btnClick(e) } } >++</button>

{/* 默认不传事件对象Event */}
<button  onClick={this.btnClick.bind(this)} >++</button>
````

#### 注释

```` jsx
 {/* 我是注释 */}
````

#### 运算

```` jsx
const { msg,msg2 } = this.state;
<h2>{msg + msg2}</h2>
<h2>20 * 50 = {20 * 50}</h2>
//类似于vue的计算属性
<h2>{this.getMsg()}</h2>
````

#### 绑定属性

```` jsx
<h2 className={this.state.class}>{this.getMsg()}</h2>

<h2 title={this.state.title? 'yes':'no'}>{this.getMsg()}</h2>

// 绑定图片
<img src={parseImg('http://p2.music.126.net/HZmS0L2T_1Q88WcL8D9kLg==/109951165761710151.jpg',140)} alt="" />

 function parseImg(url,size){
            return `${url}?param=${size}y${size}`
}
````

#### 绑定class

```` jsx
 // 因为class和js 类的关键字冲突
<h2 className='a b c'>20 * 50 = {20 * 50}</h2>

<h2 className={'helloWolrd ' + (this.state.active ? 'active' : '')}>20 * 50 = {20 * 50}</h2>
````

#### 绑定style

```` jsx
<h2 style={{color:'red',fontSize:'20px'}}>{msg + msg2}</h2>


const styles = {
                    color:'red',
                    fontSize: '20px'
}
 <h2 style={styles}>{msg + msg2}</h2>
````

#### 条件渲染

```` jsx
{/* 三目运算来判断 */}
<h1>{ !isLogin ? 'helloCode' : '' }</h1>
<button onClick={ ()=> {this.changeLogin() }} >{ isLogin ? '登录' :'退出' }</button>

{/* &&运算符 */}
<h1>{ isLogin && 'helloCode' }</h1>

{/* render外做运算 */}
 const user = {
          name: 'helloCode',
          state: '登录'
 }
 
 if(isLogin){
      user.name = 'name',
      user.state = '退出'
 }else {
      user.name = null,
      user.state = '登录'
 }  

 return(
     <div>
         <h1>{user.name}</h1>
         <button onClick={ ()=> {this.changeLogin() }} >{user.state}</button>
     </div>
 )

{/* 模拟v-show */}
 <h1 style={{ display: isLogin?'block':'none'} }>{user.name}</h1>
{/* 或者 */}
 <h1 style={{ display: user.display } }>{user.name}</h1>
````

#### 组件间传值

##### 父传子

```` jsx
`类组件间传值`

// 子组件 props
class Child extends Component {
    constructor(props) {
        // 如果没有其他state,则不用调用constructor
        super(props)
        this.state = {
            name: '我自己的'
        }
    }
    render() {
        const { msg, age } = this.props
        const { name } = this.state
        return (
            <div>
                <h1>{msg}</h1>
                <h1>{age}</h1>
                <h1>{name}</h1>
            </div>
        )
    }
}
// 父组件 属性绑定
export default function App() {
    return (
        <div>
            {* 第一种 *}
            <Child msg={this.state.msg} age={this.state.age} />
            {* 第二种,使用展开运算符 *}
            <Child {...this.state} />
        </div>
    )
}

// 函数式组件，第一个参数默认为props传递进来
function Child(props) {
    const { msg, age } = props
    return (
        <div>
            <h1>{msg}</h1>
            <h1>{age}</h1>
        </div>
    )
}


// 对传入的属性进行校验

Child.propTypes = {
    name: PropTypes.string,
    age: PropTyeps.number
}

````

##### 子传父

```` jsx
// 子组件中
sendMsg() {
    const { itemClick } = this.props
    // 要emit的事件
    itemClick(this.state.msg)
}

// 父组件中监听
 <Child itemClick={(msg: string) => this.onMsg(msg)} />

onMsg(msg: string) {
    console.log('接受子组件的msg：' + msg)
}
````

##### 父传孙

```` jsx
// Context 可以让我们无须明确地传遍每一个组件，就能将值深入传递进组件树。
// 为当前的 theme 创建一个 context（“light”为默认值）。
const ThemeContext = React.createContext('light')


// 无论多深，任何组件都能读取这个值。
// 进行包裹，我们将 “dark” 作为当前的值传递下去
<ThemeContext.Provider value="dark">
          <Child {...this.state} />
</ThemeContext.Provider>
````

##### 事件总线BUS

```` jsx
1.yarn来安装events
	yarn add @types/events events
    
    创建EventEmitter对象：eventBus对象
    const eventBus = new EventEmitter()
    
    发出事件：eventBus.emit("事件名称", 参数列表)
	EventBus.emit("send", '我是父亲的参数')

    监听事件：eventBus.on("事件名称", 监听函数)
    EventBus.on('send', (msg) => {
      console.log('msg')
      this.setState({msg})
    })

    移除事件：eventBus.removeListener("事件名称", 监听函数)
	EventBus.removeAllListeners('send')
    EventBus.off('send', this.onMsg.bind(this))
````

#### ref

```` javascript
获取DOM对象

1.字符串设置 `不推荐`
<div className="a" ref='dom'>我是Dom哦</div>
//使用refs获取
console.log(this.refs.dom)

2.设置为对象 `推荐`
// 创建ref对象
this.myRef = createRef()
//设置ref
<div className="a" ref={this.myRef}>我是Dom哦</div>
//获取ref
this.myRef.current.innerHTML = `<p>我呗改变了</p>`

3.传入一个函数
// 创建ref对象
this.myRef = null
//设置ref
<div className="a" ref={arg => this.myRef = arg}>我是Dom哦</div>
//获取ref
this.myRef.innerHTML = `<p>我呗改变了</p>`


4.调用子组件的方法
// 创建ref对象
this.myRef = createRef()
//设置ref
<Child ref={this.myRef} />
//调用子组件中的方法
this.myRef.current.getValue()

````

##### 高级组件

```` jsx
const User = createContext({
  nickName: 'zs',
  age: 15,
  sex: '男'
})


class Home extends PureComponent {
  render() {
    return (
      <div>
        <p>Home</p>
        <p>{this.props.nickName}</p>
        <p>{this.props.age}</p>
        <p>{this.props.sex}</p>
      </div>
    )
  }
}

class About extends PureComponent {
  render() {
    return (
      <div>
        <p>About</p>
        <p>{this.props.nickName}</p>
        <p>{this.props.age}</p>
        <p>{this.props.sex}</p>
      </div>
    )
  }
}

// 合并组件
function mergeCom(Compoent: any) {
  return (props: any) => {
    return (
      <User.Consumer>
        {
          value => {
            return <Compoent  {...value} {...props} />
          }
        }
      </User.Consumer>
    )
  }
}

const HomeMer = mergeCom(Home)
const AboutMer = mergeCom(About)

class App extends PureComponent {
  render() {
    return (
      <div>
        父元素
        <User.Provider value={{ nickName: '程振国', age: 16, sex: '男' }}>
          <HomeMer nickName='嘎嘎' />
          <AboutMer />
        </User.Provider>
      </div >
    )
  }
}


export default App
````

**例子：登录鉴权**

```` jsx
<div>
   <AuthCartPage isLogin={true} />
</div >
// 购物车组件
const AuthCartPage = mergeCom(Cart)

//校验是否需要登录
function mergeCom(Compoent: any) {
  return (props: any) => {
    const { isLogin } = props
    // 也可以写成网络请求，没有请求完显示Loading状态
    if (isLogin) {
      return <Compoent {...props} />
    } else {
      return <Login />
    }
  }
}

````



<div>
        <AuthCartPage isLogin={true} />
      </div >

#### 处理表单输入框

```` jsx
1.创建state对象
 state: State = {
    name: '',
    password: '',
    vcode: ''
  }
2.创建form表单和input
//绑定 onSubmit提交事件,传递e阻止默认事件
<form onSubmit={e => this.Login(e)}>
          <div>
            <label htmlFor="name">
              账号：
            </label>
        	{* 绑定value和改变事件，state的数据改变，视图跟着变，输入框变化去改变state的数据改变 *}
            <input type="text" id='name' name='name' value={name} onChange={e => this.handleInput(e)} />
          </div>
          <div>
            <label htmlFor="password">
              密码：
            </label>
            <input type="password" id='password' name='password' value={password} onChange={e => this.handleInput(e)} />
          </div>
          <div>
            <label htmlFor="vcode">
              验证码：
            </label>
            <input type="text" id='vcode' name='vcode' value={vcode} onChange={e => this.handleInput(e)} />
          </div>
          <input type='submit' value='登录' />
</form>

// 表单提交事件
Login(e: any) {
    e.preventDefault()
    console.log(this.state)
}

// 处理输入框输入事件
handleInput(e: any) {
    this.setState({
      // 可以拿到name和value，动态的变化
      [e.target.name]: e.target.value
    })
}

// 单选框  主要是checked属性
<label>
          男：
          <input
            type="radio"
            value="男"
            name='sex'
            checked={sex === '男'}
            onChange={e => this.handleInput(e)}
          />
</label>

<label>
       女：
       <input
          type="radio"
          value="女"
          name='sex'
          checked={sex === '女'}
          onChange={e => this.handleInput(e)}
        />
</label>
````





#### 生命周期

![img](https://ss1.bdstatic.com/70cFvXSh_Q1YnxGkpoWK1HF6hhy/it/u=700259853,2243097718&fm=26&gp=0.jpg)

```` jsx
export default class App extends Component {
    constructor(){
        super()
        console.log('我是第一个')
    }
    render() {
        return (
            <div>
                <Child msg='我是你爸爸' age='21' />
                <Child msg='我是干爸爸' age='21' />
            </div>
        )
    }
    componentDidMount(){
        console.log('我是第二个，挂载完毕后执行')
    }
    componentDidUpdate(){
        console.log('我是更新dom后执行')
    }
    componentWillUnmount(){
        console.log('我是销毁时执行')
    }
}
````

#### solt插槽

1.不推荐

```` jsx
  // 放入三个插槽
  <NavBar>
          <Header />
          <Main />
          <Footer />
  </NavBar>
  
 // 类中使用 `并不推荐使用该方法`
 class NavBar extends Component {
  render() {
    return (
      <div>
        <div>{this.props.children[0]}</div>
        <div>{this.props.children[1]}</div>
        <div>{this.props.children[2]}</div>
      </div>
    )
  }
}
````

2.

```` jsx
// 具名插槽，推荐
 <NavBar leftSlot={<Header />} centerSlot={<Main />} rightSlot={<Footer />} />

//组件内
class NavBar extends Component {
  render() {
    const { leftSolot, centerSlot, rightSlot } = this.props
    return (
      <div>
        <div>{leftSolot}</div>
        <div>{centerSlot}</div>
        <div>{rightSlot}</div>
      </div>
    )
  }
}
````

#### createPortal挂载节点

```` jsx
//某些情况下，我们希望渲染的内容独立于父组件，甚至是独立于当前挂载到的DOM元素中
//第一个参数（child）是任何可渲染的 React 子元素，例如一个元素，字符串或 fragment
//第二个参数（container）是一个 DOM 元素

class Modal extends PureComponent {
  constructor(props: any) {
    super(props)
  }
  render() {
    return createPortal(
      this.props.children,
      document.getElementById('modal')
    )
  }
}

// 插入即可
<Modal>
          我是弹窗
          确认
</Modal>
````

#### fragment （类似于VUE的template）

```` jsx
`fragment不会真正的渲染`
<Fragment>
          <p>啊啊啊</p>
          <p>嘎嘎嘎</p>
</Fragment>
````



#### 防止多次渲染

**1.使用PureComponent替代Component**

```` jsx
PureComponent只会浅比较,减少多次渲染
Component和PureComponent有一个不同点
除了为你提供了一个具有浅比较的shouldComponentUpdate方法，PureComponent和Component基本上完全相同。当props或者state改变时，PureComponent将对props和state进行浅比较。另一方面，Component不会比较当前和下个状态的props和state。因此，每当shouldComponentUpdate被调用时，组件默认的会重新渲染。
````

**或者使用shouldComponentUpdate钩子**`

```` json
`该方法有两个参数`
参数一：nextProps 修改之后，最新的props属性
参数二：nextState 修改之后，最新的state属性

该方法返回值是一个boolean类型
返回值为true，那么就需要调用render方法；
返回值为false，那么久不需要调用render方法；
默认返回的是true，也就是只要state发生改变，就会调用render方法

````

**2.循环时使用key来提高diff算法的效率**

```` json
`key的注意事项`
1.key应该是唯一的；
2.key不要使用随机数（随机数在下一次render时，会重新生成一个数字）；
3.使用index作为key，对性能是没有优化的；
````

**高阶组件mem**

```` jsx
/*
React.memo()是一个高阶函数，它与 React.PureComponent类似，但是一个函数组件而非一个类。
*/
const App = memo(function App(props) {
  return (
    <div>
      <p>嘎嘎</p>
    </div>
  )
})
````

#### 使用svg的方法

```` tsx
import {ReactComponent as SotfLogo} from 'assets/sotfware-logo.svg'

//直接当组件使用即可
<SotfLogo color='red' width='10rem' />
````



#### 其他库的推荐

```` jsx
1.styled-components
	用来写css样式的
2.classNames
	行内class需要拼接很不方便，推荐的一个css库，类似于vue的class写法

````

#### Vite中使用antd

https://juejin.cn/post/6938671679153373214#heading-6

##### 修改主题色

```` json
# 安装 antd
yarn add antd
# 安装 less
yarn add -D less


````

**配置**

```` json
// vite.config.ts

export default defineConfig({
  css: {
    preprocessorOptions: {
      less: {
        // 支持内联 JavaScript
        javascriptEnabled: true,
        // 重写 less 变量，定制样式
        modifyVars: {
          '@primary-color': 'red',
        },
      },
    }
  }
})
````

**或者创建一个variables**

```` ts
yarn add less-vars-to-js

...
import path from 'path'
import fs from 'fs'
import lessToJS from 'less-vars-to-js'

// less-vars-to-js 是将 less 样式转化为 json 键值对的形式
const themeVariables = lessToJS(
  fs.readFileSync(path.resolve(__dirname, './config/variables.less'), 'utf8')
)

...
css: {
  preprocessorOptions: {
    less: {
      // 支持内联 JavaScript
      javascriptEnabled: true,
      // 重写 less 变量，定制样式
      modifyVars: themeVariables
    }
  }
}
...

// variables.less
// 自定义覆盖 =============================================================
@primary-color: green; // 全局主色
// 下面你可以各种写一些覆盖的样式，这里就简单覆盖一个主题色的样式，我们改为绿色


````

##### 环境变量如何获取

**打包时** 那么我们如何在打包时，在 `vite.config.js` 中拿到环境变量呢？ 首先我们先修改 `package.json` 的 `scripts` 属性，如下所示：

```json
scripts: {
  "dev": "vite --mode development",
  "build:beta": "vite build --mode beta",
  "build:release": "vite build --mode release",
  "serve": "vite preview"
}
```

`--mode` 后代表的是各个环境对应的环境变量值，这里为什么一定要用 `--mode` 呢？官方定的，后续可以在页面中拿到这个变量值。

我们在 `vite.config.js` 打印如下所示：

```javascript
console.log('process:::env', process.argv)
```

重新运行 `npm run dev` 如下所示：

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/03332cf0368b4288ab706b7ce9a3a4ff~tplv-k3u1fbpfcp-zoom-1.image)

最后一个参数，便是我们设置好的环境变量。所以我们可以通过如下获取环境变量：

```javascript
const env = process.argv[process.argv.length - 1]
```

我们可以在 `vite.config.js` 里配置 `index.html` 内，静态资源的路径前缀。改动如下：

```javascript
...
const env = process.argv[process.argv.length - 1]
const base = config[env]
...
export default defineConfig({
	base: base.cdn
})
```

在根目录的 `config` 目录内，添加 `index.js` 文件，添加如下内容：

```javascript
export default {
  development: {
    cdn: './',
    apiBaseUrl: '/api' // 开发环境接口请求，后用于 proxy 代理配置
  },
  beta: {
    cdn: '//s.xxx.com/vite-react-app/beta', // 测试环境 cdn 路径
    apiBaseUrl: '//www.beta.xxx.com/v1' // 测试环境接口地址
  },
  release: {
    cdn: '//s.xxx.com/vite-react-app/release', // 正式环境 cdn 路径
    apiBaseUrl: '//www.xxx.com/v1' // 正式环境接口地址
  }
}
```

我们来打个测试包试试，运行如下指令：

```bash
npm run build:beta
```

结果如下所示： ![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9b21c20f79584f1b80d8082587439280~tplv-k3u1fbpfcp-zoom-1.image) 同理可得正式环境的鸟样。

**运行时** 那么运行代码的时候，我们如何获取到相应的环境变量呢？答案是 `import.meta.env` 。我们在 `Index/index.jsx` 里打印一下便可知晓：

```javascript
import React from 'react'
import { Button } from 'antd'

export default function Index() {
  console.log('import.meta.env', import.meta.env)
  return <div>
    <Button type='primary'>Index</Button>
  </div>
}

const serverAddr = import.meta.env.MODE == 'development' ? '123.207.32.32' : '123.207.32.32'

export const SERVER = `http://${serverAddr}:9001`
```

![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d123d15239944a5487087389f25da2c0~tplv-k3u1fbpfcp-zoom-1.image)

##### 导入样式

```` json
// App.tsx
import 'antd/dist/antd.less'
````

##### 使用 CSS 预处理器

修改 CSS 文件名为 CSS Module 格式即可，无需配置，Vite 默认支持。

```` json
index.css --> index.module.css
index.scss --> index.module.scss
index.less --> index.module.less
````

##### 全局样式配置

```` json
// vite.config.ts

export default defineConfig({
	...
  css: {
    preprocessorOptions: {
      scss: {
        // 自动导入全局样式
        additionalData: "@import '@/styles/base.scss';"
      },
    }
  },
})
````

##### 路径别名

```` json
// vite.config.ts

export default defineConfig({
	...
  resolve: {
    alias: [
       { find: /^~/, replacement: '/src' },
    ],
  },
})

同时在tsconfig.js中配置

"baseUrl": ".",
"paths": {
      "@/*": ["./src/*"]
 }

import Mine from "~/pages/Mine"
````

##### 按需加载

```` ts
// 安装按需加载插件
npm i vite-plugin-imp -D

// vite.config.js 中添加以下内容
import { defineConfig } from 'vite'
import reactRefresh from '@vitejs/plugin-react-refresh'
import vitePluginImp from 'vite-plugin-imp'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [
    reactRefresh(),
    vitePluginImp({
      libList: [
        {
          libName: "antd",
          style: (name) => `antd/lib/${name}/style/index.less`,
        },
      ],
    })
  ],
  css: {
    preprocessorOptions: {
      less: {
        // 支持内联 JavaScript
        javascriptEnabled: true,
      }
    }
  },
})

````

##### 完整配置

```` ts
import { defineConfig } from 'vite'
import path from 'path'
import fs from 'fs'

import lessToJS from 'less-vars-to-js'
import reactRefresh from '@vitejs/plugin-react-refresh'
import vitePluginImp from 'vite-plugin-imp'

// less 转换成json 格式
const themeVariables = lessToJS(
  fs.readFileSync(path.resolve(__dirname, './config/variables.less'), 'utf8')
)


// 获取环境变量
// const env = process.argv[process.argv.length - 1]

export default defineConfig({
  resolve: {
    // 路径别名
    alias: {
      '@': path.resolve(__dirname, './src'),
      '@store': path.resolve(__dirname, './src/store'),
      '@router': path.resolve(__dirname, './src/router'),
      '@conponents': path.resolve(__dirname, './src/components')
    }
  },
  plugins: [
    reactRefresh(),
    vitePluginImp({
      // 按需引入
      libList: [
        {
          libName: "antd",
          style: (name) => `antd/lib/${name}/style/index.less`,
        },
      ],
    })
  ],
  css: {
    preprocessorOptions: {
      less: {
        // 支持内联 JavaScript
        javascriptEnabled: true,
        modifyVars: themeVariables
      }
    }
  },
})
````

##### Tip

###### antd的table的类型扩展

```` typescript
interface ListProps extends TableProps<project>{
    users: User[]
}
````



#### React中使用TS

https://juejin.cn/post/6910863689260204039#heading-13

https://github.com/typescript-cheatsheets/react#useful-table-for-types-vs-interfaces

##### 声明UseState

- **const** [user, setUser] = React.useState<string| null>(null);  **初始值是** null 或 string
- **const** [user, setUser] = React.useState<string>(null);  

##### 函数式组件

利用 `React.FC` 内置类型的话，不光会包含你定义的 `AppProps` 还会自动加上一个 children 类型，以及其他组件上会出现的类型：

```` tsx
// 等同于
AppProps & { 
  children: React.ReactNode 
  propTypes?: WeakValidationMap<P>;
  contextTypes?: ValidationMap<any>;
  defaultProps?: Partial<P>;
  displayName?: string;
}

// 使用
interface AppProps = { message: string };

const App: React.FC<AppProps> = ({ message, children }) => {
  return (
    <>
     {children}
     <div>{message}</div>
    </>
  )
};
````



##### Ref

这个 Hook 在很多时候是没有初始值的，这样可以声明返回对象中 `current` 属性的类型：

```tsx
const ref2 = useRef<HTMLElement>(null);
```

以一个按钮场景为例：

```tsx
function TextInputWithFocusButton() {
  const inputEl = React.useRef<HTMLInputElement>(null);
  const onButtonClick = () => {
    if (inputEl && inputEl.current) {
      inputEl.current.focus();
    }
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
```

当 `onButtonClick` 事件触发时，可以肯定 `inputEl` 也是有值的，因为组件是同级别渲染的，但是还是依然要做冗余的非空判断。

有一种办法可以绕过去。

```tsx
const ref1 = useRef<HTMLElement>(null!);
```

`null!` 这种语法是非空断言，跟在一个值后面表示你断定它是有值的，所以在你使用 `inputEl.current.focus()` 的时候，TS 不会给出报错。

但是这种语法比较危险，需要尽量减少使用。

在绝大部分情况下，`inputEl.current?.focus()` 是个更安全的选择，除非这个值**真的不可能**为空。（比如在使用之前就赋值了）

###### forwardRef 

```` tsx
type Props = { children: React.ReactNode; type: "submit" | "button" };
export type Ref = HTMLButtonElement;
export const FancyButton = React.forwardRef<Ref, Props>((props, ref) => (
  <button ref={ref} className="MyClassName" type={props.type}>
    {props.children}
  </button>
));
````



##### 表单和事件

```` tsx
type State = {
  text: string;
};
class App extends React.Component<Props, State> {
  state = {
    text: "",
  };
  // typing on RIGHT hand side of =
  onChange = (e: React.FormEvent<HTMLInputElement>): void => {
    this.setState({ text: e.currentTarget.value });
  };
  render() {
    return (
      <div>
        <input type="text" value={this.state.text} onChange={this.onChange} />
      </div>
    );
  }
}

// typing on LEFT hand side of =
  onChange: React.ChangeEventHandler<HTMLInputElement> = (e) => {
    this.setState({text: e.currentTarget.value})
  }
  
<form
    ref={formRef}
    onSubmit={(e: React.SyntheticEvent) => {
    e.preventDefault();
    const target = e.target as typeof e.target & {
      email: { value: string };
      password: { value: string };
    };
    const email = target.email.value; // typechecks!
    const password = target.password.value; // typechecks!
    }}
>
  <div>
    <label>
      Email:
      <input type="email" name="email" />
    </label>
  </div>
  <div>
    <label>
      Password:
      <input type="password" name="password" />
    </label>
  </div>
  <div>
    <input type="submit" value="Log in" />
  </div>
</form>
````

##### 上下文context

```` tsx
import * as React from "react";

interface AppContextInterface {
  name: string;
  author: string;
  url: string;
}

const AppCtx = React.createContext<AppContextInterface | null>(null);

// Provider in your app

const sampleAppContext: AppContextInterface = {
  name: "Using React Context in a Typescript App",
  author: "thehappybug",
  url: "http://www.example.com",
};

export const App = () => (
  <AppCtx.Provider value={sampleAppContext}>...</AppCtx.Provider>
);

// Consume in your app

export const PostInfo = () => {
  const appContext = React.useContext(AppCtx);
  return (
    <div>
      Name: {appContext.name}, Author: {appContext.author}, Url:{" "}
      {appContext.url}
    </div>
  );
};
````

#### React中使用Mobx

###### 1.创建一个store主仓库

```` ts
// sotre/index.ts

//我们创建的user的分仓库
import UserStore, { User } from './user/user'

const user = new UserStore()

// 定义仓库类型
export interface StoreType {
    user: User
}


const stores: StoreType = {
    user
}

export default stores
````

###### 2.在main的ts中引入主仓库

```` ts
import { Provider } from 'mobx-react'
import stores from './store/index'

// 需要拿Provider来包裹
ReactDOM.render(
  <Provider {...stores}>
    <App />
  </Provider>,
  document.getElementById('root')
)
````

###### 3.编写user仓库

```` ts
// user/user.ts
import { makeAutoObservable } from 'mobx';

export interface User {
    name: string
    password: string
    userInfo: string
    loginAction: (payload: { name: string, password: string }) => void
}

class UserStore implements User {
    // state
    name = ''
    password = ''

    constructor() {
        // 观察全部数据
        makeAutoObservable(this)
    }
    // getter
    get userInfo() {
        return this.name + '' + this.password
    }

    // action
    loginAction(payload: { name: string; password: string; }): void {
        this.name = payload.name
        this.password = payload.password
        console.log(this)
        console.log('登录成功')
    }
}

export default UserStore
````

###### 4.将action的方法抽离

```` ts
// 创建一个actions的文件
export default class actions {
    loginAction(payload: { name: string; password: string; }): void {
        this.name = payload.name
        this.password = payload.password
        console.log(this)
        console.log('登录成功')
    }
}

// 导入 actions 的同时改写user.ts中的方法格式
import actions from './actios'

loginAction(payload: { name: string, password: string }) { }

// 同时实现 actios
class UserStore implements User, actions

// 创建一个mixin混入函数
function applyMixins(derivedCtor: any, baseCtors: any[]) {
    baseCtors.forEach(baseCtor => {
        Object.getOwnPropertyNames(baseCtor.prototype).forEach(name => {
            derivedCtor.prototype[name] = baseCtor.prototype[name]
        })
    })
}

// 将actions 放到 user类中
applyMixins(UserStore, [actions])
````

###### 5.在页面中使用store

```` tsx
import React from 'react'
import { inject, observer } from 'mobx-react'
import { StoreType } from './store/index'

function App({ user }: StoreType) {

  const login = () => {
     // 使用actions
    user.loginAction({ name: 'zs', password: '123456789' })
  }

  return (
    <div>
      <div>
          {* 使用state数据 *}
        <p>UserInfo:{user.name}</p>
        <p>{user.password}</p>
        <p>getter: {user.userInfo}</p>
      </div>
      <div>
        <button onClick={login}>登录</button>
      </div>
    </div>
  )
}

export default inject('user')(observer(App))
````

##### 总结

```` ts
// actions
export interface UserActions {
    loginAction: (this: { name: string, password: string }, payload: { name: string, password: string }) => void
}


export default class actions implements UserActions {
    // 声明this的属性
    loginAction(this: { name: string, password: string }, payload: { name: string, password: string }): void {
        this.name = payload.name
        this.password = payload.password
        console.log(this)
        console.log('登录成功')
    }
}

// user/index
import { makeAutoObservable } from 'mobx'

import { applyMixins } from '@/utils/mixins'

import actions, { UserActions } from './actions'

export interface User {
    name: string
    password: string
    userInfo: string
}

class UserStore implements User, UserActions {
    // state
    name = ''
    password = ''

    constructor() {
        // 观察全部数据
        makeAutoObservable(this)
    }
    // getter
    get userInfo() {
        return this.name + '' + this.password
    }

    // action
    loginAction(this: { name: string, password: string }, payload: { name: string; password: string; }): void { }
}


applyMixins(UserStore, [actions])

export default UserStore


// main.tsx
import React from 'react'
import ReactDOM from 'react-dom'
import { Provider } from 'mobx-react'

import stores from './store/index'
import './index.css'

import App from './App'

ReactDOM.render(
  <Provider {...stores}>
    <App />
  </Provider>,
  document.getElementById('root')
)

````



###### 工具函数

```` ts
function applyMixins(derivedCtor: any, baseCtors: any[]) {
    baseCtors.forEach(baseCtor => {
        Object.getOwnPropertyNames(baseCtor.prototype).forEach(name => {
            derivedCtor.prototype[name] = baseCtor.prototype[name]
        })
    })
}
````

###### 解决this指向报错问题

```` tsx
export type UserState = {
    name: string
    password: string
}

export interface User {
    state: UserState
    userInfo: string
    loginAction: (payload: { name: string, password: string }) => void
}



import { User, UserState } from "./user"

export default class actions {
    // 声明this的属性
    loginAction(this: User, payload: UserState): void {
        this.state.name = payload.name
        this.state.password = payload.password
        console.log(this)
        console.log('登录成功')
    }
}

````

### hooks

#### 在hook中使用获取路由的方法

```` jsx
需要导入这三个方法
import { useLocation,useHistory,useParams } from 'react-router-dom'

执行方法获取参数即可
const { pathname } = useLocation()
console.log(pathname)
````

