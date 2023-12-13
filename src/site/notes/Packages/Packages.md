---
{"dg-publish":true,"permalink":"/packages/packages/","updated":"2023-12-14T04:59:25.773+09:00"}
---

The `Packages` folder is a collection of independent modules.  
Each package must have no dependency when called from an external script.


## Use packages created by other manufacturers
A good example of a package is Signal modules.  
Let's use [FastSignal](https://devforum.roblox.com/t/fastsignal-1021-a-consistent-signal-library/1360042) for this section.
### Install Package
First, download the package, `FastSignal` from following lists:
- From [Wally](https://wally.run/package/lucasmzreal/fastsignal?version=10.2.1)
  Wally is a site with various packages.  
  Install packages you want and build it with a [ROJO](https://rojo.space/) in the Visual Studio Code.
- From [Github Repository](https://github.com/RBLXUtils/FastSignal)
  To download from the repository, you must build with ROJO, just like Wally.
- From [Github Repository Releases](https://github.com/RBLXUtils/FastSignal/releases)
  Download models that have already been built as rbxm from the Releases.
- From [Roblox Marketplace](https://create.roblox.com/marketplace/asset/6532460357/FastSignal-A-consistent-Signal-library%3Fkeyword=&pageNumber=&pagePosition=)
  It's the easiest to install. 
  However Many packages may only be on Wally or GitHub.

### Put Package

It doesn't matter where you put the package, but `ReplicatedStorage.Packages` is the most common package folder.

```
game
|- ReplicatedStorage
| |- Packages
| | |- FastSignal
```

The name of `FastSignal` is ambiguous. Change the name to `Signal`.  
```
game
|- ReplicatedStorage
| |- Packages
| | |- Signal
```
In most cases, the name of the object returned by the module must be identical to the name of the module, but in this case, you should change it to Signal for more general use.

### Use Package

![Packages-Use-Package-Explorer.png|300](/img/user/Packages/attachments/Packages-Use-Package-Explorer.png)![Packages-Use-Package-Script-Properties-RunContext-To-Server.png|300](/img/user/Packages/attachments/Packages-Use-Package-Script-Properties-RunContext-To-Server.png)

Let's now use the package that you installed in the external script.  
  
Let's first create the script in ReplicatedStorage and [[RunContext\|RunContext]] as the server.  
>[!Tip] RunContext
>If `RunContext` is `Legacy`, it will not run except in `workspace` and `ServerScriptService`.  
>If `RunContext` is `Server`, the script runs anywhere.


