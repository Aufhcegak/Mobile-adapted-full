# Genius Watch（小天才电话手表）

给村民送一只「小天才电话手表」：送出去后，打开地图（M 键）就能实时看到这位村民的位置标记；
再送一只给同一位村民，标记取消（这次礼物相当于"收回"）。两种分支都会正常消耗手表。

## 玩法

- **制作**：采集 6 级后次日学会配方（可在 GMCM 改等级）。材料：铱锭 2 + 精炼石英 5 + 铁锭 3。
- **送礼**：物品栏选中手表，直接拖给任意可送礼的村民，走原版送礼流程
  （喜好/品质/生日 8 倍/每周 2 次/每天 1 次等原版规则全部适用）。
- **地图标记**：M 键开地图，已送手表的村民位置会显示该村民的**头像标记**（固定取精灵表第 0 帧[朝下]的头部 16×16 × 4 倍 = 64×64，与原版玩家迷你头像 `drawMiniPortrat` 的固定取帧做法一致），右下角再压一只**手表角标**（16×16 × 2 倍 = 32×32），一眼认出是谁、且知道这是小天才定位标记。
  - 村民在室内（家里/店里）时，标记画在对应建筑门口附近（原版 `WorldMapManager.GetPositionData`
    自带 ParentBuilding 兜底，无需自己实现）。
  - 不在当前地图区域（如村民在沙漠而你在农场地图）时，和原版玩家标记一样不显示。
- **再送一次**：同一村民再次收下手表 → 取消该村民的标记。

## 存档

- 名单存在玩家 modData：`xiepe.GeniusWatch/TrackedNpcs`（JSON 数组，System.Text.Json + IncludeFields，
  损坏给空名单兜底）。联机各玩家记自己的名单，地图只画本地玩家的名单。

## 技术要点（原版源码优先，全部照搬 sdv-src）

- **送礼挂点**：`NPC.receiveGift`（NPC.cs:4898）后置补丁。原版流程
  `tryToReceiveActiveObject`（NPC.cs:1712）→ 通用礼物分支（NPC.cs:2355，条件
  `CanReceiveGifts() && canBeGivenAsGift() && !not_giftable`）→ `receiveGift` →
  `reduceActiveItemByOne()`（NPC.cs:2402-2403）。`receiveGift` 只在礼物真正被收下时调用，
  物品消耗由原版统一完成，两个分支（加/取消标记）都不干扰消耗。
- **地图标记**：`MapPage.draw` 后置补丁，换算完全照抄 `drawMiniPortraits`（MapPage.cs:261）：
  `WorldMapManager.GetPositionData(location, tile)` → `GetMapPixelPosition()` → 加 `mapBounds` 偏移 -32，
  区域不符跳过（MapPage.cs:268），多人同位置错开（MapPage.cs:272-277）。
- **物品**：`Data/Objects` 注册（`ObjectData.Texture` 自定义贴图，ObjectDataDefinition.cs:52：
  `rawData.Texture ?? "Maps\\springobjects"`），分类 Crafting（-8），`CanBeGivenAsGift=true`。
- **配方**：`Data/CraftingRecipes`，格式与 PhoneGift 一致：`原料 / Home / (O)ID 1 / false / none / 中文名`，
  解锁在 DayStarted 按采集等级发放（照抄 PhoneGift）。
- **贴图**：16×16 透明背景 PNG，PIL 逐像素渲染（`assets/_gen_icon.py` 可重新生成），
  物品格贴图与地图角标共用。设计：蓝色打孔表带 + 粉色圆角方形表圈（右上角摄像头圆点、
  右侧橙色按键，小天才经典元素）+ 深蓝屏幕（顶部反光、青色刻度、白色时针/分针、青色秒针）。

## 游戏内验证步骤

1. 重启游戏（SMAPI 启动日志出现 `Genius Watch (小天才电话手表) 1.0.0`）。
2. 采集练到 6 级，睡一觉 → HUD 提示「学会了新配方：小天才电话手表」。
3. 制作界面（X 键）→ 制作 1-2 只手表面板在背包。
4. 找到任意村民（如阿比盖尔），背包选中手表拖给她 → 送礼动画 + 收礼对话，
  同时 HUD 提示「小天才电话手表送出去了！地图上会显示阿比盖尔的位置」。
5. 按 M 开地图 → 阿比盖尔当前位置出现她的头像标记，右下角带手表角标（她在屋里则标记在建筑门口附近）。
6. 再送一只给阿比盖尔 → HUD 提示「收回了阿比盖尔的定位标记」→ 开地图确认图标消失。
7. 存档读档后开地图，标记仍在（modData 持久化）。
8. GMCM 配置页可改「配方所需采集等级」和「地图显示位置标记」开关。

## 联机

- 各玩家自己的名单存在自己的 modData，地图只画本地玩家名单。
- 送礼挂点在送礼者客户端执行（原版送礼就是送礼者本地权威），与 NPC 互动对象无关。

## 遗留风险

- 村民在无世界地图数据的自定义地图（Mod 地图）时，标记不显示（与原版玩家标记行为一致）。
- 游戏运行中 dll 被锁，`dotnet build` 的 AfterBuild 复制会报 MSB3021 —— 退出游戏后重跑。
- PhoneGift 顺丰速递**不**调用原版 NPC.receiveGift（它自己复刻了送礼结算逻辑），
  本 mod 已额外挂 PhoneGift.GiftLogic.SendGift 后置补丁：经快递寄出的手表同样会切换地图标记。
- 若给小孩（Child）送手表也会打标记（原版小孩收礼路径），地图上会画小孩头像。
