# Schemas and Types



### [Type system](https://neomatrixcode.github.io/Diana.jl/stable/server/#Type-system-1)

If you've seen a GraphQL query before, you know that the GraphQL query language is basically about selecting fields on objects. So, for example, in the following query:

```text
{
  neomatrix{
  	nombre
  	edad
  }
}
```

1. We start with a special "root" object
2. We select the `neomatrix` field on that
3. For the object returned by `neomatrix`, we select the `nombre` and `edad` fields

Because the shape of a GraphQL query closely matches the result, you can predict what the query will return without knowing that much about the server. But it's useful to have an exact description of the data we can ask for - what fields can we select? What kinds of objects might they return? What fields are available on those sub-objects? That's where the schema comes in.

Every GraphQL service defines a set of types which completely describe the set of possible data you can query on that service. Then, when queries come in, they are validated and executed against that schema.

### [Object types and fields](https://neomatrixcode.github.io/Diana.jl/stable/server/#Object-types-and-fields-1)

The most basic components of a GraphQL schema are object types, which just represent a kind of object you can fetch from your service, and what fields it has. In the GraphQL schema language, we might represent it like this:

```text
#schema-first
"""
type Persona {
  nombre: String
  edad: Int!
}
"""

# code-first
Dict("Persona" => Dict(
    "nombre"=>Dict("tipo"=>"String")
    ,"edad"=>Dict("tipo"=>"Int!")
  )
)
```

The language is pretty readable, but let's go over it so that we can have a shared vocabulary:

* `Persona` is a _GraphQL Object Type_, meaning it's a type with some fields. Most of the types in your schema will be object types.
* `nombre` and `edad` are _fields_ on the `Persona` type. That means that `nombre` and `edad` are the only fields that can appear in any part of a GraphQL query that operates on the `Persona` type.
* `String` is one of the built-in _scalar_ types - these are types that resolve to a single scalar object, and can't have sub-selections in the query. We'll go over scalar types more later.
* `Int!` means that the field is _non-nullable_, meaning that the GraphQL service promises to always give you a value when you query this field. In the type language, we'll represent those with an exclamation mark.

Now you know what a GraphQL object type looks like, and how to read the basics of the GraphQL type language.

### [Arguments](https://neomatrixcode.github.io/Diana.jl/stable/server/#Arguments-1)

Every field on a GraphQL object type can have zero or more arguments, for example the `edad` field below:

```text
#schema-first
"""
type Persona {
  nombre: String
  edad(valor:Int): Int!
}
"""

# code-first
Dict("Persona" => Dict(
    "nombre"=>Dict("tipo"=>"String")
    ,"edad"=>Dict("tipo"=>"Int!"
      ,"args"=>Dict(
        "valor"=>"Int")
    )
  )
 )
```

All arguments are named. Unlike languages like JavaScript and Python where functions take a list of ordered arguments, all arguments in GraphQL are passed by name specifically. In this case, the `edad` field has one defined argument, `valor`.

### [The Query and Mutation types](https://neomatrixcode.github.io/Diana.jl/stable/server/#The-Query-and-Mutation-types-1)

Most types in your schema will just be normal object types, but there are two types that are special within a schema:

```text
#schema-first
"""
schema {
  query: Query
  mutation: Mutation
}
"""

# code-first
Dict(
"query" => Query #Dict()
,"mutation" => Mutation #Dict()
)
```

Every GraphQL service has a `query` type and may or may not have a `mutation` type. These types are the same as a regular object type, but they are special because they define the _entry point_ of every GraphQL query. So if you see a query that looks like:

```text
query{
    neomatrix{
    	nombre
    	altura
    }
    persona(id:"200"){
    	nombre
    	edad
    }
}
```

That means that the GraphQL service needs to have a `Query` type with `neomatrix` and `persona` fields:

```text
#schema-first
"""
type Query {
  neomatrix: Persona
  persona(id: ID!): Persona
}
"""

# code-first
Dict(
"Query"   => Dict(
     "neomatrix"=>Dict("tipo"=>"Persona")
    ,"persona"=>Dict("tipo"=>"Persona"
      ,"args"=>Dict(
        "id"=>"ID!")
        )
   )
)
```

Mutations work in a similar way - you define fields on the `Mutation` type, and those are available as the root mutation fields you can call in your query.

It's important to remember that other than the special status of being the "entry point" into the schema, the `Query` and `Mutation` types are the same as any other GraphQL object type, and their fields work exactly the same way.

### [Scalar types](https://neomatrixcode.github.io/Diana.jl/stable/server/#Scalar-types-1)

A GraphQL object type has a name and fields, but at some point those fields have to resolve to some concrete data. That's where the scalar types come in: they represent the leaves of the query.

In the following query, the `nombre` and `edad` fields will resolve to scalar types:

```text
{
  neomatrix{
    nombre
    edad
  }
}
```

We know this because those fields don't have any sub-fields - they are the leaves of the query.

GraphQL comes with a set of default scalar types out of the box:

* `Int`: A signed 64‐bit integer.
* `Float`: A signed double-precision floating-point value.
* `String`: A UTF‐8 character sequence.
* `Boolean`: `true` or `false`.
* `ID`: The ID scalar type represents a unique identifier, often used to refetch an object or as the key for a cache. The ID type is serialized in the same way as a String; however, defining it as an `ID` signifies that it is not intended to be human‐readable.

### [Input types](https://neomatrixcode.github.io/Diana.jl/stable/server/#Input-types-1)

So far, we've only talked about passing scalar values as arguments into a field. But you can also easily pass complex objects. This is particularly valuable in the case of mutations, where you might want to pass in a whole object to be created. In the GraphQL schema language, input types look exactly the same as regular object types, but with the keyword `input` instead of `type`:

```text
# schema-first
"""
input DataInputType{
  nombre: String
  edad: Int
}
"""

# code-first
Dict(
"DataInputType" => Dict(
     "nombre"=>Dict("tipo"=>"String")
    ,"edad"=>Dict("tipo"=>"Int")
   )
)
```

Here is how you could use the input object type in a mutation:

```text

mutation mutationwithvariarbles(\$dato: DataInputType){
  addPerson(data: \$dato ){
    nombre,
    edad
  }
}
```

The fields on an input object type can themselves refer to input object types, but you can't mix input and output types in your schema. Input object types also can't have arguments on their fields.



