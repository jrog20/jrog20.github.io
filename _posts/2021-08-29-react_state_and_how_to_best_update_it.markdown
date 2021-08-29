---
layout: post
title:      "React State and how to best update it"
date:       2021-08-29 23:03:33 +0000
permalink:  react_state_and_how_to_best_update_it
---


React is a component-based user interface library. This means that a React application consists of a set of components that forms a tree with a root and child components.

More often than not, React components will have state. State is a plain JavaScript object which holds information that affects the output of the render() method, i.e what is displayed on the screen. It can be formed of variables that contain things like whether a user is logged in or not, or an array of posts, or a count of some kind. 

The state is managed within the component and is initialized in the constructor. Once state is initialized, it should only be updated using the setState() method. 

Before we get into the next part, a quick note about JavaScript. 

JavaScript, at its base, is a Synchronous, blocking, single-threaded language. This means that only one thing can happen at a time, on a single main thread, and everything else is blocked until an operation completes.

**Spoiler: State Updates May Be Asynchronous.**

React batches multiple setState() calls into a single update for performance. This is because setState alters the state and causes rerendering. This can be an expensive operation and making it synchronous might leave the browser unresponsive. And we don’t want our users staring at an empty page while waiting for our code to finish running.

Thus the setState calls are asynchronous as well as batched for better UI experience and performance. 

Because this.state may be updated asynchronously, you should not rely on their values for calculating the next state. 

Take this code for example: 

```
state = { 
   count: 0 
}

updateCount = () => { 
   this.setState({ count: this.state.count + 1}); 
   this.setState({ count: this.state.count + 1}); 
   this.setState({ count: this.state.count + 1}); 
   this.setState({ count: this.state.count + 1}); 
}
```

Since there are multiple setState calls, React will batch all those calls and updates the state only in the last call of setState, so at the time of last call to setState this.state.count is not yet updated and its value is still the same as it was before entering into the method so the resulting state will contain count incremented by 1.

To fix this, we can use a second form of setState() that accepts a function rather than an object. That function will receive the previous state as an argument.

```
state = { 
   count: 0 
}

updateCount = () => { 
   this.setState(prevState => ({ count: prevState.count + 1})); 
   this.setState(prevState => ({ count: prevState.count + 1})); 
   this.setState(prevState => ({ count: prevState.count + 1})); 
   this.setState(prevState => ({ count: prevState.count + 1})); 
}
```

React will again batch all those calls and update the state only in the last call of setState, however in this example, the prevState will always contain the modified state in the recent setState call. As prevState.count value has already been incremented 3 times by the last call of setState, the resultant state will contain the count value incremented by 4.

What is the difference between passing an object or a function in setState?

Passing an update function allows you to access the current state value inside the updater. Since setState calls are batched, this lets you chain updates and ensure they build on top of each other instead of conflicting.

