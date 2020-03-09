---
title: 使用Jest、Enzyme等工具进行单元测试(上)
---

# Jest
Jest是Facebook开源的测试框架，几乎是0配置直接进行单元测试，相对其他测试框架，其一大特点就是内置了常用的测试工具，比如自带断言、测试覆盖率等工具。Jest还有很多好处这里就不一一介绍了，详情可以到Jest官网查看[https://jestjs.io/](https://jestjs.io/)。  

## Jest 安装
这里默认安装了node。 如果没有安装的，可自行到Node官网进行下载安装[https://nodejs.org/](https://nodejs.org/)。安装好Node后是默认自带NPM包管理工具的。我们可以通过 `node -v` 和 `npm -v` 来检测node、和npm是否安装成功，如果都成功的显示了版本号，就说明node和npm都安装成功了。下面我们就可以对Jest来进行安装。   

Jest的安装十分简单，就一行命令    

`npm install --save-dev jest`   

因为只有在开发的时候我们才去运行测试用例，所以我们在安装Jest的时候加上`--save-dev`。安装完成后我们就可以进行Jest的学习了。

## Jest初体验   
我们可以根据Jest官网给的简单的例子，来体验一下Jest。   

我们写一个需要被测试的函数，这个函数就是做一个简单的两个数相加的运算然后返回结果。首先我们先创建一个名为 `sum.js` 的js文件:
```javascript
function sum(a, b) {
  return a + b;
}
module.exports = sum;
```
我们用module.exports将这个函数导出   
然后我们创建一个名为 `sum.test.js`的js文件，这个文件里写我们真正的测试代码:

```javascript
const sum = require('./sum');

test('adds 1 + 2 to equal 3', () => {
  expect(sum(1, 2)).toBe(3);
});
```
然后我们修改 `package.json` 文件，使得我们输入 `npm test` 的时候就可以运行我们的测试用例:
```JSON
{
  "scripts": {
    "test": "jest"
  }
}
```
最后我们输入`npm run test`来运行我们的测试用例，并可以看到Jest打印的信息：
```
PASS  ./sum.test.js
✓ adds 1 + 2 to equal 3 (5ms)
```
至此我们就成功的用Jest写了第一个测试用例。

我们可以看到这段测试代码的核心就两条语句
1. 调用一个test方法，第一个参数是对这个测试用例的描述，第二参数是一个回调函数，里面是具体的测试方法。
2. `expect(resultValue).toBe(actualValue)` 这行代码，我们可以看做，当expect方法中的传入的这个值和toBe这个方法中的值完全相等。这条测试用例就算通过（例子中，resultValue是调用sum函数的返回值，actualValue是我们认为函数运行正确应该返回的值）

我们可以简单地试着实现一下这两行代码，这样能更好的理解。

```javascript
function expect(resultValue) { //函数的返回值
  return {
    //返回一个对象，其中有toBe方法，toBe方法接收真实值。
    toBe: function(actualValue) {
      //判断函数的返回值，与真实值是否相等，如果不相等，抛出错误。
      if (resultValue !== actualValue) {
        throw new Error('Error');
      }
    }
  }
}

function test(description: string, fn: Function) {
  try {
    fn(); //执行函数fn，如果没有抛出错误，输出PASS
    console.log(`PASS`); 
  } catch(e) {
    //如果fn抛出错误，将在这里捕获，并输出错误信息。
    console.log(`${description} : ${e}`)
  }
}
```

我们可以简单的理解为，`expect(resultValue).toBe(actualValue)` 就是对上述代码的简化。它的实质其实就是比较。expect函数中resultValue和toBe函数中actualValue是否完全相等。之后会介绍Jest中更多的方法。

## 配置你的Jest
首先 我们输入下面命令，来生成Jest的配置文件。
`npx jest --init`   
> 注意，我们这里使用的是npx，不是npm，npx的意思是运行的时候，会到 `node_modules/.bin` 路径和环境变量  `$PATH` 里面，检查命令是否存在。 而不是去全局环境查找命令。

输入完命令后会有几个问题让你回答，来创建基本的配置文件。
```
Would you like to use Jest when running "test" script in "package.json"? › (Y/n)
这个就是帮你配置package.json当，npm test的时候执行Jest。

Choose the test environment that will be used for testing › - Use arrow-keys. Return to submit.
❯   node
    jsdom (browser-like)
选择node环境还是浏览器环境

Do you want Jest to add coverage reports? › (y/N)
是否增加测试覆盖率报告

Automatically clear mock calls and instances between every test? › (y/N)
每次运行测试时自动清除所有mock
```

回答完问题之后，会生成一个 `jest.config.js` 的配置文件。我们可以在里面进行Jest更多的配置。我们将会在之后专门来讲Jest的配置，以及通过babel支持TypeScript和ES Modules。   

## Jest Matchers 常用匹配器   
匹配器（Matchers）是Jest中非常重要的一个概念，它可以提供很多种方式来让你去验证你所测试的返回值

### Truthiness
* `toBe` 匹配器：相当于 Object.is 或者 ===
* `toEqual` 匹配器： 只匹配内容，不匹配引用。
* `toBeNull` 匹配器： 内容是否等于Null
* `toBeUndefined` 匹配器： 内容是否等于undefined
* `toBeDefined` 匹配器： 希望内容是定义过的。
* `toBeTruthy` 匹配器： 内容是否为true。
* `toBeFalsy` 匹配器： 内容是否为false。
* `not` 匹配器： 在其他匹配器之前，相当于取反操作   

### 与数字相关
* `toBeGreaterThan` 匹配器： 输入的数字是否大于
* `toBeGreaterThanOrEqual`  匹配器： 输入的数字是否大于等于
* `toBeLessThan` 匹配器： 输入的数字是否小于
* `toBeLessThanOrEqual`  匹配器： 输入的数字是否小于等于
* 对于浮点数判断相等，为了解决浮点数的bug。要用`toBeCloseTo`匹配器。   

### 与String相关
* `toMatch` 匹配器： 结果中是否包含内容，可以是String也可以是正则   

### 与Array相关
* `toContain` 匹配器： 判断元素是否存在数组中。   

### 与异常相关
* 用`toThrow`匹配器来判断在调用一个函数出现异常时，这个函数是否抛出了异常。   


## Jest测试异步函数

### callback
我们先看下面的测试用例

```javascript
test('testing asynchronous code', () => {
  function callback(data) {
    expect(data).toBe('success');
  }

  fetchData(callback);
});
```
在上面这个函数中，fetchData是一个异步的方法，去请求数据，当数据返回时调用，callback函数。我们期望这个异步函数的返回值是”success“。但是Jest并不知道这个异步函数什么时候返回。Jest仅仅只是从头执行到尾。这样这个测试用例是无效的。

为了解决这个问题。Jest提供了一个 `done` 参数，这个参数通过test函数的回调方法传进去，`done` 是一个不接受任何参数的方法。具体怎么用我们来看代码


```javascript
test('testing asynchronous code with done', done => {
  function callback(data) {
    expect(data).toBe('success');
    done();
  }

  fetchData(callback);
})
```
我们对比两个代码可以看出，下面这块代买仅仅是在回调函数中多执行了一句`done()`它的意思就是告诉Jest，只有运行到了`done()`这个命令的时候，这个test才算完事。如果一直不调用`done()`的话会报出超时错误。
但是这里还有点问题。就是当测试不通过的时候，会到的done不被调用。这时我们还需要改写一下代码，用try/catch 来捕获expect错误从而实现我们这个需求。

```javascript
  test('testing asynchronous code with done', done => {
    function callback(data) {
      try {
        expect(data).toBe('success');
        done();
      } catch (e) {
        done(e);
      }
    }

    fetchData(callback);
  })
```

### Promises
如果使用Promise，会简单很多。我们只需要把 promise返回， Jest会等待promise resolve。 如果  rejected 的话，这条测试用例会自动不通过。代码如下。

```JavaScript
test('testing asynchronous code', () => {
  return fetchData().then(data => {
    expect(data).toBe('success');
  });
});
```

如果你用catch捕获了rejected。 那就一定要添加assertions匹配器，assertions匹配器接收一个参数，这个参数表示expect的次数，如果没有出现指定的次数，就会报错。我们改写一下上面的方法。

```javascript
test('the fetch fails with an error', () => {
  expect.assertions(1);
  return fetchData().catch(e => expect(e).toMatch('error'));
});
```

### resolves / rejects 匹配器
我们也可以用 resolves和rejects匹配器。我们直接看代码

```javascript
test('testing asynchronous code', () => {
  return expect(fetchData()).resolves.toBe('success');
});

test('the fetch fails with an error', () => {
  return expect(fetchData()).rejects.toMatch('error');
});
```

### Async/Await
最后我们可以用，Await来等待异步函数执行完成，我们只需要把test中的回调函数改写成async/await形式即可。

```javascript
test('the data is peanut butter', async () => {
  const data = await fetchData();
  expect(data).toBe('peanut butter');
});

test('the fetch fails with an error', async () => {
  expect.assertions(1);
  try {
    await fetchData();
  } catch (e) {
    expect(e).toMatch('error');
  }
});
```

至此我们简单的介绍了Jest的基本用法。更多的用法我们会在下节讲到。