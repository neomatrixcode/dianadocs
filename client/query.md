# Query



```julia
using Diana

client = GraphQLClient("https://api.graph.cool/simple/v1/movies")
client.serverAuth("Bearer my-jwt-token")
client.headers(Dict("header"=>"value"))

or

client = GraphQLClient("https://api.graph.cool/simple/v1/movies"
                      ,auth="Bearer my-jwt-token"
                      ,headers=Dict("header"=>"value") 
         )
```

```julia
query = """
        {
          Movie(title: "Inception"){
            actors{
              name
            }
          }
        }
        """

r = client.Query(query)
r.Info.status == 200 && println(r.Data)
```

result:

```text
{
  "data":{
    "Movie":{
      "actors":[
        {
          "name":"Leonardo DiCaprio"
        },
        {
          "name":"Ellen Page"
        },
        {
          "name":"Tom Hardy"
        },
        {
          "name":"Joseph Gordon-Levitt"
        },
        {
          "name":"Marion Cotillard"
        }
      ]
    }
  }
}
```

```text
query = """
        query getMovie(\$title: String!) {
          Movie(title:\$title) {
            releaseDate
            actors {
              name
            }
          }
        }
        """
r = client.Query(query, vars= Dict("title" => "Inception"))

println(r.Data)
```

```text
query = """
        query consulta {
          Movie(title: "Inception") {
            actors{
              name
            }
          }
        }
        query hola {
          Movie(title: "Inception") {
            actors{
              name
            }
          }
        }
        """
r = client.Query(query, operationName = "consulta")
r.Info.status == 200 && println(r.Data)
```

result:

```text
{"data":{"Movie":{"actors":[{"name":"Leonardo DiCaprio"},{"name":"Ellen Page"},{"name":"Tom Hardy"},{"name":"Joseph Gordon-Levitt"},{"name":"Marion Cotillard"}]}}}
```

```text
using Diana
github_endpoint = "https://api.github.com/graphql"
github_user = # GitHub handle
github_token = # GitHub personal token
github_header = Dict("User-Agent" => github_user)
client = GraphQLClient(github_endpoint,
                       auth = "bearer $github_token",
                       headers = github_header)
query = """
        query A{
          rateLimit {
            cost
            remaining
            resetAt
          }
          repository(owner: "JuliaLang", name: "Julia") {
            id
          }
        }"""

r = client.Query(query, operationName = "A")
r.Info.status == 200 && println(r.Data)
```

