---
{"dg-publish":true,"permalink":"/frameworks/file-structure/","updated":"2023-12-14T05:22:37.917+09:00"}
---


## Scripts

### RunContext

![Frameworks-File-Structure-RunContext-In-Properties-Panel.png](/img/user/Frameworks/attachments/Frameworks-File-Structure-RunContext-In-Properties-Panel.png)

Do not use `LocalScripts` and `Scripts` with `Legacy` as `RunContext`.
use `Scripts` and `Client`/`Server` as `RunContext`.

>[!Tip] Why should I avoid Legacy?
>If `RunContext` is set to `Legacy`, 
>`LocalScript` only runs in the player or player's character, 
>and `Script` only runs in workspace or ServerScript Service.
>The client side and the server side can be divided into modules, but otherwise the file structure becomes complicated.
>
>Good For Readability:
>```
>ReplicatedStorage
>|- Timer
>| |- ServerTimeValue
>| | |- SetTimeValue.lua (Script, RunContext: Server)
>| |- UI
>| | |- TimeTextLabel
>| | |- UpdateTimeTextToServerTime.lua (Script, RunContext: Client)
>| | |- Header
>| |- CloneUIToPlayerGui.lua (Script, RunContext: Client)
>```
>
>Bad For Readability:
>```
>StarterGui
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
>```

Legacy is acceptable for plugins.  
Roblox hasn't fully released the plug-in run context yet.