---
{"dg-publish":true,"permalink":"/Packages/ValueByPriority/","noteIcon":"","updated":"2023-12-15T06:08:49.894+09:00"}
---



# Summary

An object that determines the result value based on the priority.

# API

## Methods

### AddNode
```lua
AddNode: (parentVbp:ValueByPriority, params:NodeParams) -> Node
```

```lua
--Recommended For Readability:
local newNode = someVbp:AddNode({
	Value = 16,
	Priority = 10,
	NodeFunction = "Lerp",
	CustomData = {
		alpha = .5,
	},
})

--You can call `AddNode(params)` with Metatable Magic,
--Although it's shorter than before, Not recommended for readability. (For now)
local newNode = someVbp {
	Value = 16,
	Priority = 10,
	NodeFunction = "Lerp",
	CustomData = {
		alpha = .5
	},
}
```

# Node

