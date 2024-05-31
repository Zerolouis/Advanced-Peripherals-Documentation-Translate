# ME Bridge

!!! picture inline end
    ![!Image of the ME Bridge block](/../assets/images/previews/me_bridge.png){ align=right }

ME网关能够与应用能源2进行交互。
你可以提取物品，合成物品，获取物品列表等等功能。一个ME网关将使用一个ME网络频道。

!!! warning "需求"
    你需要先安装 [应用能源2](https://www.curseforge.com/minecraft/mc-mods/applied-energistics-2) 模组

<p class="picture-spacing" style="--ps:0.6rem;"></p>

---

<center>

| 外设名称 | 连接 | 事件 | 引入版本 |
| --------------- | --------------- | ---------- | ------------- |
| meBridge        | ME 网络       | 支持        | 0.3b          |

</center>

---

!!! failure "注意"
    <center> <h3>你应该把需要导入/导出物品的容器放在**ME网关**旁而不是电脑旁！ <h3> </center>

## Events 事件

### crafting 合成
当一个合成任务开始或失败时触发

**返回值:**  
1. `success: boolean` 表示一个合成任务是否成功开始  
2. `message: string` 表示合成任务状态的一段消息  
这些值与 [`craftItem()`](#craftitem)的返回值相同.

```lua linenums="1"
local event, success, message = os.pullEvent("crafting")
if success then
    print("A crafting job has successfully started")
else
    print("A crafting job has failed to start")
end
```

!!! Warning "注意"
    `crafting` 事件将在ME网关连接至一个激活的正在进行合成的ME网络时触发。但是这些操作必须来自ME网关本身。

---

!!! tip "提示"
    你可以使用`/advancedperipherals getHashItem`命令来查看在你手中物品的NBT的MD5值，一个MD5值类似这样`ae70053c97f877de546b0248b9ddf525`

## Functions 方法

!!! info "信息"
    参数(`item: table`)可以接受我们的物品过滤器，你可以[点击此处](/../guides/filters)查看过滤器章节


### craftItem 制作物品
```
craftItem(item: table[, craftingCpu: string]) -> boolean, err: string
```

尝试制作物品 `item`，如果提供了`craftingCpu`则使用该CPU
 
#### Item Properties

查看 [物品过滤器指南](/../guides/filters) 获取更新信息!

| item             | Description                                 |
| ---------------- | ------------------------------------------- |
| name: `string`   | 物品的注册表名称或标签      |
| count: `number?` | 要合成的物品数量            |
| nbt: `string?`   | 使用NBT匹配物品                    |

**或者**  

| item                  | Description                                 |
| --------------------- | ------------------------------------------- |
| fingerprint: `string` | 需要合成的物品的唯一指纹 |
| count: `number?`      | 要合成的物品数量            |

---

### craftFluid 合成流体
```
craftFluid(fluid: table[, craftingCpu: string]) -> boolean, err: string
```

尝试制作流体 `fluid`，如果提供了`craftingCpu`则使用该CPU

#### Fluid Properties

查看 [流体过滤器指南](/../guides/filters) 获取更多信息!

| fluid            | Description                                 |
| ---------------- | ------------------------------------------- |
| name: `string`   | 流体的注册表名称或tag     |
| count: `number?` | 需要合成的流体数量            |
| nbt: `string?`   | 使用NBT匹配流体                  |

**OR**  

| fluid                 | Description                                 |
| --------------------- | ------------------------------------------- |
| fingerprint: `string` | 需要合成的流体的唯一指纹 |
| count: `number?`      | 需要合成的流体数量            |

---

### getItem 获取物品信息
```
getItem(item: table) -> table, err: string
```

返回该网络中物品的信息表

#### Properties 属性

| result                 | Description                                             |
| ---------------------- | ------------------------------------------------------- |
| name: `string`         | item的注册表名称                           |
| fingerprint: `string?` | 物品的唯一指纹 |
| amount: `number`       | 物品在网络中的数量                    |
| displayName: `string`  | 物品显示的名称                          |
| isCraftable: `boolean` | 物品是否可以合成         |
| nbt: `string?`         | 匹配物品的NBT                               |
| tags: `table`          | 物品的所有标签                          |

---

### importItem 从容器导入物品到网络(方向)
```
importItem(item: table, direction: string) -> number, err: string
```

从ME网关的`direction`方向的容器导入`item`到ME网络
返回从容器中导入的`item`数量

!!! tip "自0.7r起"
    从现在起，你可以使用相对定位(`right`, `left`, `front`, `back`, `top`, `bottom`)或罗盘定位(`north`, `south`, `east`, `west`, `up`, `down`)作为`direction`参数
    

```lua linenums="1"
local bridge = peripheral.find("meBridge")

-- 向ME网络导入1个泥土
bridge.importItem({name="minecraft:dirt", count=1}, "up")
```

---

### exportItem 从ME网络导出物品到容器（方向）
```
exportItem(item: table, direction: string) -> number, err: string
```

向ME网关的`direction`方向上的容器导出`item`
返回向容器导入的物品数量

```lua linenums="1"
local bridge = peripheral.find("meBridge")

-- Exports 1 "Protection I" book into the container above
-- 向ME网关上方的容器导出1本“保护一”的附魔书
bridge.exportItem({name="minecraft:enchanted_book", count=1, nbt="ae70053c97f877de546b0248b9ddf525"}, "up")
```

---

### importItemFromPeripheral 从容器导入物品到网络（容器名称）
```
importItemFromPeripheral(item: table, container: string) -> number, err: string
```
与[`importItem()`](#importitem)类似，这个方法从连接在外设网络中的容器导入`item`至ME网络
`container` 应该是一个在网络中的容器的确切名称
返回从容器中导入的`item`数量


---

### exportItemToPeripheral 从ME网络导出物品到容器（容器名称）
```
exportItemToPeripheral(item: table, container: string) -> number, err: string
```

与[`exportItem()`](#exportitem)类似，这个方法从ME网络导出`item`至连接在外设网络中的容器
`container` 应该是一个在网络中的容器的确切名称
返回向容器导入的物品数量

---

### getEnergyStorage 获取能量存储
```
getEnergyStorage() -> number, err: string
```
返回AE中ME网络已存储的能量容量

---

### getMaxEnergyStorage 获取最大能量存储
```
getMaxEnergyStorage() -> number, err: string
```
返回AE中ME网络存储能量的最大容量

---

### getEnergyUsage 获取能量使用量
```
getEnergyUsage() -> number, err: string
```
以AE/t为单位返回整个ME网络的能量使用量

---

### getCraftingCPUs 获取合成CPU信息
```
getCraftingCPUs() -> table, err: string
```
返回所有已经连接的合成CPU信息

#### CPU Properties 属性

| cpu                    | Description                            |
| ---------------------- | -------------------------------------- |
| storage: `number`      | CPU已经存储的数量     |
| coProcessors: `number` | CPU 拥有的协处理器数量 |
| isBusy: `boolean`      | 当前CPU是否正在合成       |

---

### isItemCrafting 物品是否正在合成
```
isItemCrafting(item: table[, craftingCpu: string]) -> boolean, err: string
```

当`item`正在合成时返回`true`，当`craftingCpu`参数被提供时，该方法仅检查该CPU是否正在合成`item`

---

### isItemCraftable 物品是否可合成
```
isItemCraftable(item: table) -> boolean, err: string
```

当`item`可被合成时返回`true`

---

### listCraftableItems 列出所有可合成物品
```
listCraftableItems() -> table, err: string
```

返回所有可合成物品信息

#### Properties

| item / fluid           | Description                                             |
| ---------------------- | ------------------------------------------------------- |
| name: `string`         | 物品的注册表名称                           |
| fingerprint: `string?` | 定义需要合成物品的唯一指纹 |
| amount: `number`       | ME网络中物品的数量                  |
| displayName: `string`  | 物品的显示名称                          |
| isCraftable: `boolean` | 物品是否有合成样板（是否可以合成）         |
| nbt: `string?`         | 物品匹配的NBT                                |
| tags: `table`          | 物品的所有tags                          |

```lua linenums="1"
local bridge = peripheral.find("meBridge")

-- print out all craftable items
craftableItems = bridge.listCraftableItems()
for _, item in pairs(craftableItems) do
    print(item.name)
end
```

---

### listCraftableFluid 列出可合成的流体
```
listCraftableFluid() -> table, err: string
```
返回所有可合成流体信息

---

### listItems 列出所有物品信息
```
listItems() -> table, err: string
```

返回ME网络中所有物品信息

---

### listFluid 列出所有流体信息
```
listFluid() -> table, err: string
```

返回ME网络中所有流体信息

---

### listGas 列出所有气体信息
```
listGas() -> table, err: string
```
返回ME网络所有气体信息(需要应用能源：通用机械附属)

---

!!! success "添加于 version 1.18.2-0.7.24r | 1.19.2-0.7.23b"

### listCells 列出存储元件
```
listCells() -> table, err: string
```
返回ME网络中磁盘驱动器所有存储元件信息


| cell                   | Description                            |
| ---------------------- | -------------------------------------- |
| item: `string`         | 存储元件的名称 例如： `ae2:64k_storage_cell`(64K存储单元) |
| cellType: `string`     | 存储元件的类型 `item`(物品) or `fluid`(流体)|
| bytesPerType: `int`    | 每种类型大小                     |
| totalBytes: `int`      | 总共可用存储大小       |

---

!!! success "添加于 version 1.18.2-0.7.24r | 1.19.2-0.7.23b"

### getTotalItemStorage 获取物品总存储量
```
getTotalItemStorage() -> int, err: string
```
返回ME网络提供的物品总存储量
Returns how much total item storage the system offers

---

!!! success "Added in version 1.18.2-0.7.24r | 1.19.2-0.7.23b"

### getTotalFluidStorage 获取流体总存储量
```
getTotalFluidStorage() -> int, err: string>
```

返回ME网络提供的流体总存储量

---

!!! success "添加于 version 1.18.2-0.7.24r | 1.19.2-0.7.23b"

### getUsedItemStorage 获取物品已用存储量
```
getUsedItemStorage() -> int, err: string
```
返回ME网络中物品已用存储量

---

!!! success "Added in version 1.18.2-0.7.24r | 1.19.2-0.7.23b"

### getUsedFluidStorage 获取流体已用存储量
```
getUsedFluidStorage() -> int, err: string
```

返回ME网络中流体已用存储量

---

!!! success "用于 version 1.18.2-0.7.24r | 1.19.2-0.7.23b"

### getAvailableItemStorage 获取物品可用存储量
```
getAvailableItemStorage() -> int, err: string
```

返回ME网络中物品可用存储量

---

!!! success "Added in version 1.18.2-0.7.24r | 1.19.2-0.7.23b"

### getAvailableFluidStorage 获取流体可用存储量
```
getAvailableFluidStorage() -> int, err: string
```

返回ME网络中流体可用存储量

---

## 例子

### 全自动合成

这个脚本将自动合成在列表中的物品

你想要同时生产500个玻璃？把玻璃加入合成列表脚本会帮你自动合成

不需要ME标准发信器或者合成卡！

[点击此处](https://github.com/SirEndii/Lua-Projects/tree/master)查看脚本的安装教程

![自动合成脚本预览](/../assets/images/me_bridge/autocraft_example.png)

### ME Crafting CPUs

这个脚本向你展示了ME合成处理器的一些数据
This script shows you some statistics about the ME crafting cpus.

[点击此处](https://github.com/SirEndii/Lua-Projects/tree/master)查看脚本的安装教程

![ME合成数据展示预览](/../assets/images/me_bridge/mecpus_example.png)

---

## Changelog/Trivia

**0.7r**  
The ME Bridge does uses computercraft relative and cardinal directions.
We also changed some function names.

**0.4b**  
Reworked the system of the ME Bridge, it now has more features and a new system for the `item` parameter.

**0.3.9b**  
Added the `importItem` and `exportItem` from container functions.

**0.3b**  
Added the ME Bridge with a good amount of features.
