# Execution

After being validated, a GraphQL query is executed by a GraphQL server which returns a result that mirrors the shape of the requested query, as JSON.

GraphQL cannot execute a query without a type system, let's use an example type system to illustrate executing a query. This is a part of the same type system used throughout the examples:

```text
#schema-first
"""
type Persona {
  nombre: String
  edad: Int
}
 type Query{
  persona: Persona
  neomatrix: Persona
}
"""

# code-first
schema = Dict(
"query" => "Query"
,"Query"   => Dict(
    "persona"=>Dict("tipo"=>"Persona")
     ,"neomatrix"=>Dict("tipo"=>"Persona")
   )
,"Persona" => Dict(
    "edad"=>Dict("tipo"=>"Int")
    ,"nombre"=>Dict("tipo"=>"String")
  )
)

```

In order to describe what happens when a query is executed, let's use an example to walk through.

```text
query{
  neomatrix{
  	nombre
  }
}
```

You can think of each field in a GraphQL query as a function or method of the previous type which returns the next type. In fact, this is exactly how GraphQL works. Each field on each type is backed by a function called the _resolver_ which is provided by the GraphQL server developer. When a field is executed, the corresponding _resolver_ is called to produce the next value.

If a field produces a scalar value like a string or number, then the execution completes. However if a field produces an object value then the query will contain another selection of fields which apply to that object. This continues until scalar values are reached. GraphQL queries always end at scalar values.



### Root fields & resolvers

At the top level of every GraphQL server is a type that represents all of the possible entry points into the GraphQL API, it's often called the _Root_ type or the _Query_ type.

In this example, our Query type provides a field called `persona` which accepts the argument `nombre`. The resolver function for this field likely accesses a database and then constructs and returns a `Persona` object.

```text
resolvers=Dict(
    "Query"=>Dict(
        "neomatrix" => (root,args,ctx,info)->(
        	return Dict("nombre"=>"josue","edad"=>25,"altura"=>10.5,"spooilers"=>false)
        	)
        ,"persona" => (root,args,ctx,info)->(
        	return Dict("nombre"=>"Diana","edad"=>14,"altura"=>11.8,"spooilers"=>true)
        	)
    )
)
```

A resolver function receives four arguments:

* `root` The previous object, which for a field on the root Query type is often not used.
* `args` The arguments provided to the field in the GraphQL query.
* `context` A value which is provided to every resolver and holds important contextual information like the currently logged in user, or access to a database.
* `info` A value which holds field-specific information relevant to the current query as well as the schema details.

#### Context

The context variable is used to pass data between the resolvers or to store global variables, its value is defined when creating the scheme

```text
 my_schema = Schema(schema, resolvers, context=Dict("data"=>"datacontext"))
```

### Trivial resolvers

Now that a `Persona` object is available, GraphQL execution can continue with the fields requested on it.

```text
resolvers=Dict(
    "Query"=>Dict(
        "neomatrix" => (root,args,ctx,info)->(
        	return Dict("nombre"=>"josue","edad"=>25,"altura"=>10.5,"spooilers"=>false)
        	)
        ,"persona" => (root,args,ctx,info)->(
        	return Dict("nombre"=>"Diana","edad"=>14,"altura"=>11.8,"spooilers"=>true)
        	)
    )
    ,"Persona"=>Dict(
      "edad" => (root,args,ctx,info)->(
      	return root["edad"])
    )
)
```

A GraphQL server is powered by a type system which is used to determine what to do next. Even before the `persona` field returns anything, GraphQL knows that the next step will be to resolve fields on the `Persona` type since the type system tells it that the `persona` field will return a `Persona`.

Resolving age in this case is very straight-forward. The name resolver function is called and the `root` argument is the `new Persona` object returned from the previous field. In this case, we expect that Human object to have a `edad` property which we can read and return directly.

In fact, Diana will allow you to skip resolvers that simple and will simply assume that if a resolver is not provided for a field, a property of the same name must be read and returned

### Producing the result

As each field is resolved, the resulting value is placed into a dictionary with the field name as the key and the resolved value as the value, this continues from the bottom leaf fields of the query all the way back up to the original field on the root Query type. Collectively these produce a structure that mirrors the original query which can then be sent \(JSON\) to the client which requested it.

Let's take one last look at the original query to see how all these resolving functions produce a result:

```text
{
  persona{
    nombre
  }
}
```

result:

```text
{"datos":{
  "persona":{
    "nombre":"Diana"
    }
  }
}
```



