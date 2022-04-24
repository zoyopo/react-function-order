
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

### The simplest use


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
### If we change `minus` and `Square` to asynchronous functions


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
`react-function-order` will automatically execute asynchronous functions in synchronous order for us

###  Execute multiple parallel asynchronous functions when`run`

1. The functions between parallel and asynchronous functions are still executed in turn
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

We can declare `flatAsyncNames` in the `init` function and mark them as asynchronous functions executed in parallel. The functions after these functions will still be executed in turn. Now there are two results. We need to use two `keys` to store them.

Therefore, we can declare the function that stores the value in `saveResultNames` and use it as a `key`.

2. An asynchronous function returns promises executed in parallel
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


### How can we modify the value stored in`actionState`(follow the above `getMotoAction`)

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

We exposed a `dispatch` method and passed in action and parameters to modify the `actionState`

## Know more about`functionOrder`

[Click Here](https://github.com/zoyopo/function-order)


## Change Log
0.1.9 —— add dispatch method in useFunctionOrderState,to modify loaded data
0.1.8 —— Change actionState key from className/methodName to methodName