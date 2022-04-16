
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
    class FnReturnPromiseAction {
        plus(num) {
            return 1 + num
        }
    
        square(num) {
            return Math.pow(num, 2)
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

    class PromiseDependOnBeforePromiseAction {


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
    
        getWeightOfMotoName(motoName) {
            return new Promise((resolve => {
                setTimeout(() => {
                    const map = {
                        'honda cm300': '170kg',
                        'gsx250r': '180kg'
                    }
                    resolve(map[motoName])
                }, 30)
            }))
        }
    
    }
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
