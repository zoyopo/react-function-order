
<p style="text-align: center"><img src="https://pic.imgdb.cn/item/625a4d6b239250f7c55b4257.png" alt=""></p>

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

## 在线demo

[在线超市](https://codesandbox.io/s/functionorder-demo-f1kqwz)

## 运行机制
![流程图](https://pic.imgdb.cn/item/6255959b239250f7c5103c3b.jpg)






## 基本使用

### 最简单的使用


```jsx
   import {useFunctionOrderState} from 'react-function-order'

    class JustFnAction {
    
        plus(num) {
            return 1 + num
        }
    
        square(num) {
            return Math.pow(num, 2)
        }
    
        minus(num) {
            return num - 2
        }
    }
    function App() {
        const {actionState, foIns} = useFunctionOrderState({action: JustFnAction})
        useEffect(() => {
            foIns.run(2)
        }, [])
    
        useEffect(() => {
            console.log('actionState Change', actionState)
            // actionState Change {}
            // actionState Change {getActionResult: 7}
        }, [actionState])
    
        return (
            <div className="App">
                {actionState['getActionResult']}
            </div>
        )
    }
```
### 如果我们将`minus`,`square`改为异步函数


```jsx
   import {useFunctionOrderState} from 'react-function-order'
    class FnReturnPromiseAction {
        plus(num) {
            return 1 + num
        }
    
        square(num) {
            return new Promise((resolve => {
                setTimeout(() => {
                    resolve(Math.pow(num, 2))
                },100)
            }))
        }
    
        minus(num) {
            return new Promise((resolve => {
                setTimeout(() => {
                    resolve(num - 2)
                }, 200)
    
            }))
        }
    }
    function App() {
        const {actionState, foIns} = useFunctionOrderState({action: FnReturnPromiseAction})
        useEffect(() => {
            foIns.run(2)
        }, [])
    
        useEffect(() => {
            console.log('actionState Change', actionState)
            // actionState Change {}
            // actionState Change {getActionResult: 7}
        }, [actionState])
    
        return (
            <div className="App">
                {actionState['getActionResult']}
            </div>
        )
    }
```
`react-function-order`会自动为我们将异步函数按照同步顺序执行

### `run`的时候执行多个并行的异步函数

1. 并行异步函数之间的函数依然依次执行
```jsx
   import {useFunctionOrderState,InitKeys} from 'react-function-order'

    class PromiseIndependentAction {
        init() {
            return {
                // Declare the functions's names that need to store the result
                [InitKeys.saveResultNames]: ['storeMotoName', 'storeLocation'],
                // Declare flat async functions name
                [InitKeys.flatAsyncNames]: ['getPopularMotoByBrand', 'getLocationByBrand']
            }
        }
    
        getPopularMotoByBrand(brand) {
            return new Promise((resolve => {
                setTimeout(() => {
                    const map = {
                        'honda': 'honda cm300',
                        'suzuki': 'gsx250r'
                    }
                    resolve(map[brand])
                }, 30)
    
            }))
        }
    
        storeMotoName(res) {
            return res
        }
    
        getLocationByBrand(brand) {
            return new Promise((resolve => {
                setTimeout(() => {
                    const map = {
                        'honda': 'Japan',
                        'suzuki': 'Japan',
                        'BMW': 'Ger'
                    }
                    resolve(map[brand])
                }, 30)
            }))
        }
    
        storeLocation(res) {
            return res
        }
    }
    function App() {
        const {actionState, foIns} = useFunctionOrderState({action: PromiseIndependentAction})
        useEffect(() => {
            foIns.run('suzuki')
        }, [])
    
        useEffect(() => {
            console.log('actionState Change', actionState)     
            //{
            // storeMotoName:"gsx250r",
            //  storeLocation:'Japan'
            // }
        }, [actionState])
    
        return (
            <div className="App">
                {actionState['storeMotoName']}
            </div>
        )
    }
```

我们可以在`init`函数中声明`flatAsyncNames`，标记其为并行执行的异步函数，在这些函数之后的函数，依然会依次执行。那么现在有两个结果，我们需要利用两个`key`来存储，
所以我们可以在`saveResultNames`声明存储值的函数，并以此为`key`

2. 一个异步函数返回并行执行的promises
```jsx
   import { useFunctionOrderState } from 'react-function-order'
class getMotoAction {
    getBrandNameById(id) {
        return new Promise((resolve => {
            setTimeout(() => {
                const map = {
                    7: 'suzuki',
                    8: 'honda'
                }
                resolve(map[id])
            }, 30)

        }))
    }

    getPopularMotoByBrand(brand) {
        let p = new Promise((resolve => {
            setTimeout(() => {
                const map = {
                    'honda': 'honda cm300',
                    'suzuki': 'gsx250r'
                }
                resolve(map[brand])
            }, 30)
        }))

        let p2 = new Promise((resolve => {
            setTimeout(() => {
                const map = {
                    'honda': 'Japan',
                    'suzuki': 'Japan',
                    'BMW': 'Ger'
                }
                resolve(map[brand])
            }, 30)
        }))
        return [p,p2]
    }
}

function App() {
    const {actionState, foIns} = useFunctionOrderState({action: getMotoAction})
    useEffect(() => {
        foIns.run(7)
    }, [])

    useEffect(() => {
        console.log('actionState Change', actionState)
        //{
        // getActionResult:["gsx250r","Japan"]        
        // }
    }, [actionState])

    return (
        <div className="App">
            {actionState['getActionResult']}
        </div>
    )
}

```


### 我们如何修改存储在`actionState`中的值(沿用上面的`getMotoAction`)

```jsx
import { useFunctionOrderState,ModifyParams } from "react-function-order"
class ModifyMotoAction {

    modifyActionState(params:ModifyParams){
        const {actionState,runParams} =params
        let actionResult = actionState["getActionResult"]
        actionResult && (actionResult[1]= runParams)
        return actionResult
    }
}
function App() {
    const {actionState, foIns,dispatch} = useFunctionOrderState({action: getMotoAction})
    useEffect(() => {
        foIns.run(7)
    }, [])

    useEffect(() => {
        console.log('actionState Change', actionState)
        // run
        //{
        //  getActionResult:["gsx250r","Japan"]        
        // }
        // handleModify
        //{
        //  getActionResult:["gsx250r","china"]        
        // }
    }, [actionState])
    
    const handleModify = () =>{
        dispatch(ModifyMotoAction,'china')
    }

    return (
        <div className="App">
            <button onClick={handleModify}>modify result</button>
            {actionState['getActionResult']}
        </div>
    )
}
```
我们暴露出一个`dispatch`方法,传入action和参数，从而进行`actionState`的修改

## 了解更多关于`functionOrder`

[点这里](https://github.com/zoyopo/function-order)


## Change Log
0.1.9 —— add dispatch method in useFunctionOrderState,to modify loaded data
0.1.8 —— Change actionState key from className/methodName to methodName
