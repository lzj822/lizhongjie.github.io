---
title: 使用Jest、Enzyme等工具进行单元测试(中)
---

## Jest 生命周期
我们如果有一些工作是在每次跑测试用例之前或者结束的时候要做的。我们就需要使用Jest提供的`beforeEach`和`afterEach`。
如果我们只想跑一次的话，那么我们就要用Jest提供的`beforeAll`和`afterAll`这两个函数会在所有测试用例开始之前，和全部结束之后调用。

我们可以通过具体代码看一下。
```javascript
beforeEach(() => {
    console.log('beforeEach');
})

afterEach(() => {
    console.log('afterEach');
})

beforeAll(() => {
    console.log('beforeAll');
})

afterAll(() => {
    console.log('afterAll');
})

test('test', () => {
    console.log('test');
})

test('test1', () => {
    console.log('test1');
})

test('test2', () => {
    console.log('test2');
})
```

输出结果
```shell
    beforeAll
    beforeEach
    test
    afterEach
    beforeEach
    test1
    afterEach
    beforeEach
    test2
    afterEach
    afterAll
```
通过打印的内容我们就能很直观的看到，beforeAll和afterAll在整个测试文件的开头和结尾调用一次。beforeEach和afterEach会在每个测试用例的开始和结束都会调用。


## Jest 作用域

我们可以用`describe`来将测试分组。当`after`和`before`在`describe`内部的时候，只适用于该`describe`内部的测试，但是顶级的`beforeEach`和`afterEach`也会作用在该`describe`内部的测试用例。但是顶部的`beforeEach`会比内部的先执行，顶部的`afterEach`会比内部的晚执行。具体可以看官网给的例子。

```javascript
beforeAll(() => console.log('1 - beforeAll'));
afterAll(() => console.log('1 - afterAll'));
beforeEach(() => console.log('1 - beforeEach'));
afterEach(() => console.log('1 - afterEach'));
test('', () => console.log('1 - test'));
describe('Scoped / Nested block', () => {
  beforeAll(() => console.log('2 - beforeAll'));
  afterAll(() => console.log('2 - afterAll'));
  beforeEach(() => console.log('2 - beforeEach'));
  afterEach(() => console.log('2 - afterEach'));
  test('', () => console.log('2 - test'));
});

// 1 - beforeAll
// 1 - beforeEach
// 1 - test
// 1 - afterEach
// 2 - beforeAll
// 1 - beforeEach
// 2 - beforeEach
// 2 - test
// 2 - afterEach
// 1 - afterEach
// 2 - afterAll
// 1 - afterAll
```

## Jest Mock函数


## Jest VSCode插件