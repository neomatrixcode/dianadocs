# Mutations

Here is a small example using [`SpaceX API`](https://api.spacex.land/graphql/?fbclid=IwAR3bC8SKhjQ0GkYKxH1xGIBb4nl3FYAjAAZHlrQUTdmOH5-ARlhr_aQjxb4)\`\`

```julia
using Random
using Diana
using Test

GET_EMPTY_USER = """
query(\$name: String!){
  users(where: {name: {_eq: \$name}}) {
    name
    rocket
  }
}
"""

my_random_name = randstring(12)

r = Queryclient("https://api.spacex.land/graphql/"
               , GET_EMPTY_USER
               , vars=Dict("name" => my_random_name)
   )

@test r.Info.status == 200
@test r.Data == "{\"data\":{\"users\":[]}}\n"

CREATE_USER = """
mutation(\$name: String!, \$rocket: String! ) {
  insert_users(objects: {name: \$name, rocket: \$rocket}) {
    returning {
      name
      rocket
    }
  }
}
"""

my_random_rocket = randstring(12)

r1 = Queryclient("https://api.spacex.land/graphql/"
                , CREATE_USER
                , vars=Dict(
                       "name" => my_random_name
                     , "rocket" => my_random_rocket
                   )
     )

@test r1.Info.status == 200
@test r1.Data == "{\"data\":{\"insert_users\":{\"returning\":[{\"name\":\"$my_random_name\",\"rocket\":\"$my_random_rocket\"}]}}}\n"

new_random_rocket = randstring(12)

UPDATE_USER = """
mutation(\$name: String!, \$rocket: String!) {
  update_users(where: {name: {_eq: \$name}}, _set: {rocket: \$rocket}) {
    returning {
      name
      rocket
    }
  }
}
"""

r2 = Queryclient("https://api.spacex.land/graphql/"
                , UPDATE_USER
                , vars=Dict(
                       "name" => my_random_name
                     , "rocket" => new_random_rocket
                   )
      )

@test r2.Info.status == 200
@test r2.Data == "{\"data\":{\"update_users\":{\"returning\":[{\"name\":\"$my_random_name\",\"rocket\":\"$new_random_rocket\"}]}}}\n"
```





