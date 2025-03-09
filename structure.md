# 第二章：扩展结构

## 1. 扩展文件
无名杀的扩展文件位于游戏本体目录的`extension`文件夹中。
通常以以下形式存在：
```
extension/
    └── 扩展名/
        ├── extension.js    # 扩展主文件
        ├── info.json       # 扩展信息
        ├── LICENSE         # 许可证
        └── ...             # 其他扩展文件
```
## 2. 扩展文件结构

### **extension.js**
- 扩展主文件
- 基础格式如下：
```javascript
import { lib, game, ui, get, ai, _status } from "../../noname.js";  // 从无名杀中导入对象
export const type = "extension";    // 声明此文件为扩展文件
export default function () {
    return {
        // 扩展名称，需要与文件夹名称相同
        name: "扩展名称",
        // 执行顺序：precontent、prepare、content、arenaReady
        // 游戏界面创建之后
        arenaReady () {}, 
        /* 游戏数据加载后、界面加载前
         * config为本扩展选项、pack为本扩展包
         */
        content (config, pack) {}, 
        // 所有扩展加载后
        prepare () {}, 
        // 游戏数据加载前
        precontent () {},
        // 扩展选项
        config: {},
        // 扩展帮助
        help: {},
        // 扩展包
        package: {
            // 武将包
            character: {
                // 武将列表
                character: {},
                // 翻译
                translate: {},
                /* 
                // 武将筛选
                characterFilter: { },
                // 武将描述
                characterIntro: { },
                // 武将换皮换音
                characterSubstitute: { },
                // 武将头衔
                characterTitle: { },
                // 武将切换（如新杀界徐盛、手杀界徐盛可以互相切换）
                characterReplace: { },
                // 武将分包
                characterSort: { },
                // 动态翻译
                dynamicTranslate: { },
                // 是否支持联机
                connect: false,
                */
            },
            // 卡牌包
            card: {
                // 卡牌列表
                card: {},
                translate: {},
                // 牌堆列表
                list: [],
                /*
                卡牌类型
                cardType: {},
                // 是否支持联机
                connect: false,
                */
            },
            // 技能包
            skill: {
                // 技能列表
                skill: {},
                translate: {},
                /*
                // 动态翻译
                dynamicTranslate: { },
                // 是否支持联机
                connect: false,
                */
            },
            // 扩展描述
            intro: "",
            // 作者名称
            author: "无名玩家",
            diskURL: "",
            forumURL: "",
            // 版本号
            version: "1.0",
        },
        files: { "character": [], "card": [], "skill": [], "audio": [] },
        connect: false // 是否支持联机
    }
};
```
### **info.json**
- 用于显示扩展信息
```
{"name":"扩展名","author":"作者名","diskURL":"","forumURL":"","version":"1.0"}
```

## 2. 新建扩展

### 方法一
 - 游戏内依次点击 `选项 -> 扩展 -> 制作扩展 -> 输入扩展名 -> 确定 -> 保存` 即可新建扩展
### 方法二
创建你的开发目录:
```
├── extension.js    # 扩展主文件
├── info.json       # 扩展信息
├── character.js    # 角色文件(可选)
├── card.js         # 卡牌文件(可选)
├── skill.js        # 技能文件(可选)
├── image/          # 图片文件夹(可选)
└── audio/          # 音频文件夹(可选)
```
将上述文件压缩为zip格式，随后游戏内依次点击 `选项 -> 扩展 -> 获取扩展 -> 导入扩展 -> 选择压缩包 -> 确定` 即可导入扩展

## 3. 无名杀代码风格
**对于现版本的无名杀，更推荐使用Async Content格式进行异步操作！**
### 3.1 Async方法(推荐)
无名杀在v1.10.6版本后引入了async/await异步写法,这是推荐的代码风格:

```javascript
async content(event, trigger, player) {
	// 弃置一张手牌
	let result = await player
        .chooseToDiscard(1, 'h', true)
        .forResult();

    // 未弃牌则中断
    if (!result.bool) return;

    // 选择一名其他角色
	let targets = await player
        .chooseTarget('请选择一名角色', true)
        .forResultTargets();

	if(targets && targets.length){
		// 对目标造成1点火焰伤害
		await targets[0].damage('fire');
		// 目标摸一张牌
		await targets[0].draw();
	}
};
```

特点:
- 使用async/await处理异步
- 代码结构清晰直观

### 3.2 Step方法(传统)
早期无名杀使用step标记来处理异步流程:

```javascript
content(){
    "step 0"
    player.chooseToDiscard(1, 'h', true); // 弃置一张手牌

    "step 1"
    if(!result.bool){
		event.finish(); // 未弃牌则结束事件
		return;
    }
    
    "step 2"
    player.chooseTarget('请选择一名角色', true); // 选择一名角色
    
    "step 3"
    if(result.bool){
        result.targets[0].damage('fire'); // 对目标造成1点火焰伤害
        result.targets[0].draw(); // 目标摸一张牌
    }
};
```

特点:
- 使用step标记分步执行
- 通过result传递结果

## 练习题

1. 将以下Step方法改写为Async方法:
```javascript
content(){
    "step 0"
    player.draw(2);
    "step 1"
    if(player.hp < 3){
        player.chooseToDiscard(1, true);
    }
}
```

<details>
<summary>参考答案 | 🟩 Easy</summary>

```javascript
async content(event, trigger, player){
    // 摸两张牌
    await player.draw(2);
    
    // 体力值小于3则弃置一张牌
    if(player.hp < 3){
        await player.chooseToDiscard(1, true);
    }
} // 摸两张牌,若体力值小于3则弃置一张牌
```
</details>
</br>
下一章我们将学习如何制作角色。