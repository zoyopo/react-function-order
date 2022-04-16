# FunctionOrder
<p style="text-align: center"><img src="https://pic.imgdb.cn/item/62595a0f239250f7c5fdd74b.png" alt=""></p>

提供一种更规范，高效，易于测试的函数式编程方式。

## 为何创建此库

前端开发总是伴随着事件、IO操作和逻辑处理。这些限制通常导致逻辑分散，代码测试和维护困难。

## 好处
- 以类来描述一个业务逻辑
- 将逻辑由命令式转换为声明式
- 易于测试
- 使得代码更干净

## 快速开始

### nodejs
```bash 
    npm i function-order -S   // or yarn add function-order -S   
```
### react
```bash
    npm i react-function-order -S   // or yarn add react-function-order -S   
```

## 在线demo(react)

[选中物品分类获取物品和剩余的例子(codesandbox需翻墙)](https://codesandbox.io/s/functionorder-demo-f1kqwz)

## 运行机制
![流程图](https://pic.imgdb.cn/item/6255959b239250f7c5103c3b.jpg)






## 基本使用

### 情景1：同步函数


```jsx
   import {useFunctionOrderState} from 'react-function-order'
    function App() {
        const {actionState, foIns} = useFunctionOrderState({action: JustFnAction})
        useEffect(() => {
            foIns.run(2)
        }, [])
    
        useEffect(() => {
            console.log('actionState Change', actionState)
            // actionState Change {}
            // actionState Change {SimpleAction/getActionResult: 7}
        }, [actionState])
    
        return (
            <div className="App">
                {actionState['JustFnAction/getActionResult']}
            </div>
        )
    }
```
### 情景2：同步函数和异步函数


```jsx
   import {useFunctionOrderState} from 'react-function-order'
    function App() {
        const {actionState, foIns} = useFunctionOrderState({action: FnReturnPromiseAction})
        useEffect(() => {
            foIns.run(2)
        }, [])
    
        useEffect(() => {
            console.log('actionState Change', actionState)
            // actionState Change {}
            // actionState Change {SimpleAction/getActionResult: 7}
        }, [actionState])
    
        return (
            <div className="App">
                {actionState['FnReturnPromiseAction/getActionResult']}
            </div>
        )
    }
```

### Situation3: 扁平的异步函数

```jsx
   import {useFunctionOrderState,InitKeys} from 'react-function-order'
    function App() {
        const {actionState, foIns} = useFunctionOrderState({action: PromiseIndependentAction})
        useEffect(() => {
            foIns.run('suzuki')
        }, [])
    
        useEffect(() => {
            console.log('actionState Change', actionState)     
            //{
            // PromiseIndependentAction/storeMotoName:"gsx250r",
            //  PromiseIndependentAction/storeLocation:'Japan'
            // }
        }, [actionState])
    
        return (
            <div className="App">
                {actionState['PromiseIndependentAction/storeMotoName']}
            </div>
        )
    }
```
### Situation4:  异步函数依赖于前面的异步函数



```jsx
   import {useFunctionOrderState} from 'react-function-order'
    function App() {
        const {actionState, foIns} = useFunctionOrderState({action: PromiseDependOnBeforePromiseAction})
        useEffect(() => {
            foIns.run('suzuki')
        }, [])
    
        useEffect(() => {
            console.log('actionState Change', actionState)     
            //{
            // PromiseDependOnBeforePromiseAction/getActionResult:"180kg"        
            // }
        }, [actionState])
    
        return (
            <div className="App">
                {actionState['PromiseDependOnBeforePromiseAction/getActionResult']}
            </div>
        )
    }
```


## 了解更多关于`functionOrder`

[点这里](https://github.com/zoyopo/function-order)


