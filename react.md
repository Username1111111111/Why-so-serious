---
layout: page
title: React
permalink: /react/
---

# Setting up project

```
git init
npm init
npx create-react-app my-app
npm i --save node-sass@4.14.1
npm i --save reactstrap bootstrap@4
npm i --save styled-components
npm i --save react-router-dom
npm i --save redux react-redux
npm i --save prop-types
```

* --save for LOCAL environment of project, but as dependency  
* --save-dev for LOCAL DEVELOPMENT installation, will not be after build  
* -g GLOBALY* (by default)

### Eslint

```
npm i --save-dev eslint-plugin-babel eslint-plugin-react
eslint --init
```

* LF, intendation with tabs, json, single quotes
* Dont forget to disable jshint plugin

### Json server

```
npm i -g json-server
```

Run json-server:

```
json-server --watch src/db.json --port 3004
```

### VScodium
VScodium addons and npm plugins: 
eslint, eslint-plugin-babel, eslint-plugin-react

### False error fixing:

```json
{
"parser": "babel-eslint",
"parserOptions": {
    "ecmaVersion": 6,
    "sourceType": "module",
    "ecmaFeatures": {
        "jsx": true,
        "modules": true,
        "experimentalObjectRestSpread": true
    }
},
```

and then

```
npm install babel-eslint --save-dev
```

[Source](https://stackoverflow.com/questions/36001552/eslint-parsing-error-unexpected-token)

## Patterns

* Keep server requests and data processing separate from components
* If necessary, then "comb" the data, before using in the components
* Try to avoid copy-paste in api requests, create separate methods for different cases, fill in empty fields and handle errors
* Use fetch and xmlhttprequest, keeping in mind that requests to the server are asynchronous (use async await)
* For li, try to use the id from the database, obtained from the server, while always being the same and unique.
* Loading data should be visual with a spinner
* Use destructuring more often to avoid code clutter
* Classfields
* Be able to forward props, states and the context of their use. It is desirable to store the global state of the application in the main application app
* Imports and exports smartly, using index.js
* Ternary rendering (or through a condition) of elements, depending on the state

```jsx
if (!this.state.item) {
    return <span className='select-error'>Please select an item:</span>;
}
```

* Bind the context of the call via this to the place where the call occurs


# Lifecycle

1.  The component appeared on the page - componentDidMount()
2.  The component refreshed on the page - сomponentDidUpdate()
3.  The component was deleted - componentWillUnmount()
4.  Error happened - componentDidCatch()


# Patterns

* Avoiding copy-paste by using **data-providing functions** (props are the input data of a component with a data-providing function, and the component renders something that is received with a providing function)

```jsx
const itemList = (
    <ItemList
        getData={this.gotService.getAllCharacters}/>
);
```

*  **Render function.** Externally control what will be displayed in the child component

```jsx
const itemList = (
    <ItemList renderItem={({ name, gender }) => `${name} (${gender})`} />
);
```

*  **React.Props.Children.** A way that allows you to pull child content, by props, into an ancestor component

```jsx
class Example extends Component {
    render() {
        return this.props.children;
    }
}

ReactDOM.render(<Example>
    <h1>Hello</h1>
</Example>, document.getElementById('root'));
```

Or:  
a. An ancestor element is created:

```jsx
const Field = ({ item, field, label }) => {
    return (
        <li className="list-group-item d-flex justify-content-between">
            <span className="term">{label}</span>
            <span>{item[field]}</span>
        </li>
    );
};
```

b. Access to children in an ancestor is done like this, adhering to the principle of immutability (cloning a child):  

```jsx
//ItemDetails:
<ul className="list-group list-group-flush">
    {
        React.Children.map(this.props.children, (child) => {
            return React.cloneElement(child, {item});
        })
    }
</ul>
```

c. Passing JSX elements as a props to get rid of clutter and duplicate layout:

```jsx
const RowBlock = ({ left, right }) => {
    return (
        <Row>
            <Col md='6'>
                {left}
            </Col>
            <Col md='6'>
                {right}
            </Col>
        </Row>
    );
};

// ---------------Other file------------------

<RowBlock left={itemList} right={itemDetails}/>
```

*  The child element passes values to the ancestor element

```jsx
const itemDetails = (
    <ItemDetails itemId={this.state.selectedChar}>
        <Field field='gender' label='Gender'/>
        <Field field='born' label='Born'/>
        <Field field='died' label='Died'/>
        <Field field='culture' label='Culture'/>
    </ItemDetails>
);
```

# Router

*  Default Router import:

```jsx
import { BrowserRouter as Router, Route } from 'react-router-dom';
```

*  Passing the id of the book from the histrory API to the router in the URL of the address line

```jsx
import {withRouter} from 'react-router-dom';
export default withRouter(BooksPage);

<ItemList
    onItemSelected={(itemId) => {
        this.props.history.push(`${itemId}`);
    }}
    getData={this.gotService.getAllBooks}
    renderItem={({ name }) => name }/>
```

*  Instead of the anchor tag - Link tag is used for the correct operation of the router:

```jsx
import {Link} from 'react-router-dom';
<Link to='/characters/'>Characters</Link>
```

* Router encloses those elements that will need to be routed. Route is a path, an element that indicates to which component this or that link in the address line will direct.
* In order that the styles are not lost - do extra. a wrapper inside router - a div with a class, with the necessary styles.

```jsx
<Router>
    <div className="app">
        <Container>
            <Header />
        </Container>
        <Container>
            <Row>
                <Col lg={ { "size": 5, "offset": 0 } }>
                    <Content />
                    <Button color="primary" 
                            className="d-flex justify-content-center my-5" 
                            onClick={this.hideRandomChar}>
                            Toggle random character
                    </Button>
                </Col>
            </Row>
            <Route path='/' exact component={() => <h1>Welcome to GOT DB</h1>} />
            <Route path='/characters' component={CharacterPage} />
            <Route path='/books' exact component={BooksPage} />
            <Route path='/books/:id' render={({ match }) => {
                const { id } = match.params;
                return <BooksItem bookId={id} />;
            }} />
            <Route path='/houses' component={HousesPage} />
        </Container>
    </div>
</Router>
```

# DefaultProps & PropTypes

* Library for easy prop types:  

```jsx
import PropTypes from 'prop-types';
```
    
* Proptypes and defaultProps can be declared inside class via 'static':

```jsx
export default class SomeClass extends Component {

     static defaultProps = {
        interval: 30000
     };	
     
     static propTypes = {
        interval: PropTypes.number
     };	
 
 }
```

* Native, without library, propTypes check and Error:

```jsx
SomeClass.propTypes = {
    interval: (props, propName, componentName) => {
        const value = props[propName];

        if (typeof value === 'number' && !isNaN(value)) {
            return null;
        }
        return new TypeError(`${componentName}: ${propName} must be a number`);
    }
};
```

* Proptypes and defaultProps via library, outside class:

```jsx
SomeClass.defaultProps = {
    interval: 30000
};

SomeClass.propTypes = {
    interval: PropTypes.number
};
```

* Native default props:

```jsx
SomeClass2.defaultProps = {
    onItemSelected: () => {}
};
```

*  PropTypes can be very variative (functions, array of smth, etc):

```jsx
SomeClass2.propTypes = {
    onItemSelected: PropTypes.func,
    // getData: PropTypes.arrayOf(PropTypes.object)
};
```

# Higher order components (HOC)

1.  Make function-wrapper, which accepts View component and data-function
2.  Returns anonymous class with all neccesary methods
3.  Return component from anonymous class with needed props, data
4.  Export default function(View, getData)

```jsx
const withData = (View, getData) => {
    // eslint-disable-next-line react/display-name
    return class extends Component {

        state = {
            data: null
        }
    
        componentDidMount() {
    
            getData()
                .then( (data) => {
                    this.setState({
                        data
                    }); 
                });
        }

        render() {

            const {data} = this.state;

            if (!data) {
                return <Spinner/>;
            }
            
            return <View {...this.props} data={data}/>;
        }
    };
};

const {getAllCharacters} = new gotService();
export default withData(ItemList, getAllCharacters());
```

# Hooks

*  Import for hooks:  

```jsx
import React, {useState, useEffect} from 'react';
```

*  Hooks are used in functional components, not class components
*  Hooks must be at the highest lever of component
*  Hook for state, function to change state, initial state:

```jsx
const [count, setCount] = useState(0);
``` 
a. count - state;  
b. setCount - function to change state;  
c. 0 - initial state (inside ***useState(0)***);
    
* Hook for after rendering "effects" (actions). Similar to componentDidMount + componentDidUpdate:  

```jsx
useEffect(functionToPerform() { return cleanFunction() {};}, dataToScanForChanging)
``` 
a. functionToPerform() - inside must be put some action (effect) to perform after rendering of component;  
b. cleanFunction() - similar to componetnWillUnmount. For cleaning, inside useEffect must be returned cleanFunction() to preserve memory leaks (or to e.g. unsubscribe from timer);  
c. dataToScanForChanging - effect are triggered after every render. This could be performance issue. To trigger only for changes in data are used dataToScanForChanging as a second argument in useEffect();
    
```jsx
function Appp() {
    const [count, setCount] = useState(0);
    const [data, refreshData] = useState([{name: 'Ivan', sex: 'male'}]);

    //	useEffect( () => {
    //		console.log(data);
    //	});

    // useEffect( () => {
    // 	updateChar();
    // 	let timerId = setInterval(updateChar, 15000);
    // 	return () => {
    // 		clearInterval(timerId);
    // 	};
    // });

    return (
        <div>
            <p>{count}</p>
            <button 
                onClick={ () => setCount(count+1)}>
                    Click
            </button>
            {data.map(item => {
                return (
                    // eslint-disable-next-line react/jsx-key
                    <div>Name: {item.name}, sex: {item.sex}</div>
                );
            })}
            <button onClick={() => refreshData(data => ([...data, {name: 'Qasda', sex: 'male'}]))}>Add data</button>
        </div>
    );
}

export default Appp;
```

# React context
For using context:
1. Create context
2. Wrap App contents with **Provider** component, import context:
3. Skip unnecesarry components
4. Wrap desired component with **Consumer** component

Component 1:

```js
import React from 'react';
import Wrapper from './components/Wrapper';

const App = () => {
	return(
		<div className="app">
			<Wrapper/>
		</div>
	);
};

export default App;
```

Component 2:

```jsx
import React from 'react';
import Name from './components/Name'

const Wrapper = () => {
	return(
		<div className="wrapper">
			<Name/>
		</div>
	);
};

export default Wrapper;
```

Component 3:

```jsx
import React from 'react';

const Name = () => {
	return(
		<div className="Name">
			My name is {value}
		</div>
	);
};

export default Name;
```
---
## Then all converts to:
* Create context in Component 0:
Component 0:

```jsx
import React from 'react';

const MyContext = React.createContext();
export default MyContext;
```

* Wrap App contents with **Provider** component, import context:
Component 1:

```jsx
import React from 'react';
import Wrapper from './components/Wrapper';
import MyContext from './components/Context';

const App = () => {
	return(
		<div className="app">
			<MyContext.Provider value={'SomeName'}>
				<Wrapper/>
			</MyContext.Provider>
		</div>
	);
};

export default App;
```
* Skipping Wrapper, since it doesn't require context:
Component 2:

```jsx
import React from 'react';
import Name from './components/Name';

const Wrapper = () => {
	return(
		<div className="wrapper">
			<Name/>
		</div>
	);
};

export default Wrapper;
```
* Wrap desired component with **Consumer** component
Component 3:

```jsx
import React from 'react';

const Name = () => {
	return(
		<MyContext.Consumer>
			{
				(value) => {
					return (
						<div className="Name">
							My name is {value}
						</div>
					)			
				}
			}
		</MyContext.Consumer>	
	);
};

export default Name;
```

## For class components
1. Import Context
2. Set context type for component (Name)
3. Use {this.context} inside component with set content;
Component 3:

```jsx
import React, {Component} from 'react';
import MyContext from './Context';

class Name extends Component {
	render() {
		return (
			<div className="Name">
			My name is {this.context}
			// if context is an object, with many props, access could be made by {this.context.propName}. Also methods could be passed in;
			</div>
		);
	}
};

Name.contextType = MyContext:
export default Name;
```
