# Quick start

GraphQL is a query language for API created by Facebook. See more complete documentation at [http://graphql.org/](http://graphql.org/). Looking for help? Find resources from the community.

An overview of GraphQL in general is available in the [README](https://github.com/facebook/graphql/blob/master/README.md) for the Specification for GraphQL.

Diana is a library that provides tools to implement a GraphQL API in Julia using both the code-first like [Graphene](https://github.com/graphql-python/graphene#examples) \(Python\) approach and the schema-first like [Ariadne](https://github.com/mirumee/ariadne/#quickstart) \(Python\). Diana produces schemas that are fully compliant with the GraphQL spec.

This package is intended to help you building GraphQL schemas/types fast and easily.

* **Easy to use:** Diana.jl helps you use GraphQL in Julia without effort.
* **Data agnostic:** Diana.jl supports any type of data source: SQL, NoSQL, etc. The intent is to provide a complete API and make your data available through GraphQL.
* **Make queries:** Diana.jl allows queries to graphql schemas.



## Installation

First [download](https://julialang.org/downloads/#current_stable_release) and install Julia. `1.5` or higher.                                                                                                                     To do the installation use any of the following commands:

```julia
using Pkg
Pkg.add("Diana")
```

```julia
Pkg> add Diana
#Release
pkg> add Diana#master
#Development
```



## An example in Diana

Let’s build a basic GraphQL schema to say “hello” and “goodbye” in Diana.

```julia
using Diana

schema = Dict(
"query" => "Query"
,"Query"   => Dict(
    "hello"=>Dict("tipo"=>"String")
   ,"goodbye"=>Dict("tipo"=>"String")
   )
)

resolvers=Dict(
    "Query"=>Dict(
        "hello" => (root,args,ctx,info)->(return "Hello World!")
        ,"goodbye" => (root,args,ctx,info)->(return "Goodbye World")
    )
)

my_schema = Schema(schema, resolvers)
```

For each Field in our Schema, we write a Resolver method to fetch data requested by a client’s Query using the current context and Arguments.

## Schema Definition Language \(SDL\)

In the GraphQL Schema Definition Language, we could describe the fields defined by our example code as show below.

```julia
using Diana

 schema= """
 type Query{
  hello: String
  goodbye: String
}
 schema {
  query: Query
}
"""

resolvers=Dict(
    "Query"=>Dict(
        "hello" => (root,args,ctx,info)->(return "Hello World!")
        ,"goodbye" => (root,args,ctx,info)->(return "Goodbye World")
    )
)

my_schema = Schema(schema, resolvers)
```

## Querying

Then we can start querying our Schema by passing a GraphQL query string to execute:

```julia
query= """
query{
  hello
}
"""

result = my_schema.execute(query)

println(result)
# {"data":{"hello":"Hello World!"}}
```

Congrats! You got your first Diana schema working!

