
# Using Apollo's useQuery Hooks to Fetch Data #

## Introduction ##

In earlier versions of Apollo, a `Query` component was used to query data. With the introduction of React Hooks in 16.8 and the sharing of stateful logic through functions, those `Query` components can now be replaced by functional components and the new `useQuery()` hook. This reduces the amount of code needed for queries, and provides a simple way to fetch data.

In this article, you'll learn:

- how to fetch data using the `useQuery()` hook
- how to poll and refetch data

This article assumes that you have a solid understanding of writing GraphQL queries, a general knowledge of what hooks are, and that you are currently writing your React components as functions instead of classes. For additional info regarding React Hooks, take a look at their documentation [here](https://reactjs.org/docs/hooks-intro.html).

## Fetching Data ##

For this exercise, we're going to pretend that we have a space shuttle service. We want to create a query that returns the name and id of each space shuttle. To begin, open a new file. Import the `gql` function to parse the query and the `useQuery` hook from the `@apollo/react-hooks` package. Your code will look something like this:

```javascript
import gql from 'graphql-tag';
import { useQuery } from '@apollo/react-hooks';

const GET_SHUTTLES = gql`
  {
    shuttles {
      id
      name
    }
  }
`;
```

Next, we want to create a new function component `Shuttles`. We will pass our query into the `useQuery` hook, and then create a list with the query data that's returned.

```javascript
function Shuttles() {
  const { loading, error, data } = useQuery(GET_SHUTTLES);

  if (loading) return 'Loading...';
  if (error) return `Error! ${error.message}`;

  return (
    <ul>
      {data.shuttles.map(shuttle => (
        <li key={shuttle.id}>
          {shuttle.name}
        </li>
      ))}
    </ul>
  );
}
```

Now, we have a simple component that returns shuttles via `useQuery`. You'll notice that in addition to `data`, `useQuery` returns an object with `loading` and `error` states needed to conditionally handle UI scenarios.

## Fetching Data with Variables ##

Let's add another query that returns an image for a specific shuttle.

```javascript
const GET_SHUTTLE_PHOTO = gql`
  query Shuttle($id: ID!) {
    shuttle(id: $id) {
      image
    }
  }
`;
```

We can create a new `ShuttlePhoto` component with a prop of `id` and use the `useQuery` hook like this:

```javascript

function ShuttlePhoto({ id }) {
  const { loading, error, data } = useQuery(GET_SHUTTLE_PHOTO, {
    variables: { id },
  });

  if (loading) return null;
  if (error) return `Error! ${error}`;

  return (
    <img src={data.shuttle.image} style={{ height: 100, width: 100 }} />
  );
}
```

You can see how easy `useQuery` makes fetching data both with and without variables.

## Additional Functionality ##

In addition to basic queries, the `useQuery` hook also has some added functionality.

- **Polling**:

In the event that you need to regularly call the query on the server, you can add a `pollInterval` config option like so:

```javascript
const { loading, error, data } = useQuery(GET_SHUTTLES, {
    pollInterval: 500,
});
```

This ensures that the `GET_SHUTTLES` query gets executed every 500ms.

- **Refetching**:

Sometimes, it's necessary to refetch data. There is also a refetch function that is returned via the `useQuery` object.

```javascript
const { loading, error, data, refetch } = useQuery(GET_SHUTTLES);
```

Something as simple as a button click could then be used to refetch the shuttle data.

```javascript
function Shuttles() {
  const { loading, error, data } = useQuery(GET_SHUTTLES);

  if (loading) return 'Loading...';
  if (error) return `Error! ${error.message}`;

  return (
    <div>
        <ul>
        {data.shuttles.map(shuttle => (
            <li key={shuttle.id}>
            {shuttle.name}
            </li>
        ))}
        </ul>
        <button onClick={() => refetch()}>Refetch!</button>
    </div>
  );
}
```

## Conclusion ##

Apollo's `useQuery` hook provides a simple way to not only fetch data, but to handle UI states and other more complex scenarios like polling and fetching. With GraphQL on the rise, it's a great time to learn and implement. If you're interested in learning about even more things that it can do, definitely take a look at the [API docs](https://www.apollographql.com/docs/react/data/queries/#usequery-api) and play around with it yourself!
