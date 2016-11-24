**store 是应用状态 state 的管理者，包含下列四个函数：**
getState() # 获取整个 state
dispatch(action) # ※ 触发 state 改变的【唯一途径】※
subscribe(listener) # 您可以理解成是 DOM 中的 addEventListener
replaceReducer(nextReducer) # 一般在 Webpack Code-Splitting 按需加载的时候用
二者的关系是：**state = store.getState();**
********************************************************************************************************************
**触发submit点击**
import React from 'react'
import { connect } from 'react-redux'
import { addTodo } from '../actions'

let AddTodo = ({ dispatch }) => {
  let input
  return (
    <div>
      <form onSubmit={e => {
        e.preventDefault()
        if (!input.value.trim()) {
          return
        }
        dispatch(addTodo(input.value))  ~~dispatch(addTodo(input.value))~~
        input.value = ''
      }}>
        <input ref={node => {
          input = node
        }} />
        <button type="submit">
          Add Todo
        </button>
      </form>
    </div>
  )
}
AddTodo = connect()(AddTodo)
export default AddTodo

**触发actions里面**
let nextTodoId = 0
export const addTodo = (text) => ({   ~~addTodo~~
  type: 'ADD_TODO',   ~~type: 'ADD_TODO',~~
  id: nextTodoId++,
  text
})

export const setVisibilityFilter = (filter) => ({
  type: 'SET_VISIBILITY_FILTER',
  filter
})

export const toggleTodo = (id) => ({
  type: 'TOGGLE_TODO',
  id
})

**触发reducers里面**
const todo = (state, action) => {
  switch (action.type) {
    case 'ADD_TODO':    ~~匹配到了type类型后,返回改变后的值~~
      return {
        id: action.id,
        text: action.text,
        completed: false
      }
    case 'TOGGLE_TODO':
      if (state.id !== action.id) {
        return state
      }

      return {
        ...state,
        completed: !state.completed
      }
    default:
      return state
  }
}

const todos = (state = [], action) => {
  switch (action.type) {
    case 'ADD_TODO':
      return [
        ...state,   ~~...state 返回的state { id: action.id,text: action.text,completed: false }~~
        todo(undefined, action)
      ]
    case 'TOGGLE_TODO':
      return state.map(t =>
        todo(t, action)
      )
    default:
      return state
  }
}

export default todos

********************************************************************************************************************

store.subscribe(() => noNext(store.getState()))
