iris Handler for [graphl-go](https://github.com/graphql-go/graphql)

### Usage

```go
package main

import (
	"github.com/kataras/iris/v12"
	"github.com/yesixuan/iris-graphql-handler/vicGraphql"
)

func graphqlHandler(ctx iris.Context)  {
    schema, _ := graphql.NewSchema(...)
    
    h := vicGraphql.New(&vicGraphql.Config{
        Schema: &schema,
        Pretty: true,
        GraphiQL: true,
    })
    result := h.ServeHTTP(ctx)
    ctx.JSON(result)
}

func main() {
    app := iris.Default()
    app.Post("/graphql", graphqlHandler)
    app.Run(iris.Addr(":8080"))
}
```

### Using Playground
```go
h := vicGraphql.New(&handler.Config{
	Schema: &schema,
	Pretty: true,
	GraphiQL: false,
	Playground: true,
})
```

### Details

The handler will accept requests with
the parameters:

  * **`query`**: A string GraphQL document to be executed.

  * **`variables`**: The runtime values to use for any GraphQL query variables
    as a JSON object.

  * **`operationName`**: If the provided `query` contains multiple named
    operations, this specifies which operation should be executed. If not
    provided, an 400 error will be returned if the `query` contains multiple
    named operations.

GraphQL will first look for each parameter in the URL's query-string:

```
/graphql?query=query+getUser($id:ID){user(id:$id){name}}&variables={"id":"4"}
```

If not found in the query-string, it will look in the POST request body.
The `handler` will interpret it
depending on the provided `Content-Type` header.

  * **`application/json`**: the POST body will be parsed as a JSON
    object of parameters.

  * **`application/x-www-form-urlencoded`**: this POST body will be
    parsed as a url-encoded string of key-value pairs.

  * **`application/graphql`**: The POST body will be parsed as GraphQL
    query string, which provides the `query` parameter.