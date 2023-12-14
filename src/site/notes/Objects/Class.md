---
{"dg-publish":true,"permalink":"/Objects/Class/","noteIcon":"","updated":"2023-12-15T06:35:27.587+09:00"}
---

Classes in `ObjectModules.Class` are designed for managing both [[Objects/DataObject\|DataObjects]] and [[Objects/ExtendedInstance\|ExtendedInstances]].

## Class Structure

A `Class` comprises an array of `Subclasses` within the `Class.Classes` module. These subclasses collectively define the functionalities and properties of a class.
## Subclass Definition

Subclasses, which form the integral parts of a class, should be defined in the `Class.Subclasses` module.

>[!Tip] 
>For better organization, especially when dealing with numerous subclasses, group them into sub-categories within child modules. Then, integrate these into the main subclasses module. Here's how you can do it:
>```lua
>-- Example in `Class.Subclasses.Physics`:
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


## Tutorials

### Creating a Class

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
- `ConveyorSpeed`: `30`.
- `ConveyorDirection`: `"Front"`.

Additionally, specifying the data type is crucial for efficient data packing and display.
While specifying the data type is optional, if you omit it, the system defaults to `typeof(DefaultValue)` as the data type.

For the `ConveyorSpeed` property, not specifying a `DataType` means it defaults to `number` (specifically float64, or double).
This default setting is generally adequate, but `ConveyorSpeed` is applied to a `BasePart`'s `LinearAssemblyVelocity`, which uses `Vector3`, each component (x, y, z) of `Vector3` is actually represented as a `float32`.

Therefore, even though `ConveyorSpeed` is set as a `float64`, the actual precision in this context is equivalent to that of a `float32`.
By explicitly setting `ConveyorSpeed` to `float32` instead of `float64`, you can reduce the amount of data required.

```lua
ConveyorSpeed = {
	Default = 30,
	DataType = "float32",
},
ConveyorDirection = {
	Default = "Front",
	DataType = "NormalId",
},
```

#### BasePart

WIP