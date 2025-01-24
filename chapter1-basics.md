# 第一章：基础知识

## 1. 开发环境准备

### 1.1 必要工具
- 文本编辑器(推荐使用VS Code)
- 无名杀游戏本体

### 1.2 开发环境设置
1. 下载并安装VS Code
2. 安装必要插件:
   - Prettier（用于JavaScript格式化）

### 1.3 创建开发目录
在无名杀游戏目录下找到 `extension` 文件夹,创建你的扩展目录:
```
extension/
  └── 扩展名/
      ├── extension.js     # 扩展主文件
      ├── character.js     # 武将代码(可选)
      ├── card.js         # 卡牌代码(可选)
      ├── skill.js        # 技能代码(可选)
      ├── image/          # 图片文件夹
      │   ├── card/       # 卡牌图片
      │   └── character/  # 武将图片
      └── audio/          # 音频文件夹
          ├── die/        # 阵亡配音
          └── skill/      # 技能配音
```

## 2. JavaScript基础

### 2.1 变量与数据类型
JavaScript中的基本数据类型：
```javascript
// 数字
let hp = 4;           // 生命值
let maxHp = 4;        // 体力上限

// 字符串
let name = "张飞";     // 武将名称
let skill = "咆哮";    // 技能名称

// 布尔值
let forced = true;    // 是否为锁定技
let available = false; // 技能是否可用

// 数组
let skills = ["咆哮", "替身"]; // 技能列表

// 对象
let character = {
    name: "张飞",
    hp: 4,
    skills: ["咆哮", "替身"]
};

// 示例

player.storage[paoxiao] = true; // 初始化状态

let targets = game.filterPlayer(current => 
    current.hp <= 2
); // 获取所有体力值不大于2的角色

game.countPlayer(function(current){
    return current.group == 'wei';
}); // 统计魏势力角色数量

{
let cards = []; // 初始化列表
game.checkGlobalHistory("cardMove", evt => {
    cards.addArray(evt.cards.filter(card => get.position(card, true) == "d"));
}); // 获取本回合进入弃牌堆的牌
};
```

### 2.2 函数
函数是JavaScript中最重要的概念之一：
```javascript
// 基本函数定义
function checkHp(player) {
    return player.hp <= 2;
}

// 箭头函数
const isDamaged = player => player.hp < player.maxHp;

// 异步函数
async function drawCards(player) {
    await player.draw(2);
    await player.chooseToDiscard(1);
}

// 示例

function checkDamage(player, target) {
    return get.damageEffect(target, player, player) > 0;
} // 检查对目标造成伤害是否有收益

const canUseCard = (card, player) => {
    return player.hasUseTarget(card);
} // 检查玩家是否能使用该牌

async function useCardEffect(card, target) {
    await target.damage('fire');
    await target.draw();
} // 对目标造成火焰伤害并令其摸一张牌
```

### 2.3 条件语句
```javascript
// if条件
if (player.hp <= 2) {
    player.draw(1);
} else if (player.hp <= 1) {
    player.draw(2);
} else {
    player.draw(3);
}

// switch语句
switch(event.name) {
    case 'damage':
        player.draw(1);
        break;
    case 'lose':
        player.recover();
        break;
    default:
        // 默认情况
}
```

### 2.4 条件判断
```javascript
// 多重条件判断示例
filter: function(event, player){
    // 体力值小于3且有手牌才能发动
    return player.hp < 3 && player.countCards('h') > 0;
},

// 目标判断示例
filterTarget: function(card, player, target){
    // 不能选择自己且目标角色已受伤
    return target != player && target.isDamaged();
}
```

## 3. 无名杀代码风格

### 3.1 Async方法(推荐)
无名杀在v1.10.6版本后引入了async/await异步写法,这是推荐的代码风格:

```javascript
content: async function (event, trigger, player) {
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
content: function(){
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

## 4. 扩展文件结构

### 4.1 基本结构
一个标准的扩展文件(extension.js)结构如下:

```javascript
game.import("extension", function(){
    return {
        name: "扩展名",
        content: function(){},
        precontent: function(){},
        config: {},
        package: {
            character: { // 角色系统
                character: {
                },
                translate: {
                }
            },
            skill: { // 技能系统
                skill: {
                },
                translate: {
                }
            },
            card: { // 卡牌系统
                card: {
                },
                translate: {
                },
                list: [],
            },
            intro: "扩展介绍",
            author: "作者名",
            diskURL: "更新地址",
            forumURL: "讨论区地址",
            version: "1.0",
        },
        files: {
            "character": [],
            "skill": [], 
            "card": [],
            "audio": [],
        },
    };
});
```

### 4.2 主要配置说明

1. **content函数**
- 扩展内容加载时执行
- 可以根据config判断是否启用某功能
- 用于初始化扩展内容

2. **precontent函数**
- 在content之前执行
- 用于预加载资源
- 设置必要的初始化内容

3. **config配置**
- 在扩展界面提供的选项
- 可以控制扩展功能的开关
- 作为content函数的参数

4. **package配置**
- character: 武将定义
- card: 卡牌定义
- skill: 技能定义
- translate: 翻译定义
- 其他扩展信息

5. **files配置**
- 指定需要加载的额外文件


## 练习题

1. 将以下Step方法改写为Async方法:
```javascript
content: function(){
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
content: async function(){
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