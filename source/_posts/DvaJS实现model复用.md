---
title: DvaJS model复用的方案
date: 2018-08-30 19:19:20
tags: React, DvaJS
---

### [Dva](https://dvajs.com/guide/getting-started.html#%E5%AE%9A%E4%B9%89-model) 下 model 复用 （一个model同时对应多个业务模块，业务模块间数据互不影响）


    前置知识: dva  =>  一个基于 redux 和 redux-saga 的数据流方案
             react => 高阶组件的定义



#### 1. 示例描述：
    多Tab结构， 存在A、B、C 业务模块，数据不同，业务逻辑一致，希望实现代码复用


#### 2. 示例代码：
```javascript 


    
    // router.js  
    // 1. 动态加载 model 和 路由组件
    {
        path: 'product',
        getComponent(nextState, cb) {
            require.ensure([], require => {
                // 从location 中获取业务类型 type
                const type = nextState.location.query.type
                // 加载model并挂载到dva实例上  注意：require后接了()，因为返回的是一个函数，函数执行返回真正将被挂载的model对象
                app.model(require('./models/product')(type))
                // 加载路由组件  require 返回的也是一个函数，函数执行返回一个React组件（高阶组件，在真正的路由组件上包了一层）
                cb(null, require('./routes/product')(type))
            }, 'product')
        }
    }


    // models/product.js  
    // 2. 根据type 动态生成 model
    export default function modelGenerate(type) {
        return {
            namespace: `products${type}`,
            state: {
                list: []
            },
            reducers: {
            }
            effects: {

            }
        }
    };

    // routes/product.jsx  
    // 3. 根据type 动态返回 新的React组件（对业务组件进行包装）
    export default function routeGenarate(type) {
        // 根据type找到命名空间
        const namespace = `products${type}`
        function ProductWrapper() {
            return class extends React.Component {
                render() {
                    return <Products {...this.props} />
                }
            }
        }
        // 根据 namespace(命名空间) 从redux store 获取对应的model实例数据 ，并命名为 productModel
        return connect(store => ({ productModel: store[namespace] }))(ProductWrapper())
    }

    // 业务组件 从 props.productModel 获取数据
    const Products = ({ dispatch, productModel }) => {
        return (
            <div>
                <ProductList list={productModel.list} />
            </div>
        );
    }

```


