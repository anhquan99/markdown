# Feature
- JSX is a syntax extension to [[Javascript Fundamentals]]. It is used with React. By using JSX we can write HTML structure in the same file that contains JS code. Web browsers can't read JSX directly.
- Components: building blocks of any React application, and single app usually consist of multiple components. It can be split into independent, reusable part that can be processed separately
-   Virtual DOM: React keeps a lightweight representation of the real DOM in the memory, and that is known as the virtual DOM. When the state of an object changes, virtual DOM changes only that object in the real DOM, rather than updating all the objects.
# Advantages
- Easy creation of dynamic applications
- Improve performance
- Reusable components
# Traversal list using map
# Components
- Functional
	- Has no state and contains only render methods. Also known as stateless components
- Class
	- Has state and a separate render method
# State
- Is a built-in React object that is used to contain data or information about the component. It can change over time  and when ever it change the component re-renders.
- Update state:
	- In class use setState
	- In function use React hook
- Should use the previous state to update the current state of the object because ReactJS is not controll all the state.
# Pros
- Short for Properties. It is a  React built-in object that store value of the attribute of a tag.
- Provide a way to pass data from component to other components
# Lifecycle methods of components
-   **getInitialState():** This is executed before the creation of the component.
-   **componentDidMount():** Is executed when the component gets rendered and placed on the DOM.
-   **shouldComponentUpdate():** Is invoked when a component determines changes to the DOM and returns a “true” or “false” value based on certain conditions.
-   **componentDidUpdate():** Is invoked immediately after rendering takes place.
-   **componentWillUnmount():** Is invoked immediately before a component is destroyed and unmounted permanently.