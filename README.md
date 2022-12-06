# `remix-sse`

Server Side Events (SSE) and WebSockets for Remix, made easy.

# Installation

```
npm i remix-sse
```

# Usage

1. Setup your emitter route, here it is called `/routes/emitter.tsx` for simplicity

> Note: This **MUST** be a resource route, you cannot return a component from this route.

```.ts
import { EventStream } from 'remix-sse'
export const loader: LoaderFunction = ({ request }) => {

  // Return the EventStream from your route loader
  return new EventStream(request, (send) => {
    // In the init function, setup your SSE Event source
    // This can be any asynchronous data source, that will send
    // events to the client periodically

    // Here we will just use a `setInterval`

    const interval = setInterval(() => {
      // You can send events to the client via the `send` function
      send('greeting', JSON.stringify({ hello: 'world'}))
    }, 1000)


    return () => {
      // Return a cleanup function
      clearInterval(interval)
    };
  });
};
```

> Note: the first argument passed to the `send` function is the `EventKey`, this can be
> anything you want - but you will need to reference it again via `useEventSource`.

2. Call the `useEventSource` hook in the browser to start receiving events.

````.ts
import { useEventSource } from 'remix-sse/client'

const { greeting } = useEventSource('/emitter', ['greeting']);

// This is a react state value, and will update everytime an event is received
console.log(greeting)

```
````

## Options

| Option              | Description                                                                                      | Default |
| ------------------- | ------------------------------------------------------------------------------------------------ | ------- |
| `maxEventRetention` | The maximum number of events that will be kept under each event key in the returned state object | 50      |

## Experimental options

These are currently being tested, and are subject to change at any point.

| Option        | Description                                                                                                                                                              | Default   |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | --------- |
| `deserialize` | A key value object where the key is the name of the event you want to de-serialize, and the value is a `DeserializeFn`. See [deserialize example](/examples/deserialize) | undefined |
