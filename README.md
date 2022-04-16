
![logo](https://pic.imgdb.cn/item/625a4d6b239250f7c55b4257.png)

[简体中文](https://github.com/zoyopo/FunctionPipeline/blob/master/README-zh_CN.md)

It provides a more standardized, efficient and easy to test functional programming method.

## why create the lib

Front-end development is always accompanied by events, IO operations and logic processing. These restrictions usually
lead to scattered logic and difficult code testing and maintenance.

## Benefits


- Describe a business logic with classes

- Support integration status management

- Convert logic from imperative to declarative

- Easy to test

- Make code clean




## quick start

### react
```bash
    npm i react-function-order -S   // or yarn add react-function-order -S   
```
## online demo

[Online Supermarket ](https://codesandbox.io/s/functionorder-demo-f1kqwz)

## how it works

![流程图](https://pic.imgdb.cn/item/6255959b239250f7c5103c3b.jpg)

## how to use

### situation 1:all sync pure functions


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

### situation 2:  sync functions with async functions


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

### Situation3: flat async functions


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

### Situation4:  async function depend on async function before


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

## Know more about functionOrder

[Click here.](https://github.com/zoyopo/function-order)
