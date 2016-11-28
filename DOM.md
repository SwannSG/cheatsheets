# Document Object Model

## Events

We know the js engine looks for *events* on the *event queue*.
- How do we "look for" specific events

### Event Types

-UI Events (*load, unload, error, resize, scroll*) 
- Keyboard Events (*keydown, keyup, keypress*)
- Mouse Events (*click, dblclick, mousedown, mouseup, mousemove, mouseover, mouseout*)
- Focus Events (*focus, focusin, blur, focusout*)
-Form Events (*input, change, submit, ~~reset~~, cut, copy, paster, select*)
- ~~Mutation Events~~  (*DOMSubtreeModified, DOMNodeInserted, DOMNodeRemoved, DOMNodeInsertedIntoDocument, DOMNodeRemovedFromDocument*) replaced by **Mutation Observers**

### Looking for specific event

1. Select element or node from which event will be generated.
2. Which **event** on selected node(s) are we "looking for". **Bind** event to a node.
3. Code to execute when target event occurs. 

### How to bind an event to an element

1. HTML event handler

		// html domain
		<button onclick="doSomething()">Do Something</button>
		
		// js domain
		function doSomething() {{
		
2. Traditional DOM event handler

		// js domain
		function doSomething() {};
		element.onevent = doSomething;	// not parenthesis are omitted
		
		// real example
		function checkUsername() {}
		el = document.getElementById('username')
		el.onblur = checkUsername 
		
		// passing paramenter to a function
		function checkUsername(par1) {}
		el.onblur = function() {			// anonymouse function wrapper
				checkUsername(par1)	// call named function with desired args
			}
		

		
3. Event listeners (modern prefered method)


		// moder event listeners
		// element or node
		// event in quotes, eg. 'blur'
		// function to execute
		// boolean is called capture (normally false, related to event flow  
		element.addEventListener('event', function [,boolean]);
		
		// real example
		function checkUsername() {}		// function before addEventListener
		el = document.getElementById('username')
		el.addEventListener('blur', checkUserName, false) // not 'onblur'

		// to remove an event listener use removeEventListener
		
		// real example with aguments
		function checkUsername(par1) {}		// function before addEventListener
		el = document.getElementById('username')
		el.addEventListener('blur', function() {
				checkUserName(par1)},
			false) //

### Event Flow


