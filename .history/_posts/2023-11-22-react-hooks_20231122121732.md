---
title: React Hooks Explained
categories: [js,react]
tags: [hooks,react,state-management]

---


# React Hooks

## Overview

Hello! 

This post will be about use cases and examples of some commonly used hooks in React JS. For those who don't know, hooks are functions that allow you to alter the global state of your functional components without the use of lifecycle methods.

The purpose of using hooks is to provide a more lightweight, concise, and readable way of handling state management. Although using hooks in a large scale application with more complex state can get a bit…messy. So in cases like that you’d more than likely use a library like Redux. Which I’ll cover some examples in a future post. But for this post I’ll cover multiple cases of the useState hook and the useEffect hook. So let’s hop into it!


## USE STATE

With all hooks you want to start with importing them into the component you’ll be building the state in. So let’s start with importing useState. After importing useState, you’ll want to define instances of the state you want to alter within your component. The syntax is pretty straightforward, you define the state and set the state like this below.
```js
import React, { useState } from 'react';

const StateManagmentComp = () => {

  const [myNum, setMyNum] = useState(0);

```

Inside the useState function you want to define the default state dependent on what datatype you’re using. So if you were changing an integer value’s state you would have a number as the default state. Below are two more examples with an array and a boolean.

```js
  const [myArray, setMyArray] = useState([]);

  const [myBoolean, setMyBoolean] = useState(false);

```
Now that your default states are defined, let’s look at some basic functionality you can create to manipulate each one. 

## INTEGERS

So again with the integer example you’d want to create a new function that interacts with your default state. After you create a new function, call on the function we typed earlier that alters the default state. So if you named it setMyInteger, you call it within the new function and pass the variable “myInteger” to it, with whatever number value you want to alter it with. 

You can also have multiple functions that do different things with the state. So with the integer example you may want to add an increment, decrement, and reset function, all of this is possible and concise using hooks. 

The code would look something like this: 
```js

  const incrementNum = () => {
    setMyInteger(myNum + 1);
  };
  const decrementNum = () => {
      setMyInteger(myNum - 1);
  };
 const resetNum = () => {
     setMyInteger(0)
 };

```

## BOOLEAN

The next example is really effective for conditional rendering, with booleans. Booleans set values to true or false, and you can alter your UI to present certain information based on the state of these values. For example if you have a component fetching data and you want to return loading text or a message to the user; This would be a great use of boolean state. 
Basically you’d define the default state of the loading function as we did before, type a function that manipulates that state, and have your UI reflect and alter the current state.
I’ve included some example code of this as well:



```js
import React, { useState, useEffect } from 'react';

const LoadingExample = () => {
 
  const [isLoading, setIsLoading] = useState(true);

  // Simulating an asynchronous operation like data loading with useEffect
  useEffect(() => {
 
    const fetch = setTimeout(() => {
      setIsLoading(false); // Set isLoading to false after the fetch (like data loading)
      clearTimeout(fetch);
    }, 3000); // Simulating a 3-second delay
  }, []); 

  return (
    <div>
      {isLoading ? (
          
        <p>Loading...</p>
      ) : (
        <div>
          <h1>Data Loaded!</h1>
        <h2> here is your content </h2>
        </div>
      )}
    </div>
  );
};

export default LoadingExample;

```

## ARRAY

For the last useState example let’s use an array. Arrays are good for altering and creating lists, or just manipulating traversable data. For this example let’s add an input value and map it to an array. So you would have two default instances of state here. You’d have the inputValue state and the arrayState. We’d do the same steps as before just for both instances. We’d create our default state, create functions to manipulate that state. Then create UI to manipulate/reflect those state changes.  

The UI for this one is a bit more complex, but basically you create an event that takes whatever string value is dynamically typed by the user to the setInputValue function, then pass it to the inputValue state; 

After that the next function checks to see if the string is empty using the trim function that's built into JavaScript. This checks for and removes white space so by checking if there is no empty string ‘ ’ first, you’re preventing empty strings from being passed. After that you grab the previous set of items in the array and pass that string as the next item. Finally, reset the input value to an empty string.

Then use an input field and add the default state as the value property and our function to alter the inputValue state as the onChange property. After this we create a button to push the item to the array and map over the items in an unordered list.  

The code example would look something like this:

```js
import React, { useState } from 'react';

const ArrayInputComponent = () => {

  const [items, setItems] = useState([]);
  const [inputValue, setInputValue] = useState('');


  const handleInputChange = (event) => {
    setInputValue(event.target.value);
  };
  const addItem = () => {
    // Prevent adding empty strings to the array
    if (inputValue.trim() !== '') {
      setItems([...items, inputValue]);
      setInputValue(''); 
      }
  };

  return (
    <div>
      <input
        type="text"
        value={inputValue}
        onChange={handleInputChange}
        placeholder="Enter item"
      />
      <button onClick={addItem}>Add Item</button>

      <ul>
        {items.map((item, index) => (
          <li key={index}>{item}</li>
        ))}
      </ul>
    </div>
  );
};

export default ArrayInputComponent;

```


## USE EFFECT
The final hook we’ll explore today is the useEffect hook. useEffect is commonly used to trigger functions asynchronously. This is effective if you need a component to update after information is fetched, or if the DOM(Document Object Model) is manually updated. Another thing about useEffect is the ability to pass dependencies that trigger the function to re-run. 

 So basically if you have a variable called currentUser that updates whenever you log in or out, You can pass this to useEffect and run a function each time currentUser is updated or changed in the application. If you don't pass any dependencies it will run similarly to the componentDidMount function in class based components, so only once. You can even combine useState functions with useEffect. 
 
 Basically whatever functions you’d type for useState you can wrap them in the useEffect function and they will load asynchronously and can refresh with whatever parameters you pass to the dependency array. useEffect is also great for catching side effects that may fall outside of the scope of your component like data fetching.



## IN CONCLUSION

React hooks are an effective tool for any React Developer and I hope this explanation was useful! As I said I will explore more complex state management with Redux in a future post.

Until then!

