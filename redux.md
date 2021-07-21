---
layout: page
title: Redux
permalink: /redux/
---

1.  Action - click/any event inside web app, which modifies data;
2.  Dispatch action - send it to reducer to process;
3.  Reducer - function which takes actions and data, then modifies state, based on action type. Logic of web app;
4.  State - all data of web app stored in one centrallized place;
5.  Store - Reducer + State. Modifies View, based on state;
6.  View - representation of web app, which cant send actions to Store;

```js
import {createStore} from 'redux';

//reducer must be free from calculations and depend only from input action and state;
const reducer = (state = 0, action) => {
  switch (action.type) {
  case 'INC':
    return state + 1;
  case 'DEC': 
    return state - 1;
  case 'RND': 
    return state + action.value;
  default:
    return state;
  }
};

// Pure JS without createStore lib
// let state = reducer(undefined, {});
// state = reducer(state, {type: 'INC'});
// console.log(state);
// state = reducer(state, {type: 'INC'});
// console.log(state);

//action creators
const inc = () => ({ type: 'INC'});
const dec = () => ({ type: 'DEC'});
const rnd = (value) => ({ type: 'RND', value});

const store = createStore(reducer);

document.getElementById('inc').addEventListener('click', () => {
  store.dispatch(inc());
});

document.getElementById('dec').addEventListener('click', () => {
  store.dispatch(dec());
});

document.getElementById('rnd').addEventListener('click', () => {
  const value = Math.floor(Math.random() * 10 );
  // input values here, inside action:
  store.dispatch(rnd(value));
});

// Rework of dispatch with action creators:
// document.getElementById('rnd').addEventListener('click', () => {
//   const value = Math.floor(Math.random() * 10 );
//   // input values here, inside action:
//   store.dispatch({type: 'RND', value});
// });

const update = () => {
  document.getElementById('counter').textContent = store.getState();
};

store.subscribe(update);

// Executes after state change (action dispatch which changed state). Subscribe before action dispatch. 
// store.subscribe(() => {
//   console.log(store.getState());
// });

// store.dispatch({type: 'INC'});
// store.dispatch({type: 'INC'});
// store.dispatch({type: 'INC'});
```

# React-Redux

## Evolution of redux patterns

*  Initial construcion

```js
document.getElementById('rnd').addEventListener('click', () => {
   const value = Math.floor(Math.random() * 10 );
   store.dispatch({type: 'RND', value});
});
```

*  Adding action creator. store.dispatch({type: 'RND', value}) -> store.dispatch(rnd(value));:

```js
const store = createStore(reducer);
const {dispatch} = store;

const rnd = (value) => ({ type: 'RND', value});

document.getElementById('rnd').addEventListener('click', () => {
  const value = Math.floor(Math.random() * 10 );
  store.dispatch(rnd(value));
});
```

*  Destructuring of store, store.dispatch() -> dispatch(), import of actions from another file:

```js
import {inc, dec, rnd} from '../actions';

const store = createStore(reducer);
const {dispatch} = store;

document.getElementById('rnd').addEventListener('click', () => {
   const value = Math.floor(Math.random() * 10 );
   dispatch(rnd(value));
});
```

*  Action creator & dispatch binding, dispatch(rnd(value)) -> rndDispatch(value):

```JS
import {inc, dec, rnd} from '../actions';
const {dispatch} = store;
const rndDispatch = (value) => dispatch(rnd(value));

document.getElementById('rnd').addEventListener('click', () => {
   const value = Math.floor(Math.random() * 10 );
   rndDispatch(value);
});
```

*  Function to bind action creators with dispatch, instead of binding manualy every creator:

```js
import {inc, dec, rnd} from '../actions';
const bindActionCreator = (actionCreator, dispatch) => (...args) => {
    dispatch(actionCreator(...args));
}

const store = createStore(reducer);
const {dispatch} = store;
const rndDispatch = bindActionCreator(rnd, dispatch);

document.getElementById('rnd').addEventListener('click', () => {
   const value = Math.floor(Math.random() * 10 );
   rndDispatch(value);
});
```

*  Native bindActionCreator() -> redux bindActionCreator**s**():

```js
import {inc, dec, rnd} from '../actions';
import { bindActionCreators } from 'redux';

const store = createStore(reducer);
const {dispatch} = store;
const rndDispatch = bindActionCreators(rnd, dispatch);

document.getElementById('rnd').addEventListener('click', () => {
   const value = Math.floor(Math.random() * 10 );
   rndDispatch(value);
});
```

*  Binding multiple action creators with single bindActionCreators conditionaly with destructurisation:

```js
import {inc, dec, rnd} from '../actions';
import { bindActionCreators } from 'redux';

const store = createStore(reducer);
const {dispatch} = store;
const { incDispatch, decDispatch, rndDispatch } = bindActionCreators(
{	
	incDispatch: inc
	decDispatch: dec
	rndDispatch: rnd
}, dispatch);

document.getElementById('rnd').addEventListener('click', () => {
   const value = Math.floor(Math.random() * 10 );
   rndDispatch(value);
});
```

*  "Import as" for action creators. -> bindActionCreators(actions, dispatch), { incDispatch, decDispatch, rndDispatch } -> { inc, dec, rnd }, rndDispatch(value) -> rnd(value)

```js
import * as actions from '../actions';
import { bindActionCreators } from 'redux';

const store = createStore(reducer);
const {dispatch} = store;
const { inc, dec, rnd } = bindActionCreators(actions, dispatch);

document.getElementById('rnd').addEventListener('click', () => {
   const value = Math.floor(Math.random() * 10 );
   rnd(value);
});
```

## Connecting redux to react

*  Initial state:

```js
const {dispatch} = store;

document.getElementById('inc').addEventListener('click', inc);
document.getElementById('dec').addEventListener('click', dec);
document.getElementById('rnd').addEventListener('click', () => {
  const value = Math.floor(Math.random() * 10);
  rnd(value);
});
```

*  Import Counter component, React, ReactDOM. Remove all native DOM functions, add actions in props of App:

```js
import React from 'react';
import ReactDOM from 'react-dom';
import App from './components/app';

const store = createStore(reducer);
const {dispatch} = store;

const update = () => {
    ReactDOM.render(
    <Counter 
        counter={store.getState()}
        inc={inc}
        dec={dec}
        rnd={ () => {
            const value = Math.floor(Math.random() * 10)
            }
        }
    />
    , document.getElementById('root'));
}
update();

store.subscribe(update);
```

*  Import Counter to fresh created App, import App and use App instead Counter. Counter -> App. Import Provider from react-redux to wrap App. Add store as props to Provider. Remove update and subscribe:

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import {Provider} from 'react-redux'; // Wrapper to app. Provider handles update of store and components, lets other components know state
import App from './components/app';

const store = createStore(reducer);

ReactDOM.render(
<Provider store={store}>
    <App/>
</Provider>
, document.getElementById('root'));

```

## How to send store and actions to certain component

```jsx
import React from 'react';
import { connect } from 'react-redux'; // to let components know and use store (state)
import * as actions from '../actions';
import { bindActionCreators } from 'redux';

const Counter = ({ counter, inc, dec, rnd }) => {
  return (
    <div className="jumbotron">
      <h1 id="counter">{counter}</h1>
      <button onClick={dec} id="dec" className="btn btn-primary">DEC</button>
      <button onClick={inc} id="inc" className="btn btn-primary">INC</button>
      <button onClick={rnd} id="rnd" className="btn btn-primary">RND</button>
    </div>
  );
};

// Configures which variables (state) are needed from store for component (Counter)
const mapStateToProps = (state) => {
  return {
    counter: state
  };
};

// Configures which actions to send to component (Counter)
const mapDispatchToProps = (dispatch) => {
  return bindActionCreators(actions, dispatch);
};

// This is how to use connect
export default connect(mapStateToProps, mapDispatchToProps)(Counter);
```
