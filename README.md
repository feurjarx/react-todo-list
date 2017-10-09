# react-todo-list 
https://codepen.io/yakovenkoroman/pen/aLYZVv?editors=0110

```js
class Todo extends React.Component {
  render() {
    let {id, title, completed} = this.props;
    return (
      <div className="space-between full-width">
        <div className={completed ? 'completed' : ''}>
          <span>{title}</span>
          <small> "{id}"</small>
        </div>
        <div>
          {this.props.children}
        </div>
      </div>
    )
  }
}

class TodoList extends React.Component {
  
  handleTodoDelete(todoId) {
    return () => this.props.onTodosDelete([todoId])
  }
  
  handleTodoComplete(todo) {
    return () => this.props.onTodoUpdate(
      todo.id, 
      Object.assign({}, todo, {
        completed: true
      })
    )
  }
  
  handleTodoSelect(nextSelectedTodo) {
    let {selectedTodo} = this.props;
    return () => this.props.onTodoSelect(
      nextSelectedTodo === selectedTodo ? null : nextSelectedTodo
    );
  }
  
  render() {
    let {todos, selectedTodo} = this.props;
    if (!todos.length) {
      return (
        <i>Todos not found</i>
      )
    }
    return (
      <ul>
        {todos.map((todo, i) => (
          <li 
            key={i} 
            className={`hovered gutters ${selectedTodo === todo ? 'selected' : ''}`}
            onClick={this.handleTodoSelect(todo)}
          >
            <Todo 
              id={todo.id}
              title={todo.title} 
              completed={todo.completed}
            >
              {!todo.completed && (
                <button onClick={this.handleTodoComplete(todo)}>Done</button>
              )}
              <button onClick={this.handleTodoDelete(todo.id)}>X</button>
            </Todo>
          </li>
        ))}
      </ul>
    )
  }
}

TodoList.defaultProps = {
  todos: [],
  onTodoUpdate: Function(),
  onTodosDelete: Function(),
  onTodoSelect: Function()
}

class TodoPanel extends React.Component {
  handleTodoCreate() {
    this.props.onTodoCreate();
  }
  
  handleFormChange(key, valueKey = 'value') {
    let {suggestedTodo} = this.props;
    return (event) => (
      this.props.onSuggestTodoChange(
        Object.assign({}, suggestedTodo, {
          [key]: event.target[valueKey]
        })
      )
    );
  }
 
  handleTodoUpdate() {
    let {selectedTodo, suggestedTodo} = this.props;
    this.props.onTodoUpdate(selectedTodo.id, suggestedTodo);
  }
  
  render() {
    let {suggestedTodo, selectedTodo} = this.props;
    return (
      <div className="space-between full-width">
        <div>
          <label>Title
            <input 
              type="text" 
              value={suggestedTodo.title}
              onChange={this.handleFormChange('title')} 
            />
          </label>
          &nbsp;
          <label>Completed
            <input 
              checked={suggestedTodo.completed}
              type="checkbox" 
              onChange={this.handleFormChange('completed', 'checked')} 
            />
          </label>
        </div>
        <div>
          {!!selectedTodo && (
            <button onClick={this.handleTodoUpdate.bind(this)}>
              <span>Save</span>
            </button>
          )}
          <button onClick={this.handleTodoCreate.bind(this)}>
            <span>Add</span>
          </button>
        </div>
      </div>
    );
  }
}

TodoPanel.defaultProps = {
  onTodoCreate: Function(),
  onTodoUpdate: Function(),
  onSuggestTodoChange: Function()
}

class TodoApplication extends React.Component {
  
  defaultTodo = {
    id: null,
    title: '',
    completed: false
  };
  
  constructor() {
    super();
    this.state = {
      selectedTodo: null,
      suggestedTodo: this.defaultTodo,
      todos: [{
        id: this.generateTodoId(),
        title: 'Todo #1',
        completed: false
      }, {
        id: this.generateTodoId(),
        title: 'Todo #2',
        completed: false
      }, {
        id: this.generateTodoId(),
        title: 'Todo #3',
        completed: false
      }]
    };    
  }
  
  generateTodoId() {
    return `todo_${Math.random() % Date.now()}`;
  }

  handleTodoCreate() {
    let {todos, suggestedTodo} = this.state;
    todos.push(Object.assign({}, suggestedTodo, {
      id: this.generateTodoId()
    }));
    
    this.setState({todos});
  }

  handleTodosDelete(todoIds) {
    let {todos} = this.state;
    this.setState({
      todos: todos.filter((todo) => (
        !todoIds.includes(todo.id)
      ))
    })
  }

  handleTodoUpdate(todoId, todoDetails) {
    let {todos} = this.state;
    let neededTodo = todos.find((todo) => (
      todo.id === todoId
    ));
    
    Object.assign(neededTodo, todoDetails);
    
    this.setState({todos});
  }
  
  handleTodoSelect(selectedTodo) {
    this.setState({
      selectedTodo,
      suggestedTodo: selectedTodo || this.defaultTodo
    });
  }

  handleSuggestTodoChange(suggestedTodo) {
    this.setState({suggestedTodo});
  }
  
  render() {
    let {todos, selectedTodo, suggestedTodo} = this.state;
    return (
      <div className="container">
        <TodoPanel 
          suggestedTodo={suggestedTodo}
          selectedTodo={selectedTodo}
          onTodoUpdate={this.handleTodoUpdate.bind(this)}
          onSuggestTodoChange={this.handleSuggestTodoChange.bind(this)}
          onTodoCreate={this.handleTodoCreate.bind(this)}
        />
        <hr />
        <TodoList 
          todos={todos}
          selectedTodo={selectedTodo}
          onTodoUpdate={this.handleTodoUpdate.bind(this)}
          onTodosDelete={this.handleTodosDelete.bind(this)}
          onTodoSelect={this.handleTodoSelect.bind(this)}
        />
      </div>
    )
  }
}

React.render(<TodoApplication />, document.getElementById('app'));

```
