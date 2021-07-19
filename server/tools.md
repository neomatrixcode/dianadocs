# Tools



Diana provides the Parser and the Lexer of the graphql for anyone to create their own tools and implementations

The lexer is built based on the [Tokenize](https://github.com/KristofferC/Tokenize.jl) package code and the Parser on the [graphql-js](https://github.com/graphql/graphql-js) package. **Thanks people.**

### [Parser](https://neomatrixcode.github.io/Diana.jl/stable/server/#Parser-1)

```text
using Diana

Parse("""
      #
      query {
        Region(name: "The North") {
          NobleHouse(name: "Stark") {
            castle {
              name
            }
            members{
              name
              alias
            }
          }
        }
      }
      """)
```

result:

```text
 < Node :: Document ,definitions : Diana.Node[
 < Node :: OperationDefinition ,operation : query ,selectionSet :
 < Node :: SelectionSet ,selections : Diana.Node[
 < Node :: Field ,name :
 < Node :: Name ,value : Region >  ,arguments : Diana.Argument[
 < Node :: Argument ,name :
 < Node :: Name ,value : name >  ,value : (":",
 < Node :: StringValue ,value : The North > ) > ] ,selectionSet :
 < Node :: SelectionSet ,selections : Diana.Node[
 < Node :: Field ,name :
 < Node :: Name ,value : NobleHouse >  ,arguments : Diana.Argument[
 < Node :: Argument ,name :
 < Node :: Name ,value : name >  ,value : (":",
 < Node :: StringValue ,value : Stark > ) > ] ,selectionSet :
 < Node :: SelectionSet ,selections : Diana.Node[
 < Node :: Field ,name :
 < Node :: Name ,value : castle >  ,selectionSet :
 < Node :: SelectionSet ,selections : Diana.Node[
 < Node :: Field ,name :
 < Node :: Name ,value : name >  > ] >  > ,
 < Node :: Field ,name :
 < Node :: Name ,value : members >  ,selectionSet :
 < Node :: SelectionSet ,selections : Diana.Node[
 < Node :: Field ,name :
 < Node :: Name ,value : name >  > ,
 < Node :: Field ,name :
 < Node :: Name ,value : alias >  > ] >  > ] >  > ] >  > ] >  > ] >
```

### [Lexer](https://neomatrixcode.github.io/Diana.jl/stable/server/#Lexer-1)

```text
using Diana

Tokensgraphql("""
              #
              query {
                Region(name: "The North") {
                  NobleHouse(name: "Stark") {
                    castle {
                      name
                    }
                    members {
                      name
                      alias
                    }
                  }
                }
              }
              """)
```

result:

```text
29-element Array{Diana.Tokens.Token,1}:
 NAME           query               2,15 - 2,19
 LBRACE         {                   2,21 - 2,21
 NAME           Region              3,31 - 3,36
 LPAREN         (                   3,37 - 3,37
 NAME           name                3,38 - 3,41
 COLON          :                   3,42 - 3,42
 STRING         The North           3,44 - 3,54
 RPAREN         )                   3,55 - 3,55
 LBRACE         {                   3,57 - 3,57
 NAME           NobleHouse          4,49 - 4,58
 LPAREN         (                   4,59 - 4,59
 NAME           name                4,60 - 4,63
 COLON          :                   4,64 - 4,64
 STRING         Stark               4,66 - 4,72
 RPAREN         )                   4,73 - 4,73
 LBRACE         {                   4,75 - 4,75
 NAME           castle              5,69 - 5,74
 LBRACE         {                   5,76 - 5,76
 NAME           name                6,91 - 6,94
 RBRACE         }                   7,111 - 7,111
 NAME           members             8,131 - 8,137
 LBRACE         {                   8,139 - 8,139
 NAME           name                9,153 - 9,156
 NAME           alias               10,175 - 10,179
 RBRACE         }                   11,195 - 11,195
 RBRACE         }                   12,213 - 12,213
 RBRACE         }                   13,229 - 13,229
 RBRACE         }                   14,243 - 14,243
 ENDMARKER                          15,257 - 15,257
```

