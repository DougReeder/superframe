## aframe-state-component

State management for [A-Frame](https://aframe.io) using single global state
modified through actions. Features declarative bindings to easily bind state to
application. By separating state from components and being able to bind state
to component properties, components can be decoupled from the application, not
needing to know about its state.

No dependencies and tailored for A-Frame. Bindings will only update their
entities if relevant pieces of the global state are modified.

### Usage

The application state is a singleton defining an initial state and handler
functions that modify the state.

```js
AFRAME.registerState({
  initialState: {
    score: 0
  },

  handlers: {
    decreaseScore: function (state, action) {
      state.score -= action.points;
    },

    increaseScore: function (state, action) {
      state.score += action.points;
    }
  }
});
```

Then we can declarative bind pieces of the state into the A-Frame application
with the `bind` component:

```html
<a-scene>
  <a-entity bind__text="value: app.score"></a-entity>
  <!-- Or <a-entity bind="text.value: app.score"> -->
</a-scene>
```

To update the state, we can dispatch an action using an event:

```js
AFRAME.scenes[0].emit('increaseScore', {points: 50});
```

Or manually dispatched:

```js
AFRAME.scenes[0].systems.state.dispatch('increaseScore', {points: 50});
```

The binding components will automatically and selectively update the entities
in response to state changes.

To attach additional computed state after the action is processed, specify a
`computeState` function to update the state:

```js
AFRAME.registerState({
  // ...

  computeState: function (newState, payload) {
    newState.isRedOrBlue = newState.isRed || newState.isBlue;
  }
});
```

### Installation

#### Browser

Install and use by directly including the [browser files](dist):

```html
<head>
  <title>My A-Frame Scene</title>
  <script src="https://aframe.io/releases/0.8.0/aframe.min.js"></script>
  <script src="https://unpkg.com/aframe-state-component/dist/aframe-state-component.min.js"></script>
  <script>
    AFRAME.registerState({
      initialState: {
        enemyPosition: {x: 0, y: 1, z: 2}
      },

      handlers: {
        enemyMoved: function (state, action) {
          state.enemyPosition = action.newPosition;
        }
      },
    });
  </script>
</head>
<body>
  <a-scene>
    <a-entity bind__position="foo.enemyPosition"></a-entity>
  </a-scene>
</body>
```

#### npm

Install via npm:

```bash
npm install aframe-state-component
```

Then register and use.

```js
require('aframe');
require('aframe-state-component');
```