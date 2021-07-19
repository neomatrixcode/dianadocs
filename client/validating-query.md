# Validating query







### [Validating query](https://neomatrixcode.github.io/Diana.jl/stable/client/#Validating-query-1)

It is possible to validate the query locally before sending the request, only basic validations are carried out.

```text
query = """
        {
          neomatrix{
            nombre
            linkedin
          }
        }
        """
r = Queryclient("https://neomatrix.herokuapp.com/graphql",query, check = true)
```

result:

```text
"ok"
```

```text
 query = """
         {
         neomatrix {
           nombre
           linkedin
         }

         """
r = Queryclient("https://neomatrix.herokuapp.com/graphql",query, true)
#r = client.Query(query, check = true)
```

result:

```text
ERROR: Diana.GraphQLError("{\"errors\":[{\"locations\": [{\"column\": 10,\"line\": 6}],\"message\"
```

