
### dva学习


- put: 执行efects中的方法，不阻塞流程
  1. yield put一个reducers时，是一个同步操作
  2. yield put一个effects时，是一个异步操作

- call: 执行异步请求，阻塞流程

- dva中efect之间相互调用如何实现同步
```js
// 输出结果为abc   effects中代码
 *a({ payload}, {put, call,take}) {
      try {
      	  console.log(a)
          yield put({type: 'b',payload: {}}); //触发b
          yield take('b/@@end') //直到监听到b结束才继续执行
          console.log(c)
          const info = yield call(serviceA, payload);
          put({
           type: 'save',
           payload: info,
          })
      }catch(e) {
      }
    },
    
 *b({ payload}, {put, call,take}) {
    yield call(serviceB, payload);
    console.log('b')
 }
```

- dva中不同module的effect之间如何通讯
```js
  // mebu为模块名称
  yield put({type: 'menu/getMenuData'})
```

### hooks学习

- 父组件调用子组件方法
```js
// 子组件
import {useState, useImperativeHandle,forwardRef} from 'react'
//props子组件需要接收 ref

const ChildComp = (props, ref) => {

    const [val, setVal] = useState('')
    // 此处注意useImperativeHandle方法的的第一个参数是目标元素的ref引用
    useImperativeHandle(ref, () => ({
        // changeVal 就是暴露给父组件的方法, newVal是父组件传递的参数
        changeVal: (newVal) => {
          setVal(newVal)
        }
    }))
  return (
    <div>{val}</div>
 )
}
ChildComp = forwardRef(ChildComp)

export default ChildComp
```

```js 
// 父组件
import {useRef} from 'react'
import ChildComp from './ChildComp.jsx'

const FComp = () => {
    const childRef = useRef(null)
    const updateChildState = () => {
        // changeVal就是子组件暴露给父组件的方法
        childRef.current.changeVal(99);
    }
    
    return (
      <>
        <ChildComp ref={childRef}></ChildComp>
        <button onClick={updateChildState}>触发子组件方法</button>
      </>
    )
}
export default FComp 
```