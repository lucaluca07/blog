#### 简介
##### jest
> Jest is a delightful JavaScript Testing Framework with a focus on simplicity.

##### Enzyme
Enzyme是Airbnb开源的React测试工具库库，它功能过对官方的测试工具库ReactTestUtils的二次封装，提供了一套简洁强大的 API。
<!-- more -->
#### 环境搭建
##### create-react-app:
```js
npm install --save enzyme enzyme-adapter-react-16 react-test-renderer
```
或者
```js
yarn add enzyme enzyme-adapter-react-16 react-test-renderer
```
从 Enzyme 3 开始，你需要安装 Enzyme 以及与你正在使用的 React 版本相对应的适配器。 （上面的例子使用 React 16 的适配器。）

还需要在 全局设置文件 中配置适配器：
```javascript
import { configure } from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

configure({ adapter: new Adapter() });
```
##### 配置文件说明
- setupFiles：配置文件，在运行测试案例代码之前，Jest会先运行这里的配置文件来初始化指定的测试环境
- moduleFileExtensions：代表支持加载的文件名
- testPathIgnorePatterns：用正则来匹配不用测试的文件
- testRegex：正则表示的测试文件，测试文件的格式为xxx.test.js
- collectCoverage：是否生成测试覆盖报告，如果开启，会增加测试的时间
- collectCoverageFrom：生成测试覆盖报告是检测的覆盖文件
- moduleNameMapper：代表需要被Mock的资源名称
- transform：用babel-jest来编译文件，生成ES6/7的语法

#### 基础语法
```js
test('描述', () => {
  expect('测试内容').toBe('期望结果');
});
```
- expect(value)：要测试一个值进行断言的时候，要使用expect对值进行包裹
##### 匹配器
- toBe(value)：使用Object.is来进行比较，如果进行浮点数的比较，要使用toBeCloseTo。
- not：用来取反
- toEqual(value)：用于对象的深比较
- toContain(item)：用来判断item是否在一个数组中，也可以用于字符串的判断
- toBeNull(value)：只匹配null
- toBeUndefined(value)：只匹配undefined
- toBeDefined(value)：与toBeUndefined相反
- toBeTruthy(value)：匹配任何使if语句为真的值
- toBeFalsy(value)：匹配任何使if语句为假的值
- toBeGreaterThan(number)： 大于
- toBeGreaterThanOrEqual(number)：大于等于
- toBeLessThan(number)：小于
- toBeLessThanOrEqual(number)：小于等于
- toMatch：用正则表达式匹配结果
- toThrow：匹配异常结果
- anything(value)：匹配除了null和undefined以外的所有值
- resolves：用来取出promise为fulfilled时包裹的值，支持链式调用
- rejects：用来取出promise为rejected时包裹的值，支持链式调用
- toHaveBeenCalled()：用来判断mock function是否被调用过
- toHaveBeenCalledTimes(number)：用来判断mock function被调用的次数
- assertions(number)：验证在一个测试用例中有number个断言被调用

[更多匹配器](https://jestjs.io/docs/zh-Hans/expect)

#### 基础语法实例
##### toBe
```js
test('two plus two is four', () => {
  expect(2 + 2).toBe(4);
});
```
##### not
```js
test('two plus two is four', () => {
  expect(2 + 2).not.toBe(5);
});
```
##### toEqual
```js
test('object assignment', () => {
  const data = {one: 1};
  data['two'] = 2;
  expect(data).toEqual({one: 1, two: 2});
});
```
##### toContain
```js
const shoppingList = [
  'diapers',
  'kleenex',
  'trash bags',
  'paper towels',
  'beer',
];

test('the shopping list has beer on it', () => {
  expect(shoppingList).toContain('beer');
  expect(new Set(shoppingList)).toContain('beer');
});
```

##### Enzyme的三种渲染方法

1. shallow：浅渲染。将组件渲染成虚拟DOM对象，只会渲染第一层，子组件将不会被渲染出来，使得效率非常高。不需要DOM环境， 并可以使用jQuery的方式访问组件的信息
2. render：静态渲染，它将React组件渲染成静态的HTML字符串，然后使用Cheerio这个库解析这段字符串，并返回一个Cheerio的实例对象，可以用来分析组件的html结构
3. mount：完全渲染，它将组件渲染加载成一个真实的DOM节点，用来测试DOM API的交互和组件的生命周期。用到了jsdom来模拟浏览器环境

##### Enzyme常用方法
1. simulate(event, mock)：模拟事件，用来触发事件，event为事件名称，mock为一个event object
2. instance()：返回组件的实例
3. find(selector)：根据选择器查找节点，selector可以是CSS中的选择器，或者是组件的构造函数，组件的display name等
4. at(index)：返回一个渲染过的对象
5. get(index)：返回一个react node，要测试它，需要重新渲染
6. contains(nodeOrNodes)：当前对象是否包含参数重点 node，参数类型为react对象或对象数组
7. text()：返回当前组件的文本内容
8. html()： 返回当前组件的HTML代码形式
9. props()：返回根组件的所有属性
10. prop(key)：返回根组件的指定属性
11. state()：返回根组件的状态
12. setState(nextState)：设置根组件的状态
13. setProps(nextProps)：设置根组件的属性

### 测试实例
#### 组件代码
##### containers/App.js
```js
import React from 'react';
import Header from '../../components/header';
import TodoInput from '../../components/todo_input';
import TodoList from '../../components/todo_list';
import Footer from '../../components/footer';

export default class App extends React.Component{
  state = {
    todoList: [],
    active: 'all',
  }

  currentId = 0;

  get todos() {
    const {todoList, active} = this.state;
    switch (active) {
      case 'completed':
        return todoList.filter(el => el.completed);
      case 'active':
      return todoList.filter(el => !el.completed);
      default:
        return todoList;
    }
  }

  addTodo = (value) => {
    const { todoList } = this.state;
    const item = {id: this.currentId++, text: value, completed: false}
    todoList.push(item);
    this.setState({todoList})
  }

  toggleTodo = (id) => {
    const { todoList } = this.state;
    for(let i = 0; i < todoList.length; i++) {
      if(todoList[i].id === id) {
        todoList[i].completed = true;
        this.setState({todoList});
        return;
      }
    }
  }

  filterTodos = (value) => {
    this.setState({active: value})
  }

  render() {
    const { active } = this.state;
    return (
      <div style={{margin: '0 auto', width: 400}}>
        <Header title="TODOS"/>
        <TodoInput onEnter={this.addTodo} placeholder="press enter add todo"/>
        <TodoList todos={this.todos} toggleTodo={this.toggleTodo}/>
        <Footer onClick={this.filterTodos} active={active}/>
      </div>
    )
  }
}
```

##### component/todo.js
```js
import React from 'react';

const Todo = ({ onClick, completed, text }) => (
  <li
    onClick={onClick}
    className={completed ? 'completed' : 'active'}
    style={{textDecoration: completed ? 'line-through' : ''}}
  >
    {text}
  </li>
)

export default Todo;
```
##### component/todo_list.js
```js
import React from 'react'
import Todo from '../todo'

const TodoList = ({ todos=[], toggleTodo }) => (
  <ul>
    {todos.map(todo =>
      <Todo
        key={todo.id}
        {...todo}
        onClick={() => toggleTodo(todo.id)}
      />
    )}
  </ul>
)

export default TodoList
```
```js
import React from 'react';

const TodoInput = ({onEnter, autoFocus, placeholder}) => {
  const handleEnter = e => {
    const value = e.target.value;
    if(e.key === 'Enter' && onEnter && value) {
      onEnter(value);
      e.target.value = '';
    }
  }

  return (
    <input placeholder={placeholder} autoFocus={autoFocus} onKeyDown={handleEnter} type="text"/>
  )
}

export default TodoInput;

```
##### component/footer.js
```js
import React from 'react';

const links = ['all', 'completed', 'active']

const Footer = ({onClick, active='all'}) => (
  <div>
    <span>Show: </span>
    {
      links.map(el => (
        <button
          key={el}
          disabled={el === active}
          onClick={() => {onClick(el)}}
        >
          {el}
        </button>
      ))
    }
  </div>
)

export default Footer
```

#### 快照测试
```js
it("Todo 快照测试", () => {
  const renderTodo = render(<Todo {...props} />);
  expect(renderTodo).toMatchSnapshot();
});
```
Jest检测到 toMatchSnapshot 方法时，会在当前文件的同级目录下生成一个 __snapshots__ 文件夹，会把组件DOM的快照存储到这个文件夹里。
每次测试都会生成一份快照和第一次生成的快照进行比较，如果不一致这个测试用例就会失败。如果需要更新快照可以执行下面的命令。

> jest ---updateSnapshot

#### 测试组件节点
```js
const props = {
  todos: [
    { id: 0, text: 'use jest', completed: false },
    { id: 1, text: 'use enzyme', completed: false },
    { id: 2, text: 'use react', completed: true },
  ],
  toggleTodo: jest.fn()
};
const shallowTodos = shallow(<TodoList {...props} />)
const mountTodos = mount(<TodoList {...props} />)

it('浅渲染可以找到 3 个 Todo 组件', () => {
  expect(shallowTodos.find('Todo').length).toBe(3);
})
it('浅渲染可以找到 0 个 li 标签', () => {
  expect(shallowTodos.find('li').length).toBe(0);
})
it('完全渲染可以找到 3 个 Todo 标签', () => {
    expect(mountTodos.find('Todo').length).toBe(3);
  })
it('完全渲染可以找到 3 个 li 标签', () => {
  expect(mountTodos.find('li').length).toBe(3);
})
```
上面使用了 enzyme 提供的 find 方法，只支持简单选择器。

上面代码还测试了 enzyme 的 mount 与 shallow 的区别：
- mount 和 shallow 都可以找到子组件 Todo;
- mount 完全渲染会渲染子组件；
- shallow 不会渲染子组件的内容。

也可以使用循环的方式对组件节点进行测试：
```js
const links = ['all', 'completed', 'active'];

links.forEach((el, index) => {
  it(`第 ${index + 1} 个 button 的文本为 ${el}`, () => {
    expect(shallowFooter.find('button').at(index).text()).toBe(el);
  })
})
```

#### 模拟原生事件
##### onClick事件
simulate 方法可以模拟触发各种原生事件，接收两个参数：
1. 事件名称：'click', 'change', 'keydown' ...
2. event 对象

```js
it('测试点击事件', () => {
  shallowTodo.find('li').at(0).simulate('click');
  expect(props.onClick).toHaveBeenCalled();
})
```
模拟点击了 li 标签，通过 props 传入的 onClick 事件被调用。

```js
it('触发点击事件 onEnter 会被调用', () => {
  shallowTodos.find('Todo').at(0).simulate('click');
  expect(props.toggleTodo).toHaveBeenCalledTimes(1)
})
```
非完全渲染的子组件也可以触发 click 事件，props.toggleTodo 被调用且只被调用一次。

> button 是 disabled 状态也可以触发 click 事件。
```js
it('点击第一按钮, 会传入一个 all', () => {
  shallowFooter.find('button').at(0).simulate('click');
  expect(props.onClick).toHaveBeenCalled();
  expect(props.onClick.mock.calls[0][0]).toBe('all');
})

it('点击第二按钮, 会传入一个 completed', () => {
  shallowFooter.find('button').at(1).simulate('click');
  expect(props.onClick).toHaveBeenCalled();
  expect(props.onClick.mock.calls[1][0]).toBe('completed');
})
```
当前第一个按钮是 disabled 状态，触发的点击事件，并且把 'all' 传入的 mock 方法。

##### 键盘事件
```js
it('target.value 为 "" onEnter 不会调用', () => {
  shallowInput.find('input').at(0).simulate('keydown',
    {key: 'Enter', keyCode: 13, target: { value: "" }}
  );
  expect(props.onEnter).not.toHaveBeenCalled();
})

it('target.value 不为 "" onEnter 会调用', () => {
  shallowInput.find('input').at(0).simulate('keydown',
    {key: 'Enter', keyCode: 13, target: { value: "123" }}
  );
  expect(props.onEnter).toHaveBeenCalled();
  expect(props.onEnter.mock.calls[0][0]).toBe('123')
})
```
通过传入 keydown 模拟键盘按下的事件，event 对象中传入的 key、keyCode、target 等属性会在调用的方法中被获取。

#### setState、setProps 方法
setState方法可以直接改变组件中的 state 状态。
```js
it('直接修改 state.active, todos 变化', () => {
  shallowApp.setState({active: 'active'});
  expect(shallowApp.state().active).toEqual('active');
  expect(shallowApp.state().todoList).toEqual([{ id: 0, text: 'use jest', completed: false }])
  expect(shallowApp.instance().todos).toEqual([{ id: 0, text: 'use jest', completed: false }])
})
```

setProps方法可以直接改变组件中的 props 状态。
```js
it('改变 props 的 completed, 类名为 completed', () => {
  shallowTodo.setProps({completed: true})
  expect(shallowTodo.find('li').prop('className')).toBe('completed');
  expect(shallowTodo.find('li').prop('style').textDecoration).toBe('line-through');
})
```

#### 异步测试
源代码：
```js
const asyncFn = (number) => {
  return new Promise((resolve, reject) => {
    number >= 0 ? resolve( {success: true} ) : reject({error: 'number不能小于0'})
  })
}

export default asyncFn;
```

测试代码：
```js
describe('asyncFn', () => {
  it('测试 Promise', () => {
    expect(asyncFn(1)).resolves.toEqual({success: true})
    expect(asyncFn(-1)).rejects.toEqual({error: 'number不能小于0'})
  })
})
```
resolves 和 rejects 必须和前面的执行结果相匹配，不然用例也会执行失败。

##### 使用 .then 测试 Promise
```js
it('test resolve with promise', () => {
  asyncFn(1).then(data => {
    expect(data).toEqual({success: true})
  })

});
it('test error with promise', () => {
  asyncFn(-1).then(data => {
    expect(data).toEqual({error: 'number不能小于0'})
  })
});
```

##### async await 测试
```js
// 测试resolve
it('works resolve with async/await', async () => {
  const data = await asyncFn(4);
  expect(data).toEqual({success: true});
});

// 测试reject
it('works reject with async/await', async () => {
  try {
    await asyncFn(-1);
  } catch (e) {
    expect(e).toEqual({
      error: 'number不能小于0'
    });
  }
});
```

#### 测试报告
直接使用 jest 后面添加参数 coverage 会生成一份测试报告，同时会在根目录下生成一个 coverage 文件夹，里面有详细的测试报告
> jest ---coverage
控制台打印的测试报告

![WX20190505-134324@2x.png](https://github.com/volcanoliuc/blog/blob/master/images/WX20190505-134324@2x.png?raw=true)

coverage中的测试报告
![WX20190505-134401@2x.png](https://github.com/volcanoliuc/blog/blob/master/images/WX20190505-134401@2x.png?raw=true)

##### 四个测试维度
- 行覆盖率(line coverage)：是否测试用例的每一行都执行了
- 函数覆盖率(function coverage)：师傅测试用例的每一个函数都调用了
- 分支覆盖率(branch coverage)：是否测试用例的每个if代码块都执行了
- 语句覆盖率(statement coverage)：是否测试用例的每个语句都执行了

#### 总结
> 上面代码并非完成的项目代码，代码已上传的 [GitHub](https://github.com/volcanoliuc/jest-todo)
我们利用 jest 完美的测试环境和 enzyme 极简API可以快速的完成单元测试。
总而言之，jest 和 enzyme 将会是测试react应用的不二选择。
