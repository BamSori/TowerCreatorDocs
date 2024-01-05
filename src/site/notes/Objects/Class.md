---
{"dg-publish":true,"permalink":"/Objects/Class/","noteIcon":"","updated":"2024-01-06T01:09:40.438+09:00"}
---

Classes in `ObjectModules.Class` are designed for managing both [[Objects/DataObject\|DataObjects]] and [[Objects/ExtendedInstance\|ExtendedInstances]].

>[!Error]
>I've made quite a few changes to the classes. Please wait for the document to be updated. Sorry for any inconvenience.
>01-06-2024 Bam_Sori

## Structure

A `Class` comprises an array of `Subclasses` within the `Class.Classes` module. These subclasses collectively define the functionalities and properties of a class.
## Subclass

Subclasses, which are integral parts of a class, should be defined in the `Class.Subclasses` module.

>[!Tip] 
>For better organization, especially when dealing with numerous subclasses, group them into sub-categories within child modules. 
>Then, integrate these into the main `Subclasses` module. Here's how you can do it:
>```
>Class
>|- Subclasses
>| |- Physics
>| |- Parts
>| |- Modifiers
>```
>```lua
>-- In `Class.Subclasses.Physics`:
>local Subclasses = {} :: types.SubclassesFormat
>
>--#1 Physic
>--#2 Constraints
>Subclasses.Constraint = {
>	Properties = {
>		Enabled = {
>			Default = true,
>			SetValue = "Property",
>		},
>	}
>}
>
>--#3 Mechanical
>
>--#4 RopeConstraint
>Subclasses.RopeConstraint = {
>	Properties = {
>		...
>	}
>}
>
>-- In `Class.Subclasses`:
>local Subclasses = {}
>combineTable( Subclasses, require(script.Physic) )
>-- Repeat for other sub-categories..
>```

### Handler

Handlers can be added to subclasses. 
To create a handler, add a module in the `Class.Subclasses` module and name it `"<Subclass Name>Handler"`. 
Handlers define the broader behavior of an [[Objects/ExtendedInstance\|ExtendedInstance]] with that subclass.

For example, to create a handler for the `ConveyorModifier`, add a module named `"ConveyorModifierHandler"`:
```
Class
|- Classes
|- Subclasses
| |- Modifiers
| | |- ConveyorModifierHandler <-- new Handler!
| | |- CustomPhysicalPropertiesHandler
| | |- SpinModifierHandler
| |- Parts
| | |- BasePartHandler
| |- ModelHandler
```

The handler module returns a table containing functions for each event. 
Events include:
#### Added
Runs when an instance with the subclass tag is added to the DataModel, receiving that instance as an argument.
Used primarily for the main behavior of the subclass.
```lua
local SpinModifierHandler = {}

function SpinModifierHandler.Added(instance: Instance)
	local self = ExtendedInstance.fromInstance(instance)
	local partInstance = instance.Parent
	if partInstance == nil or not partInstance:IsA("BasePart") then
		return
	end

	local part = ExtendedInstance.fromInstance( partInstance )
	spinPart(part, self)
end
```

#### Start
Runs once when the game starts.
Used mainly for iterations and initial setup for subclasses.
```lua
local changedParts, changedCFrames = table.create(2^16), table.create(2^16)

function BasePartHandler.Start()
	RunService.Stepped:Connect(function()
		-- BulkMoveTo is faster than setting the CFrame property of individual parts.
		workspace:BulkMoveTo(
			changedParts, 
			changedCFrames, 
			Enum.BulkMoveMode.FireCFrameChanged
		)
	)
	table.clear(changedParts)
	table.clear(changedCFrames)
end
```
## Tutorials

### Define Classes

#### Conveyor Modifier

##### Subclasses
To create a class, start by defining its `Subclasses` in the `Class.Classes` module.
For instance, let's create a `ConveyorModifier` class.

Define `ConveyorModifier` in `Classes.Subclass.Modifiers`:
```lua
local types = require(script.Parent.types)

local Subclasses = {} :: types.SubclassesFormat

--#1 Modifier
-- Defining the base Modifier subclass
Subclasses.Modifier = {
	Properties = {
		Enabled = {
			Default = true,
			GetValue = "ValueByPriority",
		},
		ModifierPriority = {
			Default = 1,
			GetValue = "ValueByPriority",
		}
	}
}

-- Other modifiers...

--#4 ConveyorModifier
Subclasses.ConveyorModifier = {
	Properties = {
		-- Properties will be added here...
	}
}
```

###### PropertyInfos
Next, add essential properties to the `ConveyorModifier`:

- ConveyorSpeed
- ConveyorDirection

```lua
Subclasses.ConveyorModifier = {
	Properties = {
		ConveyorSpeed = {
			-- Properties of property information to be defined...
		},
		ConveyorDirection = {
			-- Properties of property information to be defined...
		},
	}
}
```

>[!Tip]
>Always include a trailing comma in property tables for easier future additions.

Now, let's set the default values:
- `ConveyorSpeed`: `30`
- `ConveyorDirection`: `"Front"`

Additionally, specifying the data type is crucial for efficient data packing and display.
While specifying the data type is optional, if you omit it, the system defaults to `typeof(DefaultValue)` as the data type.

For the `ConveyorSpeed` property, not specifying a `DataType` means it defaults to `number` (equivalent to `float64`, and `double`).
While this default is generally adequate, `ConveyorSpeed` will affects a `BasePart`'s `LinearAssemblyVelocity`, which is a `Vector3`. 
Each component of `Vector3` (x, y, z) is a `float32`.

Since `ConveyorSpeed`, whether as a `float64` or a `float32`, is ultimately applied as a `float32`,
it is more efficient to set the `DataType` to `float32` to utilize a smaller data size.

For `ConveyorDirection`, use the `NormalId` defined in the [[Objects/ExtendedDataTypes#DataTypes\|DataTypes]] module.
```lua
ConveyorSpeed = {
	Default = 30,
	DataType = "float32",
},
ConveyorDirection = {
	Default = Enum.NormalId.Front,
	DataType = "NormalId",
},
```

###### Handler
Create a [[Objects/Class#Handler\|Handler]] to define the behavior of conveyor modifiers.
```
Class
|- Classes
|- Subclasses
| |- Modifiers
| | |- ConveyorModifierHandler
```

```lua
local ConveyorModifierHandler = {}

function ConveyorModifierHandler.Added(instance:Instance)
	local self = ExtendedInstance.fromInstance(instance)
	local partInstance = instance:FindFirstAncestorWhichIsA("BasePart")
	if not partInstance then
		return
	end

	-- Define how it will work..
end

return ConveyorModifierHandler
```

First, each Modifier subclass has a property `Enabled`. 
When `Enabled` is set to true, the modifier is considered active. Conversely, when `Enabled` is false, the modifier should be non-operational, as if it does not exist.

When `Enabled` is true, create the necessary nodes and connections, and assign them to Maid for management. 
When `Enabled` is false, clean up all resources using `Maid:DoCleaning()`.

For more information, refer to [[Objects/Classes/Modifier/Modifier\|Modifier]].

```lua
local self = ExtendedInstance.fromInstance(instance)
local partInstance = instance:FindFirstAncestorWhichIsA("BasePart")
if not partInstance then
	return
end
local part = ExtendedInstance.fromInstance( partInstance )

-- Maid is an effective tool for managing object lifecycles and executing functions.
local maid = Maid.new()

local function enable(enabled)
	if enabled then
		maid:Give(
			-- Tasks for Maid to clean up when Enabled is false..
		)
	else
		-- Clean up when enabled is false
		maid:DoCleaning()
	end
end

enable( self:GetProperty("Enabled") )
self:GetPropertyVbp("Enabled").Changed:Connect(enable)

self.Destroying:Connect(function()
	maid:Destroy()
end)
```


Create [[Packages/ValueByPriority#Node\|ValueByPriority Node]] to set the `AssemblyLinearVelocity` according to the `ConveyorDirection`, influenced by `ConveyorSpeed`.

The priorities of the `ValueByPriorities` created by a `Modifier` should align with the `Priority` of the `Modifier`.
```lua
local function enable(enabled)
	if enabled then
		local function getLinearVelocity()
			-- Return LinearVelocity derived from ConveyorSpeed and Direction.
		end
		
		local linearVelocityNode = part:GetPropertyVbp("AssemblyLinearVelocity"):AddNode({
			Value = getLinearVelocity(),
			Priority = self:GetProperty("Priority"),
		})
		
		-- Maid will clean up when enabled is false: destroy linearVelocityNode and connections.
		maid:Give(
			linearVelocityNode,
			self:GetPropertyVbp("ConveyorSpeed").Changed:Connect(function()
				linearVelocityNode:SetValue( getLinearVelocity() )
			end),
			self:GetPropertyVbp("ConveyorDirection").Changed:Connect(function()
				linearVelocityNode:SetValue( getLinearVelocity() )
			end),
			self:GetPropertyVbp("Priority").Changed:Connect(function(priority)
				linearVelocityNode:SetPriority(priority)
			end)
		)
		
	else
		maid:DoCleaning()
	end
end
```

To finalize, implement a `getLinearVelocity` function that returns a `Vector3` value based on `ConveyorSpeed` and `ConveyorDirection`.
```lua
local function getLinearVelocity()
	local direction = self:GetProperty("ConveyorDirection")
	local speed = self:GetProperty("ConveyorSpeed")
	
	-- Define velocity vectors for each direction.
	if direction == Enum.NormalId.Front then
		return Vector3.new(0, 0, -speed)
	elseif direction == Enum.NormalId.Back then
		return Vector3.new(0, 0, speed)
	elseif direction == Enum.NormalId.Right then
		return Vector3.new(speed, 0, 0)
	elseif direction == Enum.NormalId.Left then
		return Vector3.new(-speed, 0, 0)
	elseif direction == Enum.NormalId.Top then
		return Vector3.new(0, speed, 0)
	elseif direction == Enum.NormalId.Bottom then
		return Vector3.new(0, -speed, 0)
	end
end

local linearVelocityNode = part:GetPropertyVbp("AssemblyLinearVelocity"):AddNode({
	Value = getLinearVelocity(),
	Priority = self:GetProperty("Priority"),
})
```

>[!Note] Why not multiply `Part.CFrame.Rotation` by `LinearVelocity`? 
>When the rotation is multiplied by linear Velocity, it ensures that the linear Velocity aligns with the part's rotation. 
>The `BasePart`'s `AssemblyLinearVelocity` and `AssemblyAngularVelocity` each have a node that multiplies the value with the `CFrame.Rotation` of `BasePart`. 
>Therefore, it's unnecessary to multiply by Rotation.

##### Class

You have defined the `ConveyorModifier` subclass.
Now it's time to define the `ConveyorModifier` class.
Let's add the next class information table to the `Class.Classes` module.

```lua
Classes.ConveyorModifier = {
	RealClass = "Folder",
	Subclasses = {
		[1]=Subclasses.Modifier, -- Base Modifier
		[2]=Subclasses.BasePartModifier, -- Modifiers whose parent should be a BasePart
		[3]=Subclasses.ConveyorModifier, -- Conveyor Modifier
	},
}
```

Add [[Objects/DataObject\|DataObjects]] and [[Objects/ExtendedInstance\|ExtendedInstances]] with Class `ConveyorModifier`.
```lua
local dataObject = DataObject.new("ConveyorModifier")
dataObject:SetProperty("ConveyorDirection", Enum.NormalId.Top)

local extendedInstance = ExtendedInstance.new("ConveyorModifier")
extendedInstance:SetProperty("ConveyorSpeed", 150)
```

#### PVInstances

>[!Warning]
>It covers more in-depth content than [[Objects/Class#Conveyor Modifier\|How Conveyor Modifier is defined]].
>Requires an understanding of [[Packages/ValueByPriority\|ValueByPriority]], the `Scale`, and the `TimeScale` Property of the `Model` Class.

## Get Properties