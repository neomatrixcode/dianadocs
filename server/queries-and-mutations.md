# Queries and Mutations

### Fields

At its simplest, GraphQL is about asking for specific fields on objects. Let's start by looking at a very simple query and the result we get when we run it:

```text
{
  persona {
    nombre
  }
}
```

You can see immediately that the query has exactly the same shape as the result. This is essential to GraphQL, because you always get back what you expect, and the server knows exactly what fields the client is asking for.

The field `nombre` returns a `String` type.

> Oh, one more thing - the query above is _interactive_. That means you can change it as you like and see the new result. Try adding an `edad` field to the `persona` object in the query, and see the new result.

In the previous example, we just asked for the nombre of our persona which returned a String, but fields can also refer to Objects. In that case, you can make a _sub-selection_ of fields for that object. GraphQL queries can traverse related objects and their fields, letting clients fetch lots of related data in one request, instead of making several roundtrips as one would need in a classic REST architecture.

### Arguments

If the only thing we could do was traverse objects and their fields, GraphQL would already be a very useful language for data fetching. But when you add the ability to pass arguments to fields, things get much more interesting.

```text
{
  persona(id: "1000") {
    nombre
    altura
  }
}
```

In a system like REST, you can only pass a single set of arguments - the query parameters and URL segments in your request. But in GraphQL, every field and nested object can get its own set of arguments, making GraphQL a complete replacement for making multiple API fetches. You can even pass arguments into scalar fields, to implement data transformations once on the server, instead of on every client separately.

```text
{
  persona(id: "1000") {
    nombre
    altura(unidad: String)
  }
}
```

Arguments can be of many different types.

[Read more about the GraphQL type system here.](https://graphql.org/learn/schema)

### Operation name

Up until now, we have been using a shorthand syntax where we omit both the `query` keyword and the query name, but in production apps it's useful to use these to make our code less ambiguous.

Here’s an example that includes the keyword `query` as _operation type_ and `PersonaNombreandEdad` as _operation name_ :

```text

query PersonaNombreandEdad {
  persona {
    name
    edad
  }
}
```

The _operation type_ is either _query_, _mutation_, or _subscription_ and describes what type of operation you're intending to do. The operation type is required unless you're using the query shorthand syntax, in which case you can't supply a name or variable definitions for your operation.

The _operation name_ is a meaningful and explicit name for your operation. It is only required in multi-operation documents, but its use is encouraged because it is very helpful for debugging and server-side logging. When something goes wrong either in your network logs or your GraphQL server, it is easier to identify a query in your codebase by name instead of trying to decipher the contents. Think of this just like a function name in your favorite programming language. For example, in JavaScript we can easily work only with anonymous functions, but when we give a function a name, it's easier to track it down, debug our code, and log when it's called. In the same way, GraphQL query and mutation names, along with fragment names, can be a useful debugging tool on the server side to identify different GraphQL requests.

```text
my_schema.execute(query, operationName="PersonaNombreandEdad")
```

### Variables

So far, we have been writing all of our arguments inside the query string. But in most applications, the arguments to fields will be dynamic: For example, there might be a dropdown, or a search field, or a set of filters.

It wouldn't be a good idea to pass these dynamic arguments directly in the query string, because then our client-side code would need to dynamically manipulate the query string at runtime, and serialize it into a GraphQL-specific format. Instead, GraphQL has a first-class way to factor dynamic values out of the query, and pass them as a separate dictionary. These values are called _variables_.

When we start working with variables, we need to do three things:

1. Replace the static value in the query with `\$variableName`
2. Declare `\$variableName` as one of the variables accepted by the query
3. Pass `variableName: value` in the separate, transport-specific \(JSON\) variables dictionary

Here's what it looks like all together:

```text

query PersonaNombreandEdad(\$identificador: ID) {
  persona(id: \$identificador) {
    nombre
    edad
  }
}
```

Now, in our client code, we can simply pass a different variable rather than needing to construct an entirely new query. This is also in general a good practice for denoting which arguments in our query are expected to be dynamic - we should never be doing string interpolation to construct queries from user-supplied values.

```text
my_schema.execute(query,Variables=Dict("identificador"=>"100"))
```

### Variable definitions

The variable definitions are the part that looks like `(\$identificador: ID)` in the query above. It works just like the argument definitions for a function in a typed language. It lists all of the variables, prefixed by `\$`, followed by their type, in this case `ID`.

All declared variables must be either scalars, or input object types. So if you want to pass a complex object into a field, you need to know what input type that matches on the server. Learn more about input object types on the Schemas and Types seccion.

Variable definitions can be optional or required. In the case above, since there isn't an `!` next to the `ID` type, it's optional. But if the field you are passing the variable into requires a non-null argument, then the variable has to be required as well.

To learn more about the syntax for these variable definitions, it's useful to learn the GraphQL schema language. The schema language is explained in detail on the Schemas and Types seccion.

### Mutations

Most discussions of GraphQL focus on data fetching, but any complete data platform needs a way to modify server-side data as well.

In REST, any request might end up causing some side-effects on the server, but by convention it's suggested that one doesn't use `GET` requests to modify data. GraphQL is similar - technically any query could be implemented to cause a data write. However, it's useful to establish a convention that any operations that cause writes should be sent explicitly via a mutation.

```text
#schema-first
schema = """
type Persona {
  nombre: String
  edad: Int
  altura: Float
  spooilers:Boolean
}
 type Query{
  persona: Persona
  neomatrix: Persona
}
type Mutation{
  addPerson(nombre:String,edad:Int): Persona
}
 schema {
  query: Query
  mutation:Mutation
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

,"mutation" => "Mutation"
,"Mutation" => Dict(

    "addPerson"=>Dict(

      "args"=>Dict(
        "nombre"=>"String",
        "edad"=>"Int")

      ,"tipo"=>"Persona"

      )

  )
)


resolvers=Dict(
    "Query"=>Dict(
        "neomatrix" => (root,args,ctx,info)->(
          return Dict("nombre"=>"josue","edad"=>25,"altura"=>10.5,"spooilers"=>false)
          )
        ,"persona" => (root,args,ctx,info)->(return Dict("nombre"=>"Diana","edad"=>14,"altura"=>11.8,"spooilers"=>true))
    )
    ,"Persona"=>Dict(
      "edad" => (root,args,ctx,info)->(return root["edad"])
    )
    ,"Mutation"=>Dict(
        "addPerson" => (root,args,ctx,info)->(
          return Dict("nombre"=>args["nombre"],"edad"=>args["edad"],"altura"=>10.5,"spooilers"=>false)
          )
    )
)


 my_schema = Schema(schema, resolvers)
```

Just like in queries, if the mutation field returns an object type, you can ask for nested fields. This can be useful for fetching the new state of an object after an update. Let's look at a simple example mutation:

```text
my_schema.execute("""
mutation mutationwithvariables(\$dato: DataInputType){
  addPerson(data: \$dato ){
    nombre,
    edad
  }
}
""",Variables=Dict("dato"=>Dict("edad"=>20,"nombre"=>"bob")))
#{"data":{"addPerson":{"edad":20,"nombre":"bob"}}}
```

Note how `addPerson` field returns the `nombre` and `edad` fields of the newly created review. This is especially useful when mutating existing data, for example, when incrementing a field, since we can mutate and query the new value of the field with one request.

You might also notice that, in this example, the `data` variable we passed in is not a scalar. It's an _input object type_, a special kind of object type that can be passed in as an argument. Learn more about input types on the Schemas and Types seccion.

### Multiple fields in mutations

A mutation can contain multiple fields, just like a query. There's one important distinction between queries and mutations, other than the name:

**While query fields are executed in parallel \(in this version the execution is in series\), mutation fields run in series, one after the other.**

This means that if we send two `incrementCredits` mutations in one request, the first is guaranteed to finish before the second begins, ensuring that we don't end up with a race condition with ourselves.

