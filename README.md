# REDUX-BUCHELI-COURSE-REDUCER-COMPOSITION

## REDUX-LESSONS-INSTRUCTOR-ANDRES-R.-BUCHELI

## Usage:

In the last exercise, you saw how a single reducer was able to handle the logic for updating every slice of the store‘s state. Though this approach does work for these
relatively small examples, as the application state becomes increasingly more complex, managing it all with a single reducer will become impractical.

The solution is to follow a pattern called reducer composition. In this pattern, individual slice reducers are responsible for updating only one slice of the application’s 
state, and their results are recombined by a rootReducer to form a single state object.

 
```
// Handles only `state.todos`.
const initialTodos = [
  { id: 0, text: 'learn redux', completed: false },
  { id: 1, text: 'build a redux app', completed: true },
  { id: 2, text: 'do a dance', completed: false },
];
const todosReducer = (todos = initialTodos, action) => {
  switch (action.type) {
    case 'todos/addTodo': 
      return [...todos, action.payload]
    case 'todos/toggleTodo':
      return todos.map(todo => {
        return (todo.id === action.payload.id) ? 
          { ...todo, completed: !todo.completed } : 
          {...todo};
      });
    default:
      return todos;
  }
};
 
// Handles only `state.filter`
const initialFilter = 'SHOW_INCOMPLETE',
const filterReducer = (filter = initialFilter, action) => {
  switch (action.type) {
    case 'filter/setFilter':
      return action.payload;
    default:
      return filter;
};
 
const rootReducer = (state = {}, action) => {
  const nextState = {
    todos: todosReducer(state.todos, action),
    filter: filterReducer(state.filter, action)
  };
  return nextState;
};
 
const store = createStore(rootReducer);
```

In the reducer composition pattern, when an action is dispatched to the store:

* The rootReducer calls each slice reducer, regardless of the action.type, with the incoming action and the appropriate slice of the state as arguments.
* The slice reducers each determine if they need to update their slice of state, or simply return their slice of state unchanged.
* The rootReducer reassembles the updated slice values in a new state object.

One major advantage of this approach is that each slice reducer only receives its slice of the entire application’s state. Therefore, each slice reducer only needs to immutably
update its own slice and doesn’t care about the others. This removes the problem of copying potentially deeply nested state objects.

Take a look at store.js where you will find that the reducer for the Recipe app that you wrote in the last exercise (which can be found in reducer-old.js) has been partially
rewritten to follow the reducer composition pattern:

* The initialState object has been replaced by individual initialSliceName variables which are used as default values for each slice reducer’s slice of state. This is another
common feature of the reducer composition pattern.
* The allRecipesReducer and searchTermReducer slice reducers have been created for you. Notice that they each have a default case.
* Both slice reducers are called within the rootReducer to update their respective slices of state.

All that’s left is to complete the favoriteRecipesReducer() and include it in the rootReducer()!
