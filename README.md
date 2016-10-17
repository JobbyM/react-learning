# react-learning
# react 入门
## 参考文档

	1. https://facebook.github.io/react/
	2. http://www.ruanyifeng.com/blog/2015/03/react  React 入门实例教程


## 入门

	1. 在github 上创建库 https://github.com/JobbyM/react-learning
	2. 在本地建立git
	3. 本地访问 http://localhost/nodejs/react-learning/test01/index.html


## HTML 模版
test01
```jsx
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>test01</title>
  <script src="../build/react.js"></script>
  <script src="../build/react-dom.js"></script>
  <script src="../build/browser.min.js"></script>
</head>
<body>
  <div id="example"></div>
  <script type="text/babel">
    ReactDOM.render(
      <h1>Hello, world!</h1>,
      document.getElementById('example')
    )
  </script>
</body>
</html>
```

React 使用JSX 语法，跟JavaScript 不兼容。凡是使用JSX 的地方，都要加上type=“text/babel”。
Browser.js 的作用是将JSX 的语法转为JavaScript 语法。

## ReactDOM.render()
ReactDOM.render 是React 的最基本方法，用于将模版转为HTML 语言，并插入指定的DOM 节点。

## JSX 语法
HTML 语言直接写在JavaScript 语言中，不加任何引号，这就是JSX 语法，它允许HTML 与JavaScript 的混写。
参考 https://facebook.github.io/react/docs/jsx-in-depth.html

test02
JSX 的基本语法规则：遇到HTML 标签（以< 开头），就用HTML 规则解析；遇到代码块（以 { 开头），就用JavaScript 规则
解析。

test03
JSX 允许直接在模版中插入一个变量。如果这个变量是一个数组，则会展开这个数组的所有成员

## 组件
React 允许将代码封装成组件（component），然后像插入普通HTML  标签一样，在网页中插入这个组件。React.createClass
方法就用于生成一个组件类
test04

```jsx
    var HelloMessage = React.createClass({
      render: function(){
        return <div>Hello {this.props.name}</div>
      }
    });

    ReactDOM.render(
      <HelloMessage  name="Alice"/>,
      document.getElementById('example')
    )
```
上述代码中，变量HelloMessage 就是一个组件类。模版插入<HelloMessage /> 时，会自动生成HelloMessage 的一个实例（
下文的“组件”都指组件类的实例）。所有组件必须有自己的render 方法，用于输出组件。

注意，组件类的第一个字母必须大写，否则会报错，比如HelloMessage 不能写成helloMessage。另外，组件类只能包含一个
顶层标签，否则也会报错。

组件的用法与原生的HTML 标签完全一致，可以任意加入属性，比如<HelloMessage  name="Alice"/>，就是HelloMessage
组件加入一个name 属性，值为Alice。组件的属性可以在组件类的this.props 对象上获取，比如name 属性就可以通过
this.props.name 读取。

添加组件属性，有一个地方需要注意，就是class 属性需要写成className，for 属性需要写成htmlFor，这是因为class 和for 是
JavaScript 的保留字

## this.props.children
this.props 对象的属性与醉驾案的属性一一对应，但是有一个例外，就是this.props.children 属性。它表示组件的所有子节点
test05

这里需要注意，this.props.children 的值有三种情况：如果当前组件没有子节点，它就是undefined；如果有一个子节点，数据类
型是object；如果有多个子节点，数据类型就是array。所以，处理this.props.children 的时候需要小心。

React 提供一个工具方法React.Children 来处理this.props.children 。我们可以用React.Children.map 来遍历子节点，而不用担心
this.props.children 的数据类型是undefined 还是object。

## PropTypes
组件的属性可以接受任意值，字符串、对象、函数等等都可以。有时，我们需要一种机制，验证别人使用组件时，提供的参数是否
符合要求

组件类的PropTypes 属性，就是用来验证组件实例的属性是否符合要求。
test06
```jsx
    var MyTitle = React.createClass({
      propTypes: {
        title: React.PropTypes.string.isRequired
      },

      render: function(){
        return <h1> {this.props.title} </h1>
      }
    })
```
上面的MyTitle 组件有一个title 属性。PropTypes 告诉React，这个title 属性是必须的，而且它的值必须是字符串。

此外，getDefaultProps 方法可以用来设置组件属性的默认值。

## 获取真实的DOM 节点
组件并不是真实的DOM 节点，而是存在于内存之中的一种数据结构，叫做虚拟DOM（Virtual DOM）。只有当它插入文档以后，
才会变成真实的DOM。根据React 的设计，所有的DOM 变动，都先在虚拟DOM 上发生，然后再将实际发生变动的部分，反映在
真实DOM 上，这种算法叫做DOM diff，它可以极大提高网页的性能表现。

但是，有时需要从组件获取真实DOM 的节点，这时就要用到ref 属性 test07
```jsx
    var MyComponent = React.createClass({
      handleClick: function(){
        this.refs.myTextInput.focus();
      },

      render: function(){
        return (
          <div>
            <input type="text" ref="myTextInput" />
            <input type="button" value="Focus text input" onClick={this.handleClick} />
          </div>
        )
      }
    })

    ReactDOM.render(
      <MyComponent />,
      document.getElementById('example')
    )
```
上面代码中，组件MyComponent 的子节点有一个文本输入框，用于获取用户的输入。这时就必须获取真实的DOM 节点，虚拟
DOM 是拿不到用户输入的。为了做到这一点，文本输入框必须有一个ref 属性，然后this.refs.[refName] 就会返回这个真实的
DOM 节点。

需要注意的是，由于this.refs.[refName] 属性获取的是真实DOM，所以必须等到虚拟DOM 插入文档以后，才能使用这个属性，
否则会报错。上面代码中，通过为组件指定Click 事件的回调函数，确保了只有等到真实DOM 发生Click 事件之后，才会读取
this.refs.[refName] 属性。

React 组件支持很多事件，除了Click 事件以外，还有KeyDown、Copy、Scroll 等。

## this.state
组件免不了与用户互动，React 的一大创新，就是将组件看成是一个状态机，一开始有一个初始状态，然后用户互动，导致状态变
化，从而触发重新渲染UI test08

```jsx
    var LikeButton = React.createClass({
      getInitialState: function(){
        return {liked: false}
      },

      handleClick: function(){
        this.setState({liked: !this.state.liked})
      },

      render: function(){
        var text = this.state.liked ? 'like' : 'haven\'t liked'
        return (
          <p onClick={this.handleClick}>
            You {text} this. Click to toggle.
          </p>
        )
      }
    })

    ReactDOM.render(
      <LikeButton />,
      document.getElementById('example')
    )
```

上面代码是一个LikeButton 组件，它的getInitialState 方法用于定义初始状态，也就是一个对象，这个对象可以通过this.state
属性读取。当用户点击组件，导致状态变换，this.setState 方法就修改状态值，每次修改后，自动调用this.render 方法，再次
渲染组件。

由于this.props 和this.state 都用于描述组件的特性，可能会产生混淆。一个简单的区分方法是，this.props 表示那些一旦定义，
就不再改变的特性，而this.state 是会随着用户互动而产生变化的特性。

##　表单
用户在表单填入的内容，属于用户跟组件的互动，所以不能用this.props 读取 #test09
```jsx
    var Input = React.createClass({
      getInitialState: function(){
        return {
          value: 'Hello!'
        }
      },

      handleChange: function(event){
        this.setState({
          value: event.target.value
        })
      },

      render: function(){
        var value = this.state.value;
        return (
          <div>
            <input type="text" value={value} onChange={this.handleChange} />
            <p>{value}</p>
          </div>
        )
      }
    })

    ReactDOM.render(
      <Input />,
      document.getElementById('example')
    )
```

上面代码中，文本输入框的值，不能用this.props.value 读取，而要定义一个onChange 事件的回调函数，通过event.target.value
读取用户输入的值。textarea 元素、select元素、radio 元素都属于这种情况。

## 组件的声明周期
组件的声明周期分成三个状态：

	1. Mounting：已插入真实DOM
	2. Updating：正在被重新渲染
	3. Unmounting：已移出真实DOM


React 为每个状态都提供了两种处理函数，will 函数在进入状态之前调用，did 函数在进入状态之后调用，三种状态共计五种处理
函数

	1. componentWillMount()
	2. componentDidMount()
	3. componentWillUpdate(object nextProps, object nextState)
	4. componentDidUpdate(object preProps, object preState)
	5. componentWillUnmount()


此外，React 还提供两种特殊状态的处理函数

	1. componentWillReceiveProps(object nextProps); 已加载组件收到新的参数时调用
	2. shouldComponentUpdate(object nextProps, object nextState); 判断组件是否重新渲染时调用


这些方法的详细说明，可以参考官方文档https://facebook.github.io/react/docs/component-specs.html#lifecycle-methods

下面是一个例子 test10
```jsx
    var Hello = React.createClass({
      getInitialState: function(){
        return {
          opacity: 1.0
        }
      },

      componentDidMount: function(){
        this.timer = setInterval(function(){
          var opacity = this.state.opacity;
          opacity -= .05;
          if(opacity < 0.1){
            opacity = 1.0
          }
          this.setState({
            opacity: opacity
          })
        }.bind(this), 100)
      },

      render: function(){
        return (
          <div style={{opacity: this.state.opacity}}>
            Hello {this.props.name}
          </div>
        )
      }
    })

    ReactDOM.render(
      <Hello name="world" />,
      document.getElementById('example')
    )
```

上面代码在Hello 组件加载以后，通过componentDidMount 方法设置一个定时器，每隔100 毫秒，就重新设置组件的透明度，
从而引发重新渲染。

另外，组件的style 属性的设置方式也值得注意，不能写成
```jsx
style="opacity: {this.state.opacity};"
```

而要写成
```jsx
style={{opacity: this.state.opacity}}
```

这是因为React 组件样式 https://facebook.github.io/react/tips/inline-styles.html 是一个对象，所以第一重大括号表示这是
JavaScript 语法，第二重大括号表示样式对象

## Ajax
组件的数据来源，通常是通过Ajax 请求从服务器获取，可以使用componentDidMount 方法设置Ajax 请求，等到请求成功，再
用this.setState 方法重新渲染UI test11

```jsx
    var UserGist = React.createClass({
      getInitialState: function(){
        return {
          username: '',
          lastGistUrl: ''
        }
      },

      componentDidMount: function(){
        $.get(this.props.source, function(result){
          var lastGist = result[0];
          if(this.isMounted()){
            this.setState({
              username: lastGist.owner.login,
              lastGistUrl: lastGist.html_url
            })
          }
        }.bind(this))
      },

      render: function(){
        return (
          <div>
            {this.state.username}'s last gist is <a href={this.state.lastGistUrl}>here</a>.
          </div>
        )
      }
    });

    ReactDOM.render(
      <UserGist source="https://api.github.com/users/octocat/gists"/>,
      document.getElementById('example')
    )
```
上面代码使用jQuery 完成Ajax 请求，这是为了便于说明。React 本身没有任何依赖，完全可以不用jQuery，而使用其他库。

我们甚至可以把一个Promise 对象传入组件，请看 test12
```jsx
    ReactDOM.render(
      <RepoList
        promise={$.getJSON('https://api.github.com/search/repositories?q=javascript&sort=stars')}
      />,
      document.getElementById('example')
    );
```

上面代码从Github 的API 抓取数据，然后将Promise 对象作为属性，传给RepoList 组件。

如果Promise 对象正在抓取数据（pending 状态），组件显示“正在加载”；如果Promise 对象报错（rejected 状态），组件
显示报错信息；如果Promise 对象抓取数据成功（fullfilled 状态），组件显示获取的数据

``jsx
    var RepoList = React.createClass({
      getInitialState: function() {
        return { loading: true, error: null, data: null};
      },

      componentDidMount() {
        this.props.promise.then(
          value => this.setState({loading: false, data: value}),
          error => this.setState({loading: false, error: error}));
      },

      render: function() {
        if (this.state.loading) {
          return <span>Loading...</span>;
        }
        else if (this.state.error !== null) {
          return <span>Error: {this.state.error.message}</span>;
        }
        else {
          var repos = this.state.data.items;
          var repoList = repos.map(function (repo, index) {
            return (
              <li key={index}>
                <a href={repo.html_url}>{repo.name}</a> ({repo.stargazers_count} stars) <br/> {repo.description}
              </li>
            );
          });
          return (
            <main>
              <h1>Most Popular JavaScript Projects in Github</h1>
              <ol>{repoList}</ol>
            </main>
          );
        }
      }
    });
```
