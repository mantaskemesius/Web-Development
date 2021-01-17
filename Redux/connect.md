# Connect()


### mapStateToProps
* When is it called?

It is called every time the store state changes. [Resource](https://react-redux.js.org/using-react-redux/connect-mapstate#connect-extracting-data-with-mapstatetoprops)

* 2 parameters vs 1 parameter - what is the difference?
Example:
```javascript
// 1 example
const mapStateToProps = (state: State) => ({})

// vs
// 2 example
const mapStateToProps = (state: State, _: OwnProps) => ({})
```
[Resource](https://react-redux.js.org/using-react-redux/connect-mapstate#return-values-determine-if-your-component-re-renders)  
mapStateToProps runs when: 

1 example: store state changes  
2 example: store state changes or any field of ownProps is different

component re-renders when:   
1 example: any field of stateProps is different   
2 example: any field of stateProps is different or any field of ownProps is different. 

* Is it good to create and array in mapStateToProps function? Why?

```javascript
const mapStateToProps = (state: State, { id }: OwnProps) => ({
  ids: [...state.ids, id]
})
```

React Redux does shallow comparisons to see if the mapStateToProps results have changed. That means, if object or array references would be different every time, that could cause component re-renders even if the data is actually the same.

Best place to do this operations is in memoized selector functions to ensure that they only run if the input values have changed. This will also ensure that if the input values haven't changed, mapStateToProps will still return the same result values as before, and connect can skip re-rendering.

### mapStateToPropsFactory
* When is it called? When is returned function called?

Returned function will be used as the final mapStateToProps function for a particular component instance. That means - It is called every time the store state changes. Parent function will be initialized on component mount. This allow to do per-instance memoization.

PR: https://github.com/reduxjs/react-redux/pull/279/files

* When is it useful? Briefly explain good (or bad) usage of Factory.

```javascript
// ---------- COMPONENT WHICH USES CONTAINER ----------
import React from 'react'
import VisibleTodoList from '../containers/VisibleTodoList'

const App = () => (
  <div>
    <VisibleTodoList listId="1" />
    <VisibleTodoList listId="2" />
    <VisibleTodoList listId="3" />
  </div>
)


// ---------- CONTAINER ----------

import { connect } from 'react-redux'
import { toggleTodo } from '../actions'
import TodoList from '../components/TodoList'
import { makeGetVisibleTodos } from '../selectors'

//BAD
//Context: getVisibleTodos is memoized selector
// Why bad? We have 3 different instances which passing different listIds. That means our memoized selector will be recomputed each time when state will be updated.
const mapStateToProps = (state, props) => ({
  todos: getVisibleTodos(state, props)
})


//GOOD
// Why good? In this case, by using mapStateToPropsFactory we will create per-instance memoization and for each instance we will create brand new memoized selector which will recompute only when this specific Component data (props) will be changed.
const makeMapStateToProps = () => {
  const getVisibleTodos = makeGetVisibleTodos()
  return (state, props) => ({
    todos: getVisibleTodos(state, props)
  })
}




const VisibleTodoList = connect(
  mapStateToProps || makeMapStateToProps,  
)(TodoList)

export default VisibleTodoList
```
