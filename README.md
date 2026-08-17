# 手机版模组包(触屏适配)

适用环境:手机版星露谷 1.6.15 + SMAPI 4.3.2.5(Build 1775226918,StardewSMAPIThailand / NRTnarathip)。
把本文件夹里的每个模组文件夹直接放进手机版 `Mods\` 目录即可。

> 📦 **`压缩包\` 子文件夹**:每个模组一个 `模组名-版本.zip`,内容与本目录的模组文件夹完全一致
> (只含运行所需文件:manifest / dll / README / i18n / assets,无源码、无调试文件)。传到手机后解压即可,或直接在
> 手机文件管理器里解压 zip 到 Mods 目录。

## 触屏适配内容(已同步 2026-08-17 最新 PC 构建)

> 本次已把更新过的模组全部对齐到当前 PC 最新版:GarbageRecycler **1.4.4**、TreeFarm **0.3.3**;同时刷新 PhoneGift / ShedConsoleComputer 同版本最新 DLL,并补齐 GeniusWatch 图标与 TreeFarm 树贴图。`压缩包\` 里的 14 个 zip 已全部重新打包并校验。

手机版点按地图对象 = "触摸=左键",鼠标右键(以及 Shift 批量、F1 等键盘键)都不存在。
修复思路:所有"右键"入口改挂游戏自己的交互链路
`Game1.pressActionButton → GameLocation.checkAction → Object.checkForAction`
(PC 右键 / 手机点按 / 手柄 X 都走这一条),左键敲打拆机器的行为不受影响。

| 模组 | 版本 | 修了什么 |
|------|------|----------|
| PhoneGift 顺丰速递 | 1.1.0 | 点电话没反应 → 挂 Object.checkForAction 前缀开送礼界面(原先只认右键) |
| ShedConsoleComputer 小屋主控电脑 | 1.5.0 | 点电脑没反应 → 同款 checkForAction 前缀开电脑菜单 |
| GarbageRecycler 垃圾回收 | 1.4.4 | ① 手持净水滤芯"撒水面"改挂 GameLocation.checkAction(原先只认右键,手机版整个玩法失效);② 菜单"长按 = 右键":半取/燃料放 1 个/仓库取 10 件照旧可用;③ 仓库标签行新增「全部取出」按钮(替代手机上没有的 Shift+左键);已同步 1.4.4:每批最多 10 件可混合、预计完成时间、监测站返回、滤网返还 |
| TreeFarm 林场 | 0.3.3 | 管理页砍伐线按钮"长按 = ±5"快调(替代 Shift);已同步 0.3.3:站树被动收益、种子掉率随树龄、自动补种覆盖买苗/手动砍伐/扩容、管理页每页 10 行、升级材料完整显示 |
| AnimalBarn 动物养殖场 | 1.1.3 | 买动物/取产品"长按 = 批量取/买 5"(替代 Shift) |

其余模组(RobinOvertime / JinKeLa / MonsterArena / GeniusWatch / Maotai / RingPack /
StackableTackle / BidirectionalHopper / AutoEatLowHealth)交互本身不依赖右键,原样可用。

## 手机操作速查

- **点按电话/电脑/回收机/监测站** = 打开对应界面(和 PC 右键一样)。
- **菜单里长按(按住约 0.5 秒再松手)** = 右键:
  - 垃圾回收:槽位取 1 个/放 1 个(半取)、燃料槽放 1 个、仓库取 10 件;
  - 林场:砍伐线 ±5;
  - 养殖场:买动物/取产品 ×5。
- **「全部取出」按钮**(垃圾回收仓库页、养殖场仓库页底部)= 整类带走。
- 背包太满放不下时会自动掉在脚下,不会丢东西。

## 已知说明

- F1 查看 LookupAnything 档案是 PC 键盘键,手机端不用(LA 手机版也不一定有)。
- 模组内置逻辑测试全部回归通过:PhoneGift 403 / GarbageRecycler 2095 /
  ShedConsoleComputer 327 / TreeFarm 138 / AnimalBarn 500。
