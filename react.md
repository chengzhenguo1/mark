

# React

#### 类组件用法

需要类继承 React.Component
并且需要constructor中调用super

##### 改变变量

需要调用setState

`跟vue中直接改变不一样，需要手动调用  *this*.setState({msg: 'msg'});



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

