# Simple query

## Simple query

```julia
using Diana

query = """
        {
          neomatrix{
            nombre
            linkedin
          }
        }
        """

r = Queryclient("https://neomatrix.herokuapp.com/graphql",
                query,
                headers = Dict("header" => "value"))
r.Info.status == 200 && println(r.Data)
```

**Result**

```text
{
  "data":{
    "neomatrix":{
        "nombre":"Acevedo Maldonado Josue",
        "linkedin":"https://www.linkedin.com/in/acevedo-maldonado-josue/"
    }
  }
}
```

```julia
query = """
        query consulta {
          neomatrix {
            nombre
            linkedin
          }
        }

        query hola {
          neomatrix {
            nombre
          }
        }
        """
r = Queryclient("https://neomatrix.herokuapp.com/graphql",query,operationName="hola")
r.Info.status == 200 && println(r.Data)
```

**Result**

```text
{"data":{"neomatrix":{"nombre":"Acevedo Maldonado Josue"}}}
```

              

