---
{"dg-publish":true,"permalink":"/frameworks/style-guide/","updated":"2023-12-14T04:29:16.263+09:00"}
---



[Roblox-lua-style-guide](https://roblox.github.io/lua-style-guide/)

# To Start
## Required knowledge

### Programming Naming Convention
#### camelCase
A camelCase is a naming convention that begins with lowercase letters.  
Used for local variables, local functions, names of modules that return a single function, and so on.
#### PascalCase
PascalCase is a naming convention that begins with capital letters.  

Example
```lua
local camelCase = "it begins with lowercase"
local PascalCase = "it begins with uppercase"
```

Used for services, modules, names of instances (but use [[Frameworks/Style-Guide#camelCase\|#camelCase]] when referring to instances as local variables), objects returned by modules, property names of objects, method names of objects, types of objects returned by modules, and so on.

# Style
## Naming

use [[Frameworks/Style-Guide#camelCase\|#camelCase]] for constructors

Example:
`Instance.new`
`Font.fromEnum`
`ValueByPriority.byId`