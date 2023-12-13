---
{"dg-publish":true,"permalink":"/objects/class/"}
---


Present in `ObjectModules.Class`.

`Class` is used for classes of [[Objects/DataObject\|DataObjects]] and [[Objects/ExtendedInstance\|ExtendedInstances]].

Class consist of an array of [[Objects/Class#Subclass\|Subclasses]] in the `Class.Classes` module.
## Subclass

Another subclass that makes up the class
Define a subclass in `Class.Subclasses` module.

>[!Tip] 
>If there are too many subclasses, divide them into sub-categories, define them in the child module, and merge them in the subclasses module.
>```lua
>-- in `Class.Subclasses.Phsyics`:
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
>-- other sub-categories...
>```


## Tutorials

### Make Class

Classes consist of an array of [[Objects/Class#Subclass\|Subclasses]] in the `Class.Classes` module.
First, Make [[Objects/Class#Subclass\|#Subclass]] to consist our Class.


