---
{"dg-publish":true,"permalink":"/Packages/ValueByPriority/","noteIcon":"","updated":"2023-12-17T06:48:18.844+09:00"}
---



# Summary

An object, `ValueByPriority`, determines the result value based on priority and can have multiple nodes.

## Result Value By Nodes

Consider the following `ValueByPriority` structure:
```lua
ValueByPriority
|- DefaultValue = Vector3.new(1, 5, 150)
|- Nodes
| |- Node1: {
       Priority = 0,
       Value = 100,
       NodeFunction = "Divide",
     }
| |- Node2: {
       Priority = 10,
       Value = Vector3.new(10, 150, 150),
       NodeFunction = "Set",
     }
| |- Node3: {
       Priority = 11,
       Value = 100,
       NodeFunction = "Divide",
     }
| |- Node4: {
       Priority = 160,
       CreatedTime = 0,
       Value = Vector3.new(0.3, 4.5, 0.5),
       NodeFunction = "Lerp",
       CustomData = {
         Alpha = .5,
       }
     }
| |- Node5: {
       Priority = 160,
       CreatedTime = 1,
       Value = workspace.Part,
       NodeFunction = script.fromPartRotation,
       CustomData = {
         InverseRotation = true,
       }
     }
```

Initially, `ValueByPriority` starts with `DefaultValue`, `Vector3.new(1, 5, 150)`
```lua
result = self:GetDefaultValue()
```

Each node modifies the result. For example:
```lua
Node1:
Priority = 0,
Value = 100,
NodeFunction = "Divide"

result = result / Node1.Value
```
The `ValueByPriority` value becomes `Vector3.new(1/100, 5/100, 150/100)`. The process continues with subsequent nodes.

>[!note]- View more processes
>
>```lua
>Node2:
>Priority = 10,
>Value = Vector3.new(10, 150, 150),
>NodeFunction = "Set"
>
>result = Node2.Value
>= Vector3.new(10, 150, 150
>```
>If `NodeFunction` is `"Set"`, it ignores the lower-priority nodes and `DefaultValue`.
>
>```lua
>Node3: 
>Priority = 11,
>Value = 100,
>NodeFunction = "Divide"
>
>result = result / Node3.Value
>= Vector3.new(10/100, 150/100, 150/100)
>= Vector3.new(0.1, 1.5, 1.5)
>```
>
>```lua
>Node4:
>Priority = 160,
>CreatedTime = 0,
>Value = Vector3.new(0.3, 4.5, 0.5),
>NodeFunction = "Lerp",
>CustomData = { Alpha = .5 }
>
>result = lerp(result, Node4.Value, CustomData.Alpha)
>= result + (Node4.Value - result) * CustomData.Alpha
>= Vector3.new(0.1, 1.5, 1.5) + Vector3.new(0.2, 3, -1) * 0.5
>= Vector3.new(0.2, 3, 1)
>```
>
>```lua
>Node5:
>Priority = 160,
>CreatedTime = 1,
>Value = workspace.Part,
>NodeFunction = script.fromPartRotation,
>CustomData = { InverseRotation = true }
>
>--in fromPartRotation ModuleScript:
>return function(lastValue, nodeValue, customData)
>	local partRotation = part.CFrame.Rotation
>	return if customData and customData.InverseRotation
>		then partRotation:Inverse() * lastValue
>		else partRotation * lastvalue
>end
>
>--in ValueByPriority:
>result = require(NodeFunction)(result, Node5.Value, CustomData)
>= Node5.Value.CFrame.Rotation:Inverse() * result
>```
>

> [!Warning] Avoid using floats as Priority
> Although `Priority` can be a float or an integer, using a float may complicate the process. Prefer integers where possible.

> [!info] 
> In practice, the system's behavior might differ due to internal optimizations.
# Node

To add a node, call `ValueByPriority:AddNode(NodeParams)`. 
`NodeFunction` is optional; the default is `"Set"`.`

```lua
local newNode = someVbp:AddNode({
	Value = 16,
	Priority = 10,
	NodeFunction = "Lerp",
	CustomData = {
		alpha = .5,
	},
})

--With Metatable Magic, call `AddNode(params)` by the following way:
local newNode = someVbp {
	Value = 16,
	Priority = 10,
	NodeFunction = "Lerp",
	CustomData = {
		alpha = .5
	},
}
```

## NodeParams

### Priority

The higher the priority, the later the node is applied.

>[!Tip] Nodes with the same priority  
>The system determines that the most recently created node has a higher priority.
>```lua
>Nodes[1] = {
>	Priority = 100,
>	CreatedTime = 1000,
>}
>Nodes[2] = {
>	Priority = 100,
>	CreatedTime = 1002,
>}
>```

`ValueByPriority` has an `Enum.Priority` for easier management.
```lua
local model = ExtendedInstance.new("RootModel")
model.Instance.Parent = workspace

local self = ExtendedInstance.new("Part")
self:SetParent(model)

local sizeNode = self "Size" {
	Priority = 0,
	Value = Vector3.new(100, 100, 100)
}
task.wait()

print( self:GetProperty("Size") ) --> 100, 100, 100
model:SetProperty("Scale", .01) -- The part size is affected by the scale of the model.

task.wait()
print( self:GetProperty("Size") ) --> 1, 1, 1

--sizeNode is no longer affected by the Scale of the model.
sizeNode:SetPriority(ValueByPriority.Enum.Priority.ModelInheritance + 1)

task.wait()
print( self:GetProperty("Size") ) --> 100, 100, 100
```

### NodeFunction
NodeFunction is optional. Default: `"Set"`.`

Types of NodeFunction:
- Set: 
  `result = NodeValue`
  
- Add: 
  `result = LastValue + NodeValue`
  
- Subtract: 
  `result = LastValue - NodeValue`
  
- Multiply: 
  `result = LastValue * NodeValue`
  
- ReverseMultiply 
  `result = NodeValue * LastValue`
  For matrices such as CFrame, the order of multiplication is important.
  
- Divide
  `result = LastValue * NodeValue`
  
- Power
  `result = LastValue ^ NodeValue`
  
- Lerp
  `result = LastValue * (NodeValue - LastValue) * CustomData.Alpha`
  Lerp (linear interpolation) is very useful as it applies the value between the LastValue and the NodeValue.

- CustomNodeFunction
  To assign a custom NodeFunction, define a module that returns a Custom NodeFunction.
  ```lua
  -- in CustomNodeFunction ModuleScript:
  return function(lastValue: any, nodeValue: any, customData: {[string]:any}?)
	  local result
	  -- Codes to calculate the result value
	  return result
  end
  
  -- in Parent Script:
  local node = valueByPriority:AddNode({
	  Priority = 1,
	  Value = 1,
	  NodeFunction = script.CustomNodeFunction
  })
  ```

  >[!Warning]
  >Lua functions are not allowed as custom `NodeFunction`s for parallel computation purposes.
  >They must be modules, Unable to transmit lua function between `Actors`
  >In the future, ValueByPriority will be operated in parallel.
# Implementation

This section covers how `ValueByPriority` is implemented.

>[!Warning] 
>This information is not always up to date. 
>The module source code varies internally frequently and is improved. 
>This section is intended to help you understand `ValueByPriority` more, and may differ from your current version.
## Calculating Result Value

`ValueByPriority` does not immediately calculate the result after changes to `DefaultValue` or Nodes. 
It waits slightly (`task.defer()`) before calculating.