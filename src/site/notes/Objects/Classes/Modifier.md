---
{"dg-publish":true,"permalink":"/objects/classes/modifier/","updated":"2023-12-14T08:38:11.049+09:00"}
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
  
1. Part A spin due to SpinModifier 1  
2. With part A spins, it moves due to the moving modifier2  
3. Part A spins due to SpinModifier3.

With this, you can also create a one-way slippery truss part that spins and moves.