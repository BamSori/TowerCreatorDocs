---
{"dg-publish":true,"permalink":"/frameworks/file-structure/","updated":"2023-12-14T07:19:42.302+09:00"}
---

## Managing Scripts in Roblox

### Understanding RunContext

![Frameworks-File-Structure-RunContext-In-Properties-Panel.png](/img/user/Frameworks/attachments/Frameworks-File-Structure-RunContext-In-Properties-Panel.png)

Avoid using `LocalScripts` and `Scripts` with `Legacy` as `RunContext`. 
Instead, opt for `Scripts` with `Client` or `Server` as `RunContext`.

> [!Tip] Why is Legacy RunContext Not Recommended? 
> 
> Using `Legacy` as `RunContext` has several drawbacks:
> 
> - `LocalScript` is limited to running only in the player or player's character.
> - `Script` is confined to execute only in the workspace or ServerScript Service.
> 
> These restrictions can lead to a more complex file structure when differentiating client and server modules.
> 
> Bad Readability:
> ```lua
> StarterGui
>|- TimerUI
>| |- TimeTextLabel
>| | |- UpdateTimeTextToServerTime.lua (LocalScript, RunContext: Legacy)
>| |- Header
>
>StarterPlayer
>|- StarterPlayerScripts
>| |- Timer
>| | |- CloneUIToPlayerGui.lua (LocalScript, RunContext: Legacy)
>
>ReplicatedStorage
>|- Timer
>| |- ServerTimeValue
>
>ServerScriptService
>|- Timer
>| |- SetTimeValue.lua (Script, RunContext: Legacy)
> ```
> 
> Good Readability:
> ```lua
> ReplicatedStorage
>|- Timer
>| |- ServerTimeValue
>| | |- SetTimeValue.lua (Script, RunContext: Server)
>| |- UI
>| | |- TimeTextLabel
>| | |- UpdateTimeTextToServerTime.lua (Script, RunContext: Client)
>| | |- Header
>| |- CloneUIToPlayerGui.lua (Script, RunContext: Client)
> ```


**Note**: `Legacy` RunContext is still acceptable for plugins, as Roblox has not fully implemented a dedicated run context for plugins yet.