---
{"dg-publish":true,"permalink":"/Frameworks/Style Guide/","noteIcon":"","updated":"2023-12-16T08:06:50.324+09:00"}
---

Adhere to the guidelines set out in the [Roblox Lua Style Guide](https://roblox.github.io/lua-style-guide/).
# Getting Started

## Essential Knowledge

### Programming Naming Conventions

Various naming conventions are used in programming, and Roblox Lua is no exception. The conventions employed in Roblox include:

```lua
local camelCase = "starts with a lowercase letter"
local PascalCase = "starts with an uppercase letter"
```
#### camelCase
`camelCase` starts with a lowercase letter and is used for local variables, local functions, and names of modules that return a single function.

#### PascalCase
`PascalCase` starts with an uppercase letter and is used for services, modules, instance names (use camelCase for local variables representing instances), objects returned by modules, property names, method names, and types of objects returned by modules.

#### LOUD_SNAKE_CASE
`LOUD_SNAKE_CASE` involves all uppercase letters with underscores `_` as separators.
This is used primarily for constants.
# Style

## Naming

### Modules

#### Object Names

Use [[Frameworks/Style Guide#camelCase\|Style Guide#camelCase]] for constructors.

Example:
```lua
local instance = Instance.new("Part")
local extendedInstance = ExtendedInstance.new("SpinModifier")
local font = Font.fromEnum(Enum.Font.Code)
local valueByPriority = ValueByPriority.byId(
	ExtendedInstanceId.GetFromSubclassProperty(
		extendedInstance, "Modifier", "ModifierPriority"
	)
)
local dataObject = DataObject.fromInstance(workspace.RootModel)
```

For properties, methods, and events, use [[Frameworks/Style Guide#PascalCase\|Style Guide#PascalCase]]. 
Example:
```lua
BasePart.Size = Vector3.new(10, 10, 10)

if ExtendedInstance.Destroyed then
	return
end

ValueByPriority.Changed:Connect(function(res)
	print(`New Value: {res}`)
end)
```

Use` _camelCase` for private properties, methods, and events. 
Example:
```lua
local cache = extendedInstance._cacheProperties
valueByPriority:_update()
```

##### Type Declaration

When specifying an object type, mirror the object's name.

For objects using a metatable, apply the metatable to the type using `typeof(setmetatable())`. Include private properties at your discretion.
##### Complete Example
```lua
-- in Componenet.lua module:
local Component = {}
Component.__index = Component

export type Component = typeof(setmetatable(
	{} :: {
		Name: string,
		IsDestroyed: boolean,
		Destroying: Signal.Class,
		_hiddenProperty: any, --you don't have to include private properties.
	},
	Component
))
function Component.new(): Component
	local self = setmetatable({}, Component)
	self.Name = "name"
	self.IsDestroyed = false
	self._hiddenProperty = ""

	return self
end

function Component:Destroy()
end

function Component:_render()
end

return Component

--in another script:
local Component = require(path.to.module)

local lists = {}
local function addComponentToLists(component: Component.Component)
	table.insert(lists, component)
end

local component = Componenet.new()
wiat(5)
addComponentToLists(component)
wait(5)
component:Destroy()
```

#### Enums
Use PascalCase for keys in Enums.
### Variables

#### Local Variables

Local variables are typically in camelCase.
However, use PascalCase for services or modules.
```lua
local ReplicatedStorage = game:GetService("ReplicatedStorage")

local ObjectModules = ReplicatedStorage.ObjectModules
local ObjectClass = require(ObjectModules.Class)
-- Variables and modules typically share the same name for clarity. However, 
-- combining them with their parent folder names is also acceptable for better context.
-- Example: local PlayerData = require(Player.Data)
```
##### Constant
Constants should be in [[Frameworks/Style Guide#LOUD_SNAKE_CASE\|LOUD_SNAKE_CASE]].
```lua
-- Set true to debug raycasting and normals.
local DEBUG_MODE = true

local MAX_SIZE = 2^16
```
#### table

For tables exposed to external scripts or as properties of objects returned by module scripts, use PascalCase for keys. For internal script use, keys can be in camelCase.

##### Dictionary

`<Item>By<Key>` format
```lua
local characters = {} :: {Character}
local charactersByPlayer = {} :: {[Player]: Character}

local monsterInfos = {} :: {MonsterInfo}
local monsterInfosByMonsterId :: {[MonsterId]: MonsterInfo}
```

