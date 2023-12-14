---
{"dg-publish":true,"permalink":"/Objects/Classes/Modifier/","noteIcon":"","updated":"2023-12-15T05:16:58.228+09:00"}
---

Inspired from [Modifire](https://docs.blender.org/manual/en/latest/modeling/modifiers/introduction.html) in Blender.

Modifire defines the behavior of the parent Extended Instance.  

Modifires have priority.
Modifires are applied in ascending order.
  
Let's say we have an instance tree like this.  
```
Tower
|- PartA
| |- SpinModifier1 (ModifierPriority: 1)
| |- MovingModifier2 (ModifierPriority: 2)
| |- SpinModifier3 (ModifierPriority: 3)
| |- Attachment
```
  
1. PartA spin due to SpinModifier 1  
2. PartA moves due to MovingModifier2  
3. PartA spins due to SpinModifier3.

With this, you can also create a one-way slippery truss part that spins and moves.