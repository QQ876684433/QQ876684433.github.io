---
title: withRouter的作用和用法
date: 2019-07-14 22:43:29
categories:
- 前端
tags:
- React-Router
- React
---

# withRouter的作用和用法

> 参考：https://www.cnblogs.com/luowenshuai/p/9526341.html

## **作用**

把不是通过路由切换过来的组件中，将react-router 的 history、location、match 三个对象传入props对象上

默认情况下必须是经过路由匹配渲染的组件才存在this.props，才拥有路由参数，才能使用编程式导航的写法，执行this.props.history.push('/detail')跳转到对应路由的页面

然而不是所有组件都直接与路由相连（通过路由跳转到此组件）的，当这些组件需要路由参数时，使用withRouter就可以给此组件传入路由参数，此时就可以使用this.props

## **如何使用withRouter**

比如app.js这个组件，一般是首页，不是通过路由跳转过来的，而是直接从浏览器中输入地址打开的，如果不使用withRouter此组件的this.props为空，没法执行props中的history、location、match等方法。

我就通过在App.js组件中使用withRouter来简单介绍一下：

设置withRouter很简单只需要两步：（1）引入  （2）将App组件 withRouter() 一下

```jsx
import React,{Component} from 'react'
import {Switch,Route,NavLink,Redirect,withRouter} from 'react-router-dom' //引入withRouter
import One from './One'
import NotFound from './NotFound'
class App extends Component{
    //此时才能获取this.props,包含（history, match, location）三个对象
    console.log(this.props);  //输出{match: {…}, location: {…}, history: {…}, 等}
    render(){return (<div className='app'>
            <NavLink to='/one/users'>用户列表</NavLink>
            <NavLink to='/one/companies'>公司列表</NavLink>
            <Switch>
                <Route path='/one/:type?' component={One} />
                <Redirect from='/' to='/one' exact />
                <Route component={NotFound} />
            </Switch>
        </div>)
    }
}
export default withRouter(App);  //这里要执行一下WithRouter
```

## **介绍一个简单应用**

可以根据路由切换浏览器的title属性，对props.history进行监听，切换路由的时候获取当前的路由路径，同时可以根据不同的路由设置不同的浏览器title标题。

仍然是App.js组件：

```jsx
import React,{Component} from 'react'
import {Switch,Route,NavLink,Redirect,withRouter} from  'react-router-dom'
import One from './One'
import NotFound from './NotFound'
class App extends Component{
        constructor(props){
                super(props);
                props.history.listen((location)=>{  //在这里监听location对象
                        console.log(location.pathname);  //切换路由的时候输出"/one/users"和"/one/companies"
                        switch(location.pathname){   //根据路径不同切换不同的浏览器title
                                case '/one/users' : document.title = '用户列表'; break;
                                case '/one/companies' : document.title = '公司列表'; break;
                                default : break;
                        }
                })
        }
        render(){
                return (<div className='app'>
                        <NavLink to='/one/users'>用户列表</NavLink>
                        <NavLink to='/one/companies'>公司列表</NavLink>
                        <Switch>
                                <Route path='/one/:type?'  component={One} />
                                <Redirect from='/' to='/one' exact />
                                <Route component={NotFound} />
                        </Switch>
                </div>)
        }
}
export default withRouter(App);
```

## **如果使用编程式导航**

this.props.history.push('/detail') 去跳转页面，但是报 this.props.history 错误 undefined，请在此组件中使用 withRouter 将 history 传入到 props上。