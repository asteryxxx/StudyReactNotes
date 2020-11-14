什么是HOOk?

*Hook* 是 React 16.8 的新增特性。它可以**让你在不编写 class 的情况下使用 state 以及其他的 React 特性**

------

# 为什么需要Hook?

**Class组件**可以定义自己的state，用来保存组件自己内部的状态。可以有自己的生命周期，可以在对应的生命周期完成自己的逻辑。比如在componentDidMount中发送网络请求，并且该生命周期只会执行一次。在componentDidMount中可能会包含大量的逻辑代码，包括网络请求，一些事件的监听。业务增多就会很复杂。**Hook 将组件中相互关联的部分拆分成更小的函数（比如设置订阅或请求数据），而并非强制按照生命周期划分。你还可以使用 reducer 来管理组件的内部状态，使其更加可预测**

**函数组件**，每次调用都会产生新的临时变量。如果在函数中发送网络请求，意味着每次重新渲染都会重新发送一次网络请求。**函数组件在重新渲染时，整个函数都会被执行**，似乎没有什么地方可以只让他们调用一次。

## 初体验Hook

```js
import React, {useState} from 'react'

export default function CounterHook() {
    /* Hook:useState
        > 本身是一个函数，来自react包 
        > 元素一：当前状态的值（第一调用为初始化值）；
        > 元素二：设置内部的状态值的函数；
        > 参数：初始化值，如果不设置为undefined；
        > 返回值：数组，包含两个元素；
    */
   const [count, setCount] = useState(0)
    return ( 
        <div>
          <h2>当前计数:{count}</h2>
          <button onClick={e => setCount(count+1)}>+1</button>
          <button onClick={e => setCount(count-1)}>-1</button>
        </div>
    )
}

```

```js
import React from 'react'
import CounterHook from './01_体验hooks/01_function-hook'

export default function App() {
    return (
        <div>
            <CounterHook/>
        </div>
    )
}
```

注意：不能直接修改state，render函数不会发生改变的。只有通过setState来修改数据才会触发render去拿到最新的state来显示

**这里对useState解析一下：**

- 参数：初始化值，如果不设置则为undefined；

- 返回值：数组，包含两个元素

  > 元素1：当前状态的值（第一调用为初始值）

  > 元素2：设置状态值的函数

  

**<u>有两个额外的规则：</u>**

1）只能在**函数最外层**调用hook，**不要在循环、条件判断或者子函数调用**

2）只能在**React的函数组件**中调用hook。不要在其他js函数中调用。

------

### **01_****多个状态的使用**

```js
import React, {useState} from 'react'

export default function MultiHookState() {
    const [count, setcount] = useState(0);
    const [age, setage] = useState(18);
    const [friends, setfriends] = useState(['kobe','james'])

    return (
        <div>
            <h2>当前计数:{count}</h2>
             <h2>我的年龄:{age}</h2>
             <ul>
                 {
                     friends.map((item,index)=>{
                       return <li>{item}</li>
                     })
                 }
             </ul>
        </div>
    )
}
```

![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\1.png)

------

### **02_****复杂状态的修改**

```react
import React, {useState} from 'react'

export default function ComplexHookState() {
    const [friends, setfriends] = useState([
        '彭于晏','吴彦祖'
    ])
    const [students, setstudents] = useState([
        {id:110,name:'why',age:18},
         {id:111,name:'lx',age:28},
          {id:112,name:'zhh',age:38}
    ])
    function addFriend(){
        //【不推荐做法】
        friends.push('朋友2');
        setfriends(friends);
    //react内部在决定要不要重新渲染的时候，会判断state的值和原来
   //是不是相等的，相等就不需要返回新的jsx。这种方式界面不会重新刷新
}
    function stuAgewithIndex(index){
        const newstudents = [...students];
        newstudents[index].age +=1 ;
        setstudents(newstudents);
    }
    return (
        <div>
            <h2>好友列表：</h2>
             <ul>
                 {
                     friends.map((item,index)=>{
                       return <li key={index}>{item}</li>
                     })
                 }
             </ul>
            <button onClick={e=>setfriends([...friends,'lx'])}>添加朋友</button> 
          <button onClick={e=>addFriend}>添加朋友2</button> 
               <h2>学生列表：</h2>
          <ul>
                 {
                     students.map((item,index)=>{
                       return <li key={item.id}>
                           <span>
                               名字：{item.name}
                                年龄：{item.age}
                           </span>
                           <button onClick={e => stuAgewithIndex(index)}>age+1</button>
                                </li>
                     })
                 }
             </ul>
        </div>
    )
}

```

![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\3.png)

**这里有个注意的地方：**

这里***初始化useState()还可以传入一个函数***，如果初始化状态是数字，布尔值等，不会有性能问题。如果是动态获取的数据，就要传入一个函数。可以为useState(computeInitialState)提供一个函数来使用状态的延迟初始化：

![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\2.png)

这里getInitialState（）**仅在初始化渲染的时候执行一次**，以获得初始状态，在以后的组件渲染中，不会再调用getInitialState（），从而跳过昂贵的操作

------

**如果我们通过setState修改对象或者数组的元素有以下几种情况：**

```js
		//修改对象中的某个属性
         this.setState({
             object: {...object, key: value}
        });
        //删除数组
        array.splice(0, 1);
        this.setState({
             array
        });
        //删除数组尾部
        array.splice(array.length - 1);
        this.setState({
             array
        });
        //删除数组任意一项
        array.splice(index, 1);
        this.setState({
             array
        });
        //数组尾部添加一项
        this.setState({
             array: [...array, item]
        });
        //数组头部添加元素
        this.setState({
            array:[item,...array]
        })
        //数组中任意位置添加一项
        array.splice(index,0,item);
        this.setState({
            array
        })
     /*    const months = ['Jan', 'March', 'April', 'June'];
        months.splice(1, 0, 'Feb');
         Array ["Jan", "Feb", "March", "April", "June"] */

        //修改数组中任意一项中值
        function updateArrayItem(index, key, value) {
        this.setState({
            array: array.map((item, _index) => _index == index ?
             {...item, [key]: value} : item)
          });
        }
        //上面的例子
        this.state = {
        listData: [
            {name: "小坏", age: "20"},
            {name: "小不", age: "21"},
        ],
        obj: {
            name: "小坏",
            age: "22",
        }
    }

        change = (index) => {
            const listData = [...this.state.listData];   //复制数组--浅拷贝
            const obj = Object.assign({}, this.state.obj, { age: "21" });

            this.setState({
                listData: listData.map((item, idx) => idx === index ? {...item, name:  "陈小坏"} : item),
                obj: obj,                         
            })
        }
```

<center>补充知识: </center>

```react
 function useState<S>(initialState: S | (() => S)): [S, Dispatch<SetStateAction<S>>];
```

<S>是泛型，传入的参数：initialState（可以传S泛型或者箭头函数，返回S）

修改下**传入的参数为函数**：

```react
const [count, setCount] = useState(()=>10)
```

![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\4.png)

我们发现初始化值变成10了。

```react
[S, Dispatch<SetStateAction<S>>]
```

返回值是数组，数组类型有两个东西。其实第二个参数就是一个函数。

**我们点开Dispatch查看**

```react
type Dispatch<A> = (value: A) => void;
```

我们发现他等于一个函数，这个函数接受一个参数**(value:** **A)这是一个泛型**。这个函数没有返回值void。

```react
type SetStateAction<S> = S | ((prevState: S) => S);
```

我们发现他是一个联合类型，可以等于S（例子就是数字）。还可以是一个函数。函数本身拿到**上一次的值prevState，并且返回新的State**。我们可以修改一下代码：

```react
<button onClick={e => setCount(pre => pre+10)}>+10</button>
```

![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\5.png)

第二种方法会把前一次的值传给我们，我们就可以在前一次值的基础上进行加10。

------

## **Effect Hook**

类似网络请求，手动更新DOM，一些事件的监听，都是React更新DOM的一些副作用（Side Effects）。`useEffect` 就是一个 Effect Hook，给函数组件增加了操作副作用的能力。它跟 class 组件中的 `componentDidMount`、`componentDidUpdate` 和 `componentWillUnmount` 具有相同的用途，只不过被合并成了一个 API。

**假如我们有一个需求：页面的title总是显示counter的数字**

*1）Class组件实现方法：*

```react
import React, { PureComponent } from 'react'

export default class Classtitle extends PureComponent {
    constructor(props){
            super(props);
            this.state = {
                counter:0
            }
        }
componentDidMount(){
//第一次渲染完成要执行
        document.title = this.state.counter
    }
    componentDidUpdate(){
        //更新之后，每当render完成就会执行这个生命周期
       document.title = this.state.counter 
    }
    render() {
        return (
            <div>
                <h2>当前计数：{this.state.counter}</h2>
                <button onClick={e => this.add()}>+1</button>
            </div>
        )
    }
    add(){
        this.setState({
            counter:this.state.counter+1
        })
    }
}
```

*2）Effect方法实现方法：*

```react
import React,{useEffect,useState} from 'react'

export default function Hooktitle() {
    const [counter, setcounter] = useState(0);
    useEffect(() => {
        document.title = counter;
    })
    /* 这个函数默认等到组件渲染到真实dom的时候会回调，不管函数
    是第一次渲染还是发生更新之后的渲染都会执行的，相当于把不管
    是第一次还是后面的更新都会回调。合二为一了。 */
    return (
         <div>
                <h2>当前计数：{counter}</h2>
                <button onClick={e => setcounter(counter + 1)}>+1</button>
         </div>
    )
}
```

### **模糊生命周期订阅，卸载**

```react
import React,{useEffect,useState} from 'react'

export default function EffectcancelDemo() {
    const [counter, setcounter] = useState(0);
    useEffect(() => {
        console.log('订阅了一些事件。。。');
        return ()=>{
            //组件卸载的时候/组件更新的时候。来到这个回调函数
            console.log('卸载了宝贝...');
        }
    },[])
    return (
        <div>
            EffectcancelDemo
                <h2>当前计数：{counter}</h2>
   <button onClick={e => setcounter(counter + 1)}>+1</button> 
        </div>
    )
}
```

```react
export default function App() {
    const [show, setshow] = useState(true)
    return (
        <div>
            {show && <Effectdemo/>}
            <button onClick={e => setshow(!show)}>切换显示</button>
        </div>
    )
}
```

![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\6.png)

当我们点击+1的时候，也会触发订阅/卸载的事件。我们并不想这样来回切换的。我们**可以传入第二个参数[]**。

```react
 useEffect(() => {
        console.log('订阅了一些事件。。。');
        return ()=>{
            //组件卸载的时候/组件更新的时候。来到这个回调函数
            console.log('卸载了宝贝...');
        }
    },[])
```

![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\7.png)

------

### **使用多个Effect**

默认情况下，如果第二个参数不写的话。当组件重新渲染了，所有的useEFfect都会执行一遍。

React 将<font color=blue>**按照 effect 声明的顺序依次调用组件中的每一个effect**</font>

<font color=blue>**useEffect实际上有两个参数:**</font>

参数一：执行的回调函数；

参数二：该useEffect在哪些state发生变化时，才重新执行；**（受谁的影响）**

<font color=red>**不希望依赖任何的内容时，也可以传入一个空的数组** **[]**</font>

```react
export default function MultiUseEffect() {
    const [counter, setcounter] = useState(0);
    useEffect(() => {
        console.log("网络请求");
    },[]);

    useEffect(() => {
        console.log("修改DOM");
        //修改title的时候是依赖counter的。第二个参数传依赖的变量
    },[counter])

    useEffect(() => {
        console.log("事件监听");
    //空数组表示谁都不依赖，表示第一次渲染的时候执行。
//dom发生变化是不会影响到我的，因为我不依赖他。
        return () => {
         console.log("取消监听");
      //当卸载组件的时候就会触发取消监听
     }
    },[])

  return (
    <div>
      <h2>MultiUseEffect</h2>
       <h2>当前计数：{counter}</h2>
       <button onClick={e => setcounter(counter + 1)}>+1</button>
    </div>
  )
}
```

![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\8.png)

------

## Context Hook

![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\9.png)

```react
import Contexthook from './04_useContext使用/useContext使用'

export const UserContext = createContext();
export const ThemeContext = createContext();

export default function App() {
    return (
        <div>
            <UserContext.Provider value={{name:'zhh',age:18}}>
                <ThemeContext.Provider value={{fontSize:'18px',color:'red'}}>
                    <Contexthook/>
                </ThemeContext.Provider>
            </UserContext.Provider>
        </div>
    )
}
```

```react
import React,{useContext} from 'react'
import {
    UserContext,ThemeContext
}from '../App'
export default function useContextDemo() {
     const user = useContext(UserContext);
     const theme = useContext(ThemeContext);
     console.log(user,theme);
     //{name: "zhh", age: 18}
     //{fontSize: "18px", color: "red"}
    return (
        <div>
            useContext...
        </div>
    )
}
```

## useReducer

在某些场景下，如果state的处理逻辑比较复杂，我们**可以通过useReducer**来对其进行拆分；或者这次修改的state需要依赖之前的state时，也可以使用。

<font color=blue>**可以共享共同的reducer逻辑代码，但是state并不可以共享**</font>

```react
import React,{useState,useReducer} from 'react'

function reducer(state,action){
    switch (action.type) {
        case "increment":
            return {...state,counter:state.counter+1}
        case "decrement":
            return {...state,counter:state.counter-1} 
        default:
           return state;
    }
}
export default function Home() {
    //useReducer接受的第二个参数是初始化值
    //【如果我们传的是0，然后case "increment": return state+1 】
    const [state, dispatch] = useReducer(reducer, {
        //useReducer会返回state，返回当前状态的值
        //dispatch：改变某个值的时候dispatch某个action对象
        counter:0
    })
  return (
    <div>
      <h2>当前计数: {state.counter}</h2>
      <button onClick={e => dispatch({type: "increment"})}>+1</button>
      <button onClick={e => dispatch({type: "decrement"})}>-1</button>
    </div>
  )
}
```

<font color=red>用reducer把state做了替换。并不是对counter数据进行共享的</font>

****

我们可以把reducer.js进行拆分到单独的文件：

```react
export const  reducer = (state,action)=> {
    //纯函数
    switch (action.type) {
        case "increment":
            return {...state,counter:state.counter+1}
        case "decrement":
            return {...state,counter:state.counter-1} 
        default:
           return state;
    }
}
```

**我们创建一个profile.js看看会不会共享：**

```react
import React,{useState,useReducer} from 'react'

import{
    reducer
}from './reducer'

export default function profile() {
     const [state, dispatch] = useReducer(reducer, {
        counter:0
    })
  return (
    <div>
      <h2>当前计数: {state.counter}</h2>
      <button onClick={e => dispatch({type: "increment"})}>+1</button>
      <button onClick={e => dispatch({type: "decrement"})}>-1</button>
    </div>
  )
}
```

![10](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\10.png)

> **共享的仅仅是reducer函数里的处理逻辑。useReducer只是useState的一种替代品，并不能替代Redux。**

## useCallback

useCallback实际的**目的是为了进行性能的优化**。useCallback**会返回一个函数的** **memoized（记忆的）值**；在依赖不变的情况下，多次定义的时候，返回的值是相同的

```react
import React,{useState,useCallback} from 'react'

export default function CallbackHookDemo() {
    const [count, setcount] = useState(0)
    const increment =()=>{
        console.log('increment++++++');
        setcount(count+1);
    }
    //每次点击+1的按钮，increment函数就需要重新定义一次
    //因为他就是局部变量。
    const increment1 = useCallback(() => {
            console.log('increment1++++++');
            setcount(count+1);
     },[]);
    return (
        <div>
            CallbackHookDemo
              <h2>{count}</h2>
            <button onClick={increment}>优化前的：+1</button>
            <button onClick={increment1}>useCallback的：+1</button>
        </div>
    )
}
```

![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\11.png)

![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\12.png)

![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\13.png)

useCallback()**在没有存在任何依赖的情况下，返回的函数永远都是同一个值。**意味着调用的一直是原来的某个函数。**原先最早的值就是0，相当于一直引用着0的值。执行的时候返回的都是同一个函数，所以每次set都是1.**



**在真实开发里面，**是不是所有的函数都应该写成下列的形式，意味着当count在没有发生变化的情况，对increment1函数进行性能优化，但是并不是这样的。**因为在重新渲染的时候，创建新的匿名函数赋值给increment，会有一个函数创建的过程**；**increment1在重新渲染的时候，也有函数创建的过程**，只不过返回的值是相同的值（**因为有useCallback**），**还是要创建匿名函数传到useCallback函数里，所以没有任何的性能优化。<font color=blue>两个都有函数创建的过程</font>

```react
 const increment1 = useCallback(() => {
            console.log('increment1++++++');
            setcount(count+1);
     },[count]);
```



**我们来试下这个例子：**

```react
import React,{useState,useCallback,memo} from 'react'
const HYbutton = memo((props) =>{
    //函数组件
        console.log("hybutton被重新渲染了:"+props.title);
        return <button onClick={props.increment}>
            {props.title}HYbutton+1
                </button>
})
export default function CallbackHookdemo01() {
    console.log('CallbackHookdemo01被渲染...');

     const [count, setcount] = useState(0)
     const [show, setshow] = useState(true)

    const increment1 =()=>{
        console.log('increment1.....');
        setcount(count+1);
    }
    const increment2 = useCallback(() => {
            console.log('increment2.....');
            setcount(count+1);
     },[count]);
    return (
        <div>
            <h2>当前计数：{count}</h2>
            <HYbutton increment={increment1} title={"btn1"}/>
            <HYbutton increment={increment2} title={"btn2"}/>
            <button onClick={e => setshow(!show)}>切换显示</button> 
        </div>
    )
}
```

![14](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\14.png)

> **我们从结果图中**：会发现but1重新渲染了，btn2没有。每次给hybutton组件传过去的increment1**都是新的函数**，每次都是新的函数意味着**memo**是没有啥意义的。而increment2依赖count，通过show改变的时候，每次返回的都是相同的值，因为他用了useCallback,所以传给hybutton就是相同的值incre2，就没有重新渲染。

- 只要父组件渲染，子组件肯定会重新渲染的
- 我们show发生了切换，跟Button没关系的。所以不需要重新渲染的
- **如果是函数组件我们可以用**memo**，类组件就继承**Purecomponent。

> 函数组件**使用memo包裹会根据当前的props有没发生更新，浅层比较**。如果发生了更新就重新渲染。这就是 React.memo() 所做的事情，**它会检查接下来的渲染是否与前一次的渲染相同，如果两者是一样的，那么就会保留上一次的渲染结果**。

**<font color=blue>useCallback使用场景：</font>**在**将一个组件中的函数，传递给子元素进行回调使用时（**上面的例子就是将increment**传递给子组件调用**）**，使用useCallback对函数进行处理。useCallback最主要用于性能渲染的地方**应该是和memo结合起来，决定子组件是否需要重新渲染

 

------

## useMemo

useMemo**返回的也是一个 memoized（记忆的） 值**，不一定返回的是函数。

在依赖不变的情况下，多次定义的时候，返回的值是相同的；

##### 第一个使用场景：

```react
![15](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\15.png)import React,{useState} from 'react'
const calcuNum = (count)=>{
    console.log('calcuNum渲染...');
    let total = 0;
        for(let i =1 ;i<=count;i++){
            total += i;
        }
    return total;
}
export default function MemoHookDemo01() {
    console.log('MemoHookDemo01渲染...');
    const [count, setcount] = useState(10);
    const [show, setshow] = useState(true)

     return (
        <div>
            <h2>数字的和:{calcuNum(count)}</h2>
            <button onClick ={e => setcount(count +1)}>+1</button>
            <button onClick ={e => setshow(!show)}>切换显示</button> 
        </div>
    )
}
```

![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\15.png)

![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\16.png)

Count改变的时候，**传入的回调函数**才会执行。才会返回新的count。计算过程重新执行一遍。

##### 第二个场景：

```react
import React,{useState,useMemo, memo} from 'react'
const HYinfo = memo(props =>{
    console.log('hyinfo重新渲染了。。。。');
    return <h2>名字：{props.info.name}-----年龄：{props.info.age}</h2>
})
export default function MemoHookDemo02() {
    console.log('MemoHookDemo02重新渲染了。。');
    const [show, setshow] = useState(true)

    const info = {name :"lx",age:18};
    return (
        <div>
            <HYinfo info={info}/>
            <button onClick ={e => setshow(!show)}>切换显示</button> 
        </div>
    )
}
```

![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\17.png)

> 其实当前定义的info是局部变量，每次渲染都会定义新的info对象，所以传给子组件都是新的对象。浅层比较的时候发生info永远不一样，所以hyinfo会每次都重新渲染。

![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\18.png)

<center><font color=gray size=72>useMemo和usecallback区别</font></center>

UseMemo是**对自己的返回值**进行优化的，【返回值是对象，数字，函数都可以】。useCallback是**对传入的回调函数**进行优化【返回值是函数】。

useMemo返回**缓存的变量**，useCallback**返回缓存的函数**。

![19](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\19.png)

> **用Usememo实现了usecallback。**



## **UseRef**

返回一个ref对象，**返回的ref对象在组件的整个生命周期保持不变。**

常用的ref两种用法：

1.    用法1：引入dom元素
2.   用法2：保存一个数据，这个对象在整个生命周期可以保存不变

```react
import React, { useRef } from 'react'
class Testcpn extends React.PureComponent{
    render(){
        return <h2>Testcpn</h2>
    }
}
export default function RefhookDom() {
    const inputRef = useRef();
     const titleRef = useRef();
      const testcpn = useRef();

  const Operate = () => {
    titleRef.current.innerHTML = "我是lx哈哈哈";
    inputRef.current.focus();
    console.log(testcpn);
  }

  return (
    <div>
      <input type="text" ref={inputRef}/>
      <h2 ref={titleRef}>默认内容</h2>
      <Testcpn ref={testcpn}/>
      {/* 如果想把ref放到组件上，必须是类组件。 */}
      <button onClick={e => Operate()}>操作</button>
    </div>
  )
}
```

![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\20.png)

> **如果想把ref放到组件上，必须是类组件。**

------



##### **用法二：使用ref保存上一次的某一个值**

> 可以const numRef = useRef（10）往这里面传一个默认值，当前的数据就放到counterRef.current里面了。{numRef.current}就可以得到10.

**在组件的生命周期里面，**useRef返回的值总是相同

useRef可以想象成在ref对象中保存了一个.current的可变盒子；

useRef在组件重新渲染时，返回的依然是之前的ref对象，但是current是可以修改的

![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\21.png)

![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\22.png)

```react
import React, { useState, useEffect, useRef } from 'react';

let preValue = 0;
export default function UseRef02() {
    const [count, setCount] = useState(0);
    const NumRef = useRef(count);
//在组件的生命周期里面，useRef返回的值总是相同。
    useEffect(() => {
        NumRef.current = count;
    }, [count]);
//表示count一旦发生变化，重新渲染之后，就会执行useEffect代码
//因为当前useEffect依赖count。拿到NumRef.current,因为他永远
//保存的是count值。这里赋值是在页面渲染完之后。
  return (
    <div>
      <h2>count上一次的值: {NumRef.current}</h2>
      <h2>count这一次的值:{count}</h2>
      <button onClick={e => setCount(count + 10)}>+10</button>
    </div>
  )
}
```

<center>执行过程：</center>

> 点击+10，就会执行setCount函数，就重新渲染。因为{NumRef.current}永远记录的是不改变的值，就是0，然后这时候count已经变成10了，所以{count}显示10。然后页面渲染完了，【**因为count发生了改变（变成了10）就会执行到了useEffect函数**】，把当前count为10赋值给了{NumRef.current}，意味着当前的current就发生改变了，但是不会引起渲染。下次点击的时候上一次的值就是10，以此类推

![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\23.png)



## useImperativeHandle

先来回顾一下ref和forwardRef结合使用：通过forwardRef可以将ref转发到子组件；

子组件拿到父组件中创建的ref，绑定到自己的某一个元素中；

```react
import React, { forwardRef, useRef } from 'react'

const HYInput = forwardRef(
    (props,inputRef)=>{
    return <input type='text' ref={inputRef}/>
})
export default function ForwardRefDemo() {
    const inputRef = useRef();
    return (
        <div>
            <HYInput ref={inputRef}/>
            <button onClick={e=>inputRef.current.focus()}>
                聚焦
            </button>
        </div>
    )
}
```

**逐步解释：**

> 1. \1. 我们通过调用 **useRef()** 创建了一个 [React ref](https://react.docschina.org/docs/refs-and-the-dom.html) 并将其赋值给 inputRef 变量。
> 2. \2. 我们通过指定 ref 为 JSX 属性，将其向下传递给 <HYInput ref={inputRef}/
> 3. \3. React 传递 inputRef给 forwardRef 内函数 (props, **inputRef**) => ...，作为其第二个参数。
> 4. \4. 我们向下转发该 **inputRef**参数到 <input type='text' ref={inputRef}/>，将其指定为 JSX 属性。
> 5. \5. 当 ref 挂载完成，ref.current 将指向 < input > DOM 节点。



存在以下问题：前inputRef绑定的是dom元素，如果父组件想对dom元素做任何操作都是可以的。子组件只是希望能只做聚焦这一件事件。

useImperativeHandle 可以让你在使用 ref 时**自定义暴露给父组件的实例值**

```react
import React, { forwardRef, useRef,useImperativeHandle } from 'react'

const HYInput = forwardRef((props,ref)=>{
    const meRef = useRef();
    //在子组件里面有一个属于自己的inpuref
    useImperativeHandle(ref,()=>({
        //返回的对象绑定到meRef.current里面
        //接受两个参数，第一个参数是ref，第二个是回调函数并且返回一个对象
        focus:()=>{
            console.log('嘻嘻嘻嘻嘻嘻嘻嘻嘻........');
            meRef.current.focus();
            //meRef是子组件自己的ref
        },
        printHi:()=>{
            console.log('say hi');
        }
    }),[meRef.current])//当meRef.current需要更新才更新
    return <input type='text' ref={meRef}/>
})
export default function UseImperativehandleDemo() {
    const inputRef = useRef();
    return (
        <div>
            <HYInput ref={inputRef}/>
            <button onClick={e=>inputRef.current.focus()}>
                聚焦
            </button>
        </div>
    )
}
```

> 本来inputRef传递给了子组件的![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\24.png)
>
> ，然后子组件又把inputRef传递给了**useImperativeHandle****的****inputRef**
>
> 接着把useImperativeHandle第二个参数的返回的对象绑定到inputRef里面的current,所以我们再去拿ref拿到的就是自己自定义返回的对象。

![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\25.png)

当我们inputRef.current.focus()调用时，实际上调用的是第二个参数返回的对象的暴露的东西。

![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\26.png)

![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\27.png)

![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\28.png)



## useLayoutEffect

useLayoutEffect看起来和useEffect非常的相似，**区别：**

> useEffect会在渲染的内容**更新到**DOM上后执行，不会阻塞DOM的更新；
>
>  useLayoutEffect会在渲染的内容**更新到DOM**上之前执行，会阻塞DOM的更新；

如果我们希望在某些操作发生之后再更新DOM，那么应该将这个操作放到useLayoutEffect。

> <font color=blue>他在jsx内容返回之后，并不会直接渲染到真实dom上去， **他会先执行**useLayoutEffect的方法，有个回调函数是会有阻塞的。等函数执行完了，可能我们又改变了state，根据改变的最新的state，在渲染一下dom，更新到屏幕上去。【参考下面例子去理解这段话】</font>



**我们来看下面的一段代码：**

这段代码在开发中**会发生闪烁的现象**；

因为我们先将count设置为了0，那么DOM会被更新，并且会执行一次useEffect中的回调函数；在useEffect中我们发现count为0，又执行一次setCount操作，那么DOM会再次被更新，并且useEffect又会被执行一次，但是这时候count是随机数不等于0；

```react
import React,{useState,useEffect} from 'react'

export default function UseEffectcountDemo() {
    const [count, setcount] = useState(10)
    useEffect(() => {
        if(count===0){
            setcount(Math.random())
        }
    }, [count])
    //修改数字的时候改成0，会重新渲染，页面渲染完会执行回调
    //我们做了一个操作useEffect依赖count,然后页面又会重新渲染，会有闪烁的现象
    
    return (
        <div>
            <h2>数字：{count}</h2>
            <button onClick={e => setcount(0)}>修改数字</button>
        </div>
    )
}
```

改成如下代码：

```react
useLayoutEffect(() => {
        if(count===0){
            setcount(Math.random())
        }
    }, [count])
```



## **自定义hook**

自定义Hook**本质上只是一种函数代码逻辑的抽取**，严格意义上来说，它本身并不算React的特性。

##### 需求：所有的组件在创建和销毁时都进行打印

> ------组件被创建：打印+组件被创建了；
>
> ------组件被销毁：打印+组件被销毁了；

```react
import React,{useEffect,useState} from 'react'
const Home = (props) => {
    useEffect(() => {
        console.log('Home组件创建了...');
        return () => {
             console.log('Home组件销毁了...');
        }
    }, [])
    return <h2>home</h2>
}
const Profile = (props) => {
    useEffect(() => {
        console.log('Profile组件创建了...');
        return () => {
             console.log('Profile组件销毁了...');
        }
    }, [])
    return <h2>Profile</h2>
}
export default function CustomHookDemo() {
    useEffect(() => {
        console.log('CustomHookDemo组件创建了...');
        return () => {
             console.log('CustomHookDemo组件销毁了...');
        }
    }, [])
    return (
        <div>
            <Home/>
            <Profile/>
            <h2>CustomHookDemo</h2>
        </div>
    )
}
```

![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\29.png)

如果有多个组件需要打印，就意味着多了很多重复的代码逻辑，我们可能希望抽取到一个函数中。【**普通的函数中不能使用hook**】

```react
function loggingLife(name) {
  useEffect(() => {
    console.log(`${name}组件被创建了`);
    return () => {
      console.log(`${name}组件被销毁了`);
    }
  }, [])
}
```

![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\30.png)

函数以特殊的方式命名，**以 Use开头即可【大写开头】**

```react
import React,{useEffect,useState} from 'react'
const Home = (props) => {
    UseloggingLife("Home")
    return <h2>home</h2>
}
const Profile = (props) => {
    UseloggingLife("Profile")
    return <h2>Profile</h2>
}

function UseloggingLife(name) {
  useEffect(() => {
    console.log(`${name}组件被创建了`);
    return () => {
      console.log(`${name}组件被销毁了`);
    }
  }, [])
}
```

##### 应用2：Context共享：

```react
import React, { useState ,useContext, createContext} from 'react'
import {
    UserContext,ThemeContext
}from '../App'

export default function CustomContextDemoHook() {
    const user = useContext(UserContext);
    const theme = useContext(ThemeContext);
    console.log(user,theme);
    return (
        <div>
            
        </div>
    )
}
```

```react
export const UserContext = createContext();
export const ThemeContext = createContext();

export default function App() {
 return (
        <div>
 <UserContext.Provider value={{name:'lx',age:18}}>
                <ThemeContext.Provider value={"theme......"}>
                    <CustomContextDemoHook/>
                </ThemeContext.Provider>
            </UserContext.Provider>
        </div>
    )
}
```

如果组件需要用到context的地方要两个步骤：

> 1、导入**import** **{**
>
>   **UserContext,ThemeContext**
>
> **}from** **'../App'** 
>
> 2、  **const** **user** **=** **useContext(UserContext);**
>
>   **const** **theme** **=** **useContext(ThemeContext);**

如果多有个地方要拿到context,显然有点麻烦了。

**修改的代码：我们创建一个文件夹在src下：**

![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\31.png)

User-hook.js代码如下：

```react
import  { useContext} from 'react'
import {
  UserContext,ThemeContext
 }from '../App'
function UseUserContext(){
    const user = useContext(UserContext);
    const theme = useContext(ThemeContext);
    return [user,theme];
    //返回数组
}

export default UseUserContext;
```

需要的组件导入：

```react
import React, { useState ,useContext, createContext} from 'react'
import UseUserContext from '../hooks/user-hook'
export default function CustomContextDemoHook() {
    const  [user,theme] = UseUserContext();
    console.log(user,theme);
    return (
        <div>
            
        </div>
    )
}
```

##### 应用3：获取滚动的位置

```react
import React, { useEffect,useState } from 'react'

export default function CustomerHookGetScrollDemo() {
    const [scrollPosition, setScrollPosition] = useState(0);
    useEffect(() => {
        const handScroll = () =>{
            setScrollPosition(window.scrollY)         
        }
        document.addEventListener('scroll',handScroll);

        return () => {
            document.removeEventListener('scroll',handScroll);
        }
    }, [])

    return (
        <div style={{padding:'1000px'}}>
            <h2 style={{position:'fixed',left:0,top:0}}>
                滚动距离:{scrollPosition}</h2>
        </div>
    )
}
```

修改后的代码：

![](C:\Users\Administrator\Desktop\clone\react的md笔记\hookimages\32.png)

```react
import React, { useEffect,useState } from 'react'

function UseScrollPositio(){
    const [scrollPosition, setScrollPosition] = useState(0);
    useEffect(() => {
        const handScroll = () =>{
            setScrollPosition(window.scrollY)         
        }
        document.addEventListener('scroll',handScroll);
        return () => {
            document.removeEventListener('scroll',handScroll);
        }
    }, [])
    return scrollPosition;
}
export default UseScrollPositio;
```

在别的组件导入，就两行代码：

```react
import UseScrollPositio from '../hooks/scroll-position-hook'
const scrollPosition = UseScrollPositio();
```



##### **应用4：localstorage的存储**

```react
import React,{useState,useEffect} from 'react'

export default function CustomDataStorageHook() {
    const [name, setName] = useState(()=>{
     //我们要从localstorage中获取数据的，所以这里不能随便写
     //所以这里初始化数据，我们可以传入一个匿名函数   
       const name = (window.localStorage.getItem('name'));
       return name;
    })

    useEffect(() => {
        window.localStorage.setItem('name',JSON.stringify(name));
    }, [name])
    return (
        <div>
            <h2>名字:{name}</h2>
            <button onClick={e=> setName('lx')}>改变名字</button>
        </div>
    )
}
```

修改代码：

```react
import React, { useEffect,useState } from 'react'

function UseLocalStorange(key) {
  const [data, setData] = useState(() => {
    return JSON.parse(window.localStorage.getItem(key))
  });

  useEffect(() => {
    window.localStorage.setItem(key, JSON.stringify(data));
  }, [data]);
  return [data, setData];
}
export default UseLocalStorange;
```

```react
import React,{useState,useEffect} from 'react'
import UseLocalStorange from '../hooks/storage-hook'
export default function CustomDataStorageHook() {
    const [name, setName] = UseLocalStorange('name');
    
    return (
        <div>
            <h2>名字:{name}</h2>
            <button onClick={e=> setName('lx')}>改变名字</button>
        </div>
    )
}
```

