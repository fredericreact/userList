# Wrapper component

## Reusable card component which wrap components

1. Wrap component between Card component

```javascript
   <Card className={classes.input}>
    <form onSubmit={addUserHandler}>
        <label htmlFor="username">Username</label>
        <input id="username" type="text"/>
        <label htmlFor="age">Age (Years)</label>
        <input id="age" type="number"/>
        <button type="submit">Add User</button>
    </form>
    </Card>
```

```javascript
import React from 'react';
import classes from './Card.module.css'
const Card = (props) => {
    return <div className={`${classes.card} ${props.className}`}>{props.children}</div>;
}
 
export default Card;
```

2. Reusable button component

```javascript
<Card className={classes.input}>
    <form onSubmit={addUserHandler}>
        <label htmlFor="username">Username</label>
        <input id="username" type="text" value={enteredUsername} onChange={usernameChangeHandler}/>
        <label htmlFor="age" >Age (Years)</label>
        <input id="age" type="number" value={enteredAge} onChange={ageChangeHandler}/>
        <Button type="submit">Add User</Button>
    </form>
    </Card>
```

```javascript
import React from "react";
 
import classes from './Button.module.css';
 
 
const Button = (props) => {
    return (
        <button
        className={classes.button}
        type={props.type || 'button'}
        onClick={props.onClick}
        >
        {props.children}
        </button>
    )
}
 
export default Button;
```

# Div Soup

Pour chaque component, tu dois retourner un seul element, donc on le wrap dans une div
Sau que ca rend ton code html crade parce que tu as plein de div inutile

![image](https://user-images.githubusercontent.com/104289891/198816157-ce04173d-d6fe-49ad-b452-bf32ce942d1e.png)

On peut pallier a ca en remplacant la div par un empty wrapper component :
Doesnt render any real html element to the dom

![image](https://user-images.githubusercontent.com/104289891/198816162-550b1446-db6a-45ec-8425-433cdc07bb9f.png)

Exemple de custom empty wrapper component :

```javascript
const Wrapper = props => {
return props.children;
}
 
export default Wrapper
```

```javascript
   <Wrapper>
   
    {error && <ErrorModal
    title={error.title}
    message={error.message}
    onConfirm={errorHandler}/>}
   
    <Card className={classes.input}>
    <form onSubmit={addUserHandler}>
        <label htmlFor="username">Username</label>
        <input id="username" type="text" value={enteredUsername} onChange={usernameChangeHandler}/>
        <label htmlFor="age" >Age (Years)</label>
        <input id="age" type="number" value={enteredAge} onChange={ageChangeHandler}/>
        <Button type="submit">Add User</Button>
    </form>
    </Card>
    </Wrapper>
```

Mais pas besoin de le creer nous meme a chaque fois, on peut utiliser un truc fourni par react :  React fragment


```javascript
 return (
    <React.Fragment>
      <AddUser onAddUser={addUserHandler}/>
      <UsersList users={usersList}/>
    </React.Fragment>
  );

```
ou

```javascript
import React, {useState, Fragment} from 'react';
 
  return (
    <Fragment>
      <AddUser onAddUser={addUserHandler}/>
      <UsersList users={usersList}/>
    </Fragment>
  );
}
 
export default App;
```

# React Portal

Modals should be at the top

![image](https://user-images.githubusercontent.com/104289891/198816291-2921e143-5c3b-4676-acac-150f7705a032.png)

We can use portals : we keep the structure on the left but render the modal somewhere else

![image](https://user-images.githubusercontent.com/104289891/198816298-f87cb22d-0ccc-4425-9cb3-36bff21402e6.png)

Add 2 divs in the html

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <link rel="icon" href="%PUBLIC_URL%/favicon.ico" />
    <meta name="viewport" content="width=device-width, initial-scale=1" />
    <meta name="theme-color" content="#000000" />
    <meta
      name="description"
      content="Web site created using create-react-app"
    />
    <link rel="apple-touch-icon" href="%PUBLIC_URL%/logo192.png" />
    <!--
      manifest.json provides metadata used when your web app is installed on a
      user's mobile device or desktop. See https://developers.google.com/web/fundamentals/web-app-manifest/
    -->
    <link rel="manifest" href="%PUBLIC_URL%/manifest.json" />
    <!--
      Notice the use of %PUBLIC_URL% in the tags above.
      It will be replaced with the URL of the `public` folder during the build.
      Only files inside the `public` folder can be referenced from the HTML.
 
      Unlike "/favicon.ico" or "favicon.ico", "%PUBLIC_URL%/favicon.ico" will
      work correctly both with client-side routing and a non-root public URL.
      Learn how to configure a non-root public URL by running `npm run build`.
    -->
    <title>React App</title>
  </head>
  <body>
    <noscript>You need to enable JavaScript to run this app.</noscript>
    <div id="backdrop-root"></div>
    <div id="overlay-root"></div>
    <div id="root"></div>
    <!--
      This HTML file is a template.
      If you open it directly in the browser, you will see an empty page.
 
      You can add webfonts, meta tags, or analytics to this file.
      The build step will place the bundled scripts into the <body> tag.
 
      To begin the development, run `npm start` or `yarn start`.
      To create a production bundle, use `npm run build` or `yarn build`.
    -->
  </body>
</html>
```

Then use ReactDom.createPortal

reactDOM allows react to be rendered on the dom (or a web browser)


```javascript
import React from "react";
import Button from "./Button";
import Card from './Card';
import classes from './ErrorModal.module.css'
import ReactDOM from 'react-dom';
 
const Backdrop = (props) => {
    return <div className={classes.backdrop} onClick={props.onConfirm}/>
}
 
const ModalOverlay =(props) =>{
    return (
<Card className={classes.modal}>
    <header className={classes.header}>
        <h2>{props.title}</h2>
    </header>
    <div className={classes.content}>
        <p>{props.message}</p>
    </div>
    <footer className={classes.actions}>
        <Button onClick={props.onConfirm}>Okay</Button>
    </footer>
</Card>
    )
}
 
const ErrorModal =(props) =>{
    return (
 
        <React.Fragment>
       
        {ReactDOM.createPortal(
            <Backdrop
            onConfirm={props.onConfirm}/>,
            document.getElementById('backdrop-root')
        )}
       
        {ReactDOM.createPortal(
            <ModalOverlay
                title={props.title}
                message={props.message}
                onConfirm={props.onConfirm}
            />,
            document.getElementById('overlay-root')
        )}
</React.Fragment>
    )
}
 
export default ErrorModal;
```

# useRef()

Input controlled : on update notre state a chaque fois qu’on sait qq chose dans le clavier , ce qui est pas optimal

Ref permet dacceder a des dom elements et de work with them
On connect html element to the ref
Du coup ce ne sont plus des input controlled et on n’utilise plus de state


```javascript
import React , {useState, useRef} from 'react';
import Card from '../UI/Card';
import classes from './AddUser.module.css';
import Button from '../UI/Button'
import ErrorModal from '../UI/ErrorModal';
import Wrapper from '../Helper/Wrapper'
const AddUser = (props) => {
 
const nameInputRef =  useRef();
const ageInputRef =useRef()
 
 
const [error,setError]=useState();
 
 
    const addUserHandler = (event) => {
        event.preventDefault();
        const enteredName = nameInputRef.current.value
        const enteredUserAge = ageInputRef.current.value
        if(enteredName.trim().length ===0 || enteredUserAge.trim().length===0) {
           setError({
            title: 'invalid input',
            message: 'please enter valid name'
           })
            return;
        }
 
        if (+enteredUserAge<1) {
            setError({
                title: 'invalid age',
                message: 'please enter valid age'
               })
            return;
        }
 
        props.onAddUser(enteredName,enteredUserAge);
        nameInputRef.current.value='';
        ageInputRef.current.value='';
    }
 
 
 
    const errorHandler =() =>{
        setError(null);
    }
 
return(
    <Wrapper>
   
    {error && <ErrorModal
    title={error.title}
    message={error.message}
    onConfirm={errorHandler}/>}
   
    <Card className={classes.input}>
    <form onSubmit={addUserHandler}>
        <label htmlFor="username">Username</label>
        <input
        id="username"
        type="text"
 
        ref={nameInputRef}
        />
        <label htmlFor="age" >Age (Years)</label>
        <input
        id="age"
        type="number"
 
        ref={ageInputRef}    
        />
        <Button type="submit">Add User</Button>
    </form>
    </Card>
    </Wrapper>
)
}
 
 
export default AddUser;
```

