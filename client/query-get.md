# Query get





### Query get

```text
query="https://neomatrix.herokuapp.com/graphql?query=%7B%0A%20%20neomatrix%7B%0A%20%20%20%20nombre%0A%20%20%20%20linkedin%0A%20%20%7D%0A%7D"
r = Queryclient(query)
r.Info.status == 200 && println(r.Data)
```

