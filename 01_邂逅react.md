# 我们来邂逅一下react.... #

----------
**React必须依赖三个库：**

- react：包含react所必须的核心代码
- react-dom：react渲染在不同平台所需要的核心代码
- babel：将jsx转换成React代码的工具
- **React和Babel的关系：**
  - 默认情况下开发React其实可以不使用babel。
  - 但是前提是我们自己使用 `React.createElement` 来编写源代码，它编写的代码非常的繁琐和可读性差。
  - 那么我们就可以直接**编写jsx（JavaScript XML）的语法，并且让babel帮助我们转换成React.createElement**



## &nbsp;&nbsp;&nbsp;1.1 首先是输出hellowrod ##

```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Document</title>
</head>
<body>
<div>Header</div>
<div id="app">dafdasf</div>
<div>Footer</div>

<!-- 添加React的依赖 -->
<script src="https://unpkg.com/react@16/umd/react.development.js" crossorigin></script>
<script src="https://unpkg.com/react-dom@16/umd/react-dom.development.js" crossorigin></script>
<script src="https://unpkg.com/babel-standalone@6/babel.min.js"></script>
<!-- crossorigin:因为我们引用了远程的js文件，文件内部可能代码有问题。如果希望在本地这里显示出来错误。 -->

<!-- 注意事项: 使用jsx, 并且希望script中的jsx的代码被解析, 必须在script标签中添加一个属性  -->
<script type="text/babel">
let message = "Hello World";
ReactDOM.render(<h2>{message}</h2>, document.getElementById("app"))
//render(渲染内容，挂载的对象【一般给个id】)，渲染的内容就会覆盖掉id='app'的内容
 </script>
</body>
</html>
```


----------
## &nbsp;&nbsp;&nbsp;1.2 helloword按钮实现 ##
```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Document</title>
</head>
<body>
<div>Header</div>
<div id="app"></div>
<div>Footer</div>

<!-- 添加React的依赖 -->
<script src="../importjs/react.development.js"></script>
<script src="../importjs/react-dom.development.js"></script>
<script src="../importjs/babel.min.js"></script>

<!-- 注意事项: 使用jsx, 并且希望script中的jsx的代码被解析, 必须在script标签中添加一个属性  -->
<script type="text/babel">
//  jsx特点：多个标签最外层（根）只能有一个标签
let message = "Hello World";

function btnClick(){
    message = '我被改变了的内容...';
    //message不会同时更新的，只有重新渲染才能
    //更新数据需要重新渲染一次
    render();
}

function render(){
    ReactDOM.render(
        <div>
          <h2>{message}</h2>
        <button onClick={btnClick}>改变文本</button>
        </div>,
        document.getElementById("app"))
}
render();
// 页面加载时渲染一次dom
</script>
</body>
</html>
```

----------
## &nbsp;&nbsp;&nbsp;1.3 helloword-组件化实现 ##
```html
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Document</title>
</head>
<body>
<div id="app"></div>

<!-- 添加React的依赖 -->
<script src="../importjs/react.development.js"></script>
<script src="../importjs/react-dom.development.js"></script>
<script src="../importjs/babel.min.js"></script>

<script type="text/babel">
// 我们想把message和按钮封装成一个组件叫做App
class App extends React.Component{
    constructor(){
      super();
    //必须先调用下super，初始化父类的实例，才能使用this
    //   this.message = 'hellow world'  
    //在构造函数声明了message，别的地方才可以this.message使用
    //如果想更改数据时，页面也跟着改变数据。要定义在state里
     this.state = {
        message:'hello,world'
      }
    }
    render(){ 
        return (
            <div>
                <h2>{this.state.message}</h2>
                <button onClick={this.btnClick.bind(this)}>改变文本</button>
            </div>
        )
    }
    btnClick(){
        console.log('按钮发生了点击..');
        console.log(this);//undefined
    /*  内部回调的时候拿不到this。 btnClick.apply(undefined)
         this.message = '改变后的内容...'
     Cannot set property 'message' of undefined */

   /*   this.render(); 这里调用render没用，相当于把render函数的
    返回值返回给你 。虚拟dom不知道你要生成最新的jsx。不要手动调用render()
    */
      this.setState({
          message:'66666'
      })
      //这里有点像小程序。内部会进行更新操作
    }
}  

    ReactDOM.render(
        <App/>,
        document.getElementById("app"))
</script>
</body>
</html>
```

- **ReactDOM.render函数：**

- - 这里我们已经提前定义一个id为app的div
  - 这里我们传入了一个h2元素，后面我们就会使用React组件
    - 参数一：传递要渲染的内容，这个内容可以是HTML元素，也可以是React的组件
    - 参数二：将渲染的内容，挂载到哪一个HTML元素上



在组件中的数据，可以分成类：

