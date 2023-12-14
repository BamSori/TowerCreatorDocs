---
{"dg-publish":true,"permalink":"/Packages/Packages/","noteIcon":"","updated":"2023-12-14T08:43:41.669+09:00"}
---

The `Packages` folder contains a collection of independent modules.  
Each package should be independent, with no dependencies when called from an external script.

## Using Packages from Other Manufacturers

A notable example of a package is the Signal module.  
We will use [FastSignal](https://devforum.roblox.com/t/fastsignal-1021-a-consistent-signal-library/1360042) for illustration in this section.

### Installing the Package

First, download the `FastSignal` package from the following sources:

- From [Wally](https://wally.run/package/lucasmzreal/fastsignal?version=10.2.1): Wally hosts a variety of packages.  
    Select and install the desired packages, then build them using [ROJO](https://rojo.space/) in Visual Studio Code.
- From the [GitHub Repository](https://github.com/RBLXUtils/FastSignal): To download from GitHub, build with ROJO, similar to the process with Wally.
- From [GitHub Repository Releases](https://github.com/RBLXUtils/FastSignal/releases): Download pre-built models in rbxm format from the Releases section.
- From the [Roblox Marketplace](https://create.roblox.com/marketplace/asset/6532460357/FastSignal-A-consistent-Signal-library%3Fkeyword=&pageNumber=&pagePosition=): This is the simplest installation method. However, many packages may only be available on Wally or GitHub.

### Placing the Package

You can place the package anywhere, but `ReplicatedStorage.Packages` is the most common location.
```
game
|- ReplicatedStorage
| |- Packages
| | |- FastSignal
```

Rename `FastSignal` to `Signal` for clarity.
```
game
|- ReplicatedStorage
| |- Packages
| | |- Signal
```

Typically, the name of the object returned by the module should match the module's name. However, in this case, rename it to `Signal` for broader applicability.

### Using the Package

![Packages-Use-Package-Explorer.png|300](/img/user/Packages/attachments/Packages-Use-Package-Explorer.png)![Packages-Use-Package-Script-Properties-RunContext-To-Server.png|300](/img/user/Packages/attachments/Packages-Use-Package-Script-Properties-RunContext-To-Server.png)

Now, let's use the installed package in an external script.

First, create a script in `ReplicatedStorage` and set the [[Frameworks/File Structure#RunContext\|RunContext]] to server.

> [!Tip] RunContext 
> If `RunContext` is `Legacy`, the script will only run in `workspace` and `ServerScriptService`.  
> If `RunContext` is `Server`, the script can run anywhere.

```lua
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local RunService = game:GetService("RunService")

local Packages = ReplicatedStorage.Packages
local Signal = require(Packages.Signal)

local signal = Signal.new()
signal:Connect(function(value)
	print(`Value: {value}`)
end)

RunService.Heartbeat:Connect(function()
	local t = os.clock()
	signal:Fire(t)
end)
```

It's complete!

# Package List

- [[Packages/ValueByPriority\|ValueByPriority]]
- Maid
- [[Objects/ExtendedDataTypes#DataTypes\|DataTypes]]
- etc..