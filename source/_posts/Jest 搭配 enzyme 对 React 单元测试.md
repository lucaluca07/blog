#### 简介
##### jest
> Jest is a delightful JavaScript Testing Framework with a focus on simplicity.

##### Enzyme
Enzyme是Airbnb开源的React测试工具库库，它功能过对官方的测试工具库ReactTestUtils的二次封装，提供了一套简洁强大的 API

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
#####
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
> Object.is(value1, value2);

```js
test('two plus two is four', () => {
  expect(2 + 2).toBe(4);
});
```

- not：用来取反
```js
test('two plus two is four', () => {
  expect(2 + 2).not.toBe(5);
});
```

- toEqual(value)：用于对象的深比较
```js
test('object assignment', () => {
  const data = {one: 1};
  data['two'] = 2;
  expect(data).toEqual({one: 1, two: 2});
});
```

- toContain(item)：用来判断item是否在一个数组中，也可以用于字符串的判断
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

[更多](https://jestjs.io/docs/zh-Hans/expect)


##### 三种渲染方法

1. shallow：浅渲染。将组件渲染成虚拟DOM对象，只会渲染第一层，子组件将不会被渲染出来，使得效率非常高。不需要DOM环境， 并可以使用jQuery的方式访问组件的信息
2. render：静态渲染，它将React组件渲染成静态的HTML字符串，然后使用Cheerio这个库解析这段字符串，并返回一个Cheerio的实例对象，可以用来分析组件的html结构
3. mount：完全渲染，它将组件渲染加载成一个真实的DOM节点，用来测试DOM API的交互和组件的生命周期。用到了jsdom来模拟浏览器环境

##### 常用方法
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

component/Header.js
```js
import React from 'react';
import PropTypes from 'prop-types'

const Header = ({ title, onClick }) => (
  <header className="header">
    <button onClick={onClick}>button</button>
    <h1>{title}</h1>
  </header>
);

Header.propTypes = {
  title: PropTypes.string.isRequired,
  onClick: PropTypes.func
}

export default Header;
```
App.js
```js
import React, { Component } from 'react';
import Header from './components/Header';

class App extends Component {
  state = {
    value1: 0,
    value2: 0,
  }

  addition = () => {
    const { value1, value2 } = this.state;
    return value1 + value2;
  }

  subtraction = () => {
    const { value1, value2 } = this.state;
    return value1 - value2;
  }

  multiplication = () => {
    const { value1, value2 } = this.state;
    return value1 * value2;
  }

  division = () => {
    const { value1, value2 } = this.state;
    return value1 / value2;
  }

  handleValue1Change = e => {
    this.setState({value1: e.target.value})
  }

  handleValue2Change = e => {
    this.setState({value2: e.target.value})
  }

  handleClick = () => {
    console.log('点击事件')
  }

  render() {
    const { value1, value2 } = this.state;
    return (
      <div>
        <Header title="JestTest" onClick={this.handleClick}/>
        <input
          onChange={this.handleValue1Change}
          type="number"
          value={value1}
        />
        <input
          onChange={this.handleValue2Change}
          type="number"
          value={value2}
        />
        <button onClick={this.addition}>加</button>
        <button onClick={this.subtraction}>减</button>
        <button onClick={this.multiplication}>乘</button>
        <button onClick={this.division}>除</button>
      </div>
    );
  }
}

export default App;
```
#### 测试构建代码
```js
const setup = () => {
  const props = {}

  const _mount = mount(<App {...props}/>)
  const _render = render(<App {...props}/>)
  const _shallow = shallow(<App {...props}/>)

  return {
    props,
    mount: _mount,
    render: _render,
    shallow: _shallow,
  }
}
```
#### 快照测试
```js
it("App 快照测试", () => {
    const { render } = setup();
    expect(render).toMatchSnapshot();
});
```
jest检测到 toMatchSnapshot 方法时，会在当前文件的同级目录下生成一个 __snapshots__ 文件夹，会把组件DOM的快照存储到这个文件夹里。
每次测试都会生成一份快照和第一次生成的快照进行比较，如果不一致这个测试用例就会失败。如果需要更新快照可以执行下面的命令
> jest --updateSnapshot

#### 测试组件节点
```js
describe('App', () => {
  it('测试 App 组件样式', () => {
    const { mount, shallow } = setup()
    // 查找 Header 组件
    expect(
      shallow.find('Header').length
    ).toBe(1);

    // 查找类名为 header 的元素
    expect(
      mount.find('.header').length
    ).toBe(1);

    // 查找类名为 header 的元素
    expect(
      shallow.find('.header').length
    ).toBe(0);

    // 查找 input 标签
    expect(
      shallow.find('input').length
    ).toBe(2);

    // 确定 input 的类型为 number
    [0, 1].forEach(el => {
      expect(
        shallow.find('input').at(el).props().type
      ).toBe('number');
    })


    // 查找 button 标签
    expect(
      shallow.find('button').length
    ).toBe(4);

    // 确定按钮的文本内容
    ['加', '减', '乘', '除'].forEach((el, index) => {
      expect(
        shallow.find('button').at(index).text()
      ).toBe(el);
    })
  })
```
enzyme 的 mount 方法与 shallow 可以在这两段代码中看出区别：
```js
// 查找类名为 header 的元素
expect(
  mount.find('.header').length
).toBe(1);

// 查找类名为 header 的元素
expect(
  shallow.find('.header').length
).toBe(0);
```
mount 方法渲染出的元素可以找到类名为 header 的元素，shallow 渲染的实例中不存在类名为 header 的元素。

#### enzyme  setState 方法
```js
it('测试加法', () => {
  const { shallow } = setup();
  shallow.setState({ value1: 1, value2: 2 });
  expect(shallow.instance().addition()).toBe(3)
  shallow.setState({ value1: 0.1, value2: 0.2 });
  // expect(shallow.instance().addition()).toBe(0.3)
  expect(shallow.instance().addition()).toBeCloseTo(0.3)
})
```
setState方法可以直接改变组件中的 state 状态。通过获取组件的示例调用 addition 方法就可以得到 state 改变后相加的值。
类似的还有 setProps 方法

#### 触发点击事件
```js
it('测试减法', () => {
  const { shallow } = setup();
  const spyFunction = jest.spyOn(shallow.instance(), 'subtraction');
  shallow.setState({ value1: 2, value2: 2 });
  // 模拟触发点击事件
  shallow.find('button').at(1).simulate('click');
  expect(spyFunction).toHaveBeenCalledTimes(1);
  spyFunction.mockRestore();
})
```
可以通过实例的 simulate 方法模拟点击事件，然后通过 jest.spyOn 监听组件中的函数是否执行。

#### 模拟触发 change 事件
```js
t('测试乘法', () => {
  const { shallow } = setup();
  shallow.find('input').at(0).simulate('change', {
    target: {
      value: 4
    }
  })
  shallow.find('input').at(1).simulate('change', {
    target: {
      value: 2
    }
  })
  expect(shallow.state().value1).toBe(4);
  expect(shallow.state().value2).toBe(2);
  expect(shallow.instance().multiplication()).toBe(8)
})
```
同样通过实例的 simulate 方法模拟change事件，simulate方法接受第二个参数是 event。
示例中触发了 change 事件修改了 state 的中的值，可以通过实例的 state() 获取到组件 state 的值。

#### Jest 的 mock 方法
Header组件的 step 方法：
```js
const setup = () => {
  const props = {
    onClick: jest.fn(),
    title: 'JestTest'
  }

  const _mount = mount(<Header {...props}/>)
  const _render = render(<Header {...props}/>)
  const _shallow = shallow(<Header {...props}/>)

  return {
    props,
    mount: _mount,
    render: _render,
    shallow: _shallow,
  }
}
```
##### 触发click事件
```js
it('onClick 事件会被调用', () => {
  const { shallow, props } = setup();
  shallow.find('button').at(0).simulate('click');
  expect(props.onClick).toHaveBeenCalled();
})
```

#### 异步测试
源代码：
```js
const request = (number) => {
  return new Promise((resolve, reject) => {
    number >= 0 ? resolve( {success: true} ) : reject({error: 'number不能小于0'})
  })
}

export default request;
```

测试代码：
```js
describe('request', () => {
  it('测试 Promise', () => {
    expect(request(1)).resolves.toEqual({success: true})
    expect(request(-1)).rejects.toEqual({error: 'number不能小于0'})
  })
})
```
resolves 和 rejects 必须和前面的执行结果相匹配，不然用例也会执行失败。

##### 使用 .then 测试 Promise
```js
it('test resolve with promise', () => {
  request(1).then(data => {
    expect(data).toEqual({success: true})
  })

});
it('test error with promise', () => {
  request(-1).then(data => {
    expect(data).toEqual({error: 'number不能小于0'})
  })
});
```

##### async await 测试
```js
// 测试resolve
it('works resolve with async/await', async () => {
  const data = await request(4);
  expect(data).toEqual({success: true});
});

// 测试reject
it('works reject with async/await', async () => {
  try {
    await request(-1);
  } catch (e) {
    expect(e).toEqual({
      error: 'number不能小于0'
    });
  }
});
```

#### 测试报告
直接使用 jest:
> jest --coverage
create-react-app中使用下面的命令：
> npm run test -- --coverage

##### 四个测试维度
- 行覆盖率(line coverage)：是否测试用例的每一行都执行了
- 函数覆盖率(function coverage)：师傅测试用例的每一个函数都调用了
- 分支覆盖率(branch coverage)：是否测试用例的每个if代码块都执行了
- 语句覆盖率(statement coverage)：是否测试用例的每个语句都执行了
