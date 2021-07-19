# Link





### [Link](https://neomatrixcode.github.io/Diana.jl/stable/client/#Link-1)

It is possible to get links to the graphql query editor

```text
query = """
        {
          neomatrix{
            nombre
            linkedin
          }
        }
        """
r = Queryclient("https://neomatrix.herokuapp.com/graphql",query,getlink=true)
```

result:

```text
"https://neomatrix.herokuapp.com/graphql?query=%7B%0A%20%20neomatrix%7B%0A%20%20%20%20nombre%0A%20%20%20%20linkedin%0A%20%20%7D%0A%7D%0A"
```

or

```text
r = client.Query(query, getlink = true)
```

result:

```text
"https://api.graph.cool/simple/v1/movies?query=%7B%0A%20%20Movie%28title%3A%20%22Inception%22%29%7B%0A%20%20%20%20actors%7B%0A%20%20%20%20%20%20name%0A%20%20%20%20%7D%0A%20%20%7D%0A%7D%0A"
```

```text
query = """
        query consulta {
          neomatrix {
            nombre
          }
        }
        query hola {
          neomatrix {
            nombre
            linkedin
          }
        }
        """
r = Queryclient("https://neomatrix.herokuapp.com/graphql",
                query,
                getlink = true,
                operationName = "consulta")
```

result:

```text
"https://neomatrix.herokuapp.com/graphql?query=query%20consulta%7B%0A%20%20neomatrix%7B%0A%20%
```

