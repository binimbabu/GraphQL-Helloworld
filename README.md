GraphQL

GraphQL is a query language for APIs and a runtime for fulfilling those queries with your existing data. GraphQL provides a complete and understandable description of the data in your API, gives clients the power to ask for exactly what they need and nothing more make it easier to evolve APIs over time and enables powerful developer tools.

Client has full control over which fields to fetch from the server. With RESTful API when you request a resource will get all data in the response. If interested for only some fields in data and that can result in overfetching which means fetching too much data, data thats not actually used by the client. GraphQL provides a solution to this problem, with REST sometime if we want to fetch 2 different resources we need to make 2 separate calls to the server. GraphQL makes to get many resources in a single request (avoids underfetching). GraphQL writes a schema that describes API. GraphQL provides API layer but data and business logic stay same. GraphQL is open specification with many implementations available for different languages.






server.js


const typeDefs = `
schema {
query: Query
}
type Query {
 greeting: String
}`;

const resolvers = {
  Query: {
    greeting: () => "Hello World",
  },
};


we use 'type' keyword and of type is 'Query', inside 'Query' we have fields that can be queried, requested by clients calling this API (type definition denotes the interface for the API that they declare what our clients can request). This 'Query' is a valid GraphQL schema (it declares that any client calling the API can ask for or query for a greeting and the server will return a string value in response ). We can write comments by # followed by comments.
Implementation part that returns a greeting value which include the following code from the above code


const resolvers = {
  Query: {
    greeting: () => "Hello World",
  },
};

 (since the schema has a 'Query' hence 'resolvers' also contains Query object and since the schema 'Query'  contains a property 'greeting', resolver needs property 'greeting' ). But in 'resolvers' the 'greeting' will be an actual function that returns a value ( this function is called a 'resolver' function in GraphQL because it resolves the value of greeting field, in this example we are returning hard coded value but we can execute any code (like load a value from database or randomly generated string or fetch some other API) here in this 'resolver' function ). 'resolvers' are the implementation they provide the code that knows how to return the actual values.

To expose the above API in GraphQL we use a library called Apollo Server.

create package.json file



'private' set to true because we use this project locally not publish it to a registry.   "type": "module" because it enables ES modules (which means able to use JavaScript features in the code)

Set up Apollo Server for GraphQL


npm install @apollo/server graphql


'graphql' will provide graphql features, while Apollo Server expose the API over HTTP.

In server.js import the following

 import { ApolloServer } from "@apollo/server";

In the below piece of code we pass configuration options, where 'typeDefs' property set to 'typeDefs' value of the variable defined. 

const server = new ApolloServer({ typeDefs: typeDefs, resolvers });

Since the property and value variable is of same name we just use it once 


const server = new ApolloServer({ typeDefs, resolvers });

To start the apollo server we need to import the following

import { startStandaloneServer } from "@apollo/server/standalone";

and add the following code to start the server, since startStandaloneServer is a promise we use await to wait for the asynchronous operation

const { url } = await startStandaloneServer(server, { listen: { port: 9000 } });

'url' is the destructuring where it contains url.

run the terminal by the following

node server.js
await startStandaloneServer(server, { listen: { port: 9000 } });

when opening http://localhost:9000/ the sandbox is enabled by default when we run apollo server in development. Sandbox is a web based client to call any GraphQL API. Sandbox automatically loads the schema from the server and generates documentation based on that.

in Sandbox operation we will run query with the following

query {
  greeting
}


we are requesting 'greeting' value and whwn we click Run then it sends the request to server and displays the response on right (as following).

{
  "data": {
    "greeting": "Hello World"
  }
}



Full code server.js


import { ApolloServer } from "@apollo/server";
import { startStandaloneServer } from "@apollo/server/standalone";

const typeDefs = `
schema {
query: Query
}
type Query {
 greeting: String
}`;

const resolvers = {
  Query: {
    greeting: () => "Hello World",
  },
};

const server = new ApolloServer({ typeDefs, resolvers });

const { url } = await startStandaloneServer(server, { listen: { port: 9000 } });



Schema polling enabled in Sandbox which keeps on re-requesting the schema from server to make sure its using latest version ( thats why in network tab the api triggered every second).
All GraphQL requests are sent as HTTP POST.



Create client folder outside server folder and add 'index.html' file.

index.html

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>GraphQL Client</title>
  </head>
  <body>
    <h1>GraphQL Client</h1>
    <p>
      The server says
      <strong id="greeting">Loading...</strong>
    </p>
    <script src="app.js"></script>
  </body>
</html>



Also create app.js


app.js


async function fetchGreeting() {
  const response = await fetch("http://localhost:9000/", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      query: "query {greeting}",
    }),
  });
  const { data } = await response.json();
  return data.greeting;
}

fetchGreeting().then((greeting) => {
  document.getElementById("greeting").textContent = greeting;
});




Since the server is running in 'http://localhost:9000' we put the first argument in fetch as 'http://localhost:9000/' and the second argument is an object which consists of method, headers can be set which is an object. stringify to convert JavaScript object to JSON. The JavaScript object must contain query property, that will be a string containing GraphQL query. We want to make a query to greeting request. The fetch returns a promise hence use async and await. ' const body = response.json()' will get the body of response.
Browser sent HTTP request to GraphQL server and when it received the response it updated the page showing the data. The below code extracted from above is creating a query


const response = await fetch("http://localhost:9000/", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      query: "query {greeting}",
    }),
  });
