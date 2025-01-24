# 第四章：卡牌开发

## 1. 卡牌系统概述

无名杀的卡牌系统包括：
- 基本牌
- 锦囊牌
- 装备牌
- 延时锦囊牌

## 2. 卡牌定义

### 2.1 基本结构
```javascript
card: {
    "my_card": {
        type: "basic",       // 牌的类型(basic/trick/delay/equip)
        enable: true,        // 是否可以使用
        filterTarget: true,  // 目标选择条件
        content: function(){ // 卡牌效果
            target.damage();
        }
    },
    translate: {
        "my_card": "我的卡牌",
        "my_card_info": "卡牌描述"
    }
}
```

### 2.2 卡牌类型
```javascript
// 基本牌
"basic_card": {
    type: "basic",
    enable: true,
    usable: 1,                // 使用次数限制
    selectTarget: 1,          // 目标数量
},

// 锦囊牌
"trick_card": {
    type: "trick",
    enable: true,
    toself: false,            // 是否可以对自己使用
    selectTarget: -1,         // -1表示可以选择任意数量目标
},

// 装备牌
"equip_card": {
    type: "equip",
    subtype: "equip1",        // 装备类型(equip1武器/equip2防具/equip3防御马/equip4进攻马/equip5宝物)
    skills: ["my_skill"],     // 装备技能
    distance: {               // 距离修正
        attackFrom: -1,       // 攻击距离
        globalFrom: -1,       // 防御距离
    }
},

// 延时锦囊
"delay_card": {
    type: "delay",
    enable: true,
    filterTarget: function(card, player, target){
        return !target.hasJudge('my_delay');  // 判断目标是否已有同名判定牌
    },
    judge: function(card){    // 判定函数
        if(get.color(card) == 'red') return 1;
        return -1;
    }
}
```

## 3. 卡牌效果

### 3.1 基础效果
```javascript
content: async function(){
    // 造成伤害
    await target.damage();
    
    // 回复体力
    await target.recover();
    
    // 摸牌
    await target.draw(2);
    
    // 弃牌
    await target.chooseToDiscard(1, true);
}
```

### 3.2 复杂效果
```javascript
"complex_card": {
    content: async function(){
        // 选择效果
        let choice = await player.chooseControl('选项1', '选项2')
            .set('prompt', '请选择一个效果')
            .forResult();
            
        // 条件判断
        if(choice.control === '选项1'){
            await target.damage('fire');
        } else {
            await target.draw(2);
        }
        
        // 多目标效果
        for(let current of targets){
            await current.damage();
            await game.delay(0.5);
        }
    }
}
```

## 4. 卡牌动画

### 4.1 使用动画
```javascript
"animation_card": {
    content: async function(){
        // 使用动画
        player.$throw(card);
        await game.delay(0.5);
        
        // 目标动画
        target.$damage('fire');
        await game.delay(0.3);
        
        // 获得动画
        target.$gain(cards);
    }
}
```

### 4.2 特殊动画
```javascript
"special_animation": {
    content: async function(){
        // 判定动画
        let result = await player.judge();
        
        // 展示动画
        await player.$showCards(cards);
        
        // 比较动画
        await player.$compare(card1, target, card2);
    }
}
```

## 5. 卡牌音效

### 5.1 基础音效
```javascript
"audio_card": {
    audio: true,              // 使用默认音效
    // 或
    audio: "ext:扩展名:2",    // 使用扩展音效
}
```

### 5.2 条件音效
```javascript
"condition_audio": {
    audio: function(player){
        // 根据条件返回不同音效
        if(player.hp < 3) return "ext:扩展名:2";
        return true;
    }
}
```

## 6. 进阶功能

### 6.1 联动效果
```javascript
"link_card": {
    init: function(player){
        // 初始化
        player.storage.link_count = 0;
    },
    onuse: function(result, player){
        // 使用时触发
        player.storage.link_count++;
    },
    content: async function(){
        // 根据使用次数改变效果
        let count = player.storage.link_count;
        await target.damage(count);
    }
}
```

### 6.2 特殊规则
```javascript
"special_rule": {
    mod: {
        targetEnabled: function(card, player, target){
            // 目标限制
            if(target.hp > player.hp) return false;
        },
        cardUsable: function(card, player, num){
            // 使用次数修改
            if(player.hp < 3) return num + 1;
        },
        ignoredHandcard: function(card, player){
            // 手牌规则修改
            if(card.name == 'my_card') return true;
        }
    }
}
```

## 7. 注意事项

1. **卡牌设计**
   - 效果要平衡
   - 规则要明确
   - 避免过于复杂

## 练习

1. 创建一个基本牌：
   - 设计基础效果
   - 添加使用条件
   - 实现动画效果

<details>
<summary>参考答案 | 🟩 Easy</summary>

```javascript
// 在扩展中添加卡牌
card: {
    "my_basic": {
        type: "basic",                // 基本牌
        enable: true,                 // 可以使用
        usable: 1,                    // 每回合限一次
        filterTarget: function(card, player, target){
            return target != player;   // 不能对自己使用
        },
        selectTarget: 1,              // 选择一个目标
        content: async function(){
            // 播放使用动画
            player.$throw(cards);
            game.delay(0.5);
            
            // 造成伤害
            await target.damage('fire');
        },
        ai: {
            order: 4,                 // 使用优先级
            value: 5,                 // 基础价值
            useful: 4,                // 使用价值
            result: {
                target: -1.5          // 对目标效果
            }
        }
    }
},
translate: {
    "my_basic": "火燎",
    "my_basic_info": "出牌阶段限一次，对一名其他角色造成1点火焰伤害。"
}
```
</details>

2. 创建一个装备牌：
   - 定义装备效果
   - 添加装备技能
   - 设置距离修正

<details>
<summary>参考答案 | 🟩 Easy</summary>

```javascript
card: {
    "my_equip": {
        type: "equip",               // 装备牌
        subtype: "equip1",           // 武器
        distance: {
            attackFrom: -1,          // 攻击距离+1
        },
        skills: ["my_equip_skill"],  // 装备技能
        onLose: function(){          // 失去装备时
            player.chooseToDiscard("hes", true);
        },
        onGain: function(){          // 获得装备时
            player.draw();
        },
        ai: {
            basic: {
                equipValue: 5,        // 装备价值
                order: 5,             // 使用优先级
                useful: 2,            // 使用价值
            }
        }
    }
},
skill: {
    "my_equip_skill": {
        trigger: {source: 'damageBegin1'},
        forced: true,
        filter: function(event, player){
            return event.card && event.card.name == 'sha';
        },
        content: function(){
            trigger.num++;            // 伤害+1
        },
        ai: {
            damageBonus: true
        }
    }
},
translate: {
    "my_equip": "神剑",
    "my_equip_info": "装备时摸一张牌；装备后攻击范围+1。使用【杀】造成的伤害+1；失去后弃置一张牌，。",
    "my_equip_skill": "神剑",
    "my_equip_skill_info": "锁定技，你使用【杀】造成的伤害+1。"
}
```
</details>

3. 创建一个延时锦囊：
   - 设计判定效果
   - 添加持续效果
   - 实现特殊规则

<details>
<summary>参考答案 | 🟩 Easy</summary>

```javascript
card: {
    "my_delay": {
        type: "delay",               // 延时锦囊
        enable: true,                // 可以使用
        filterTarget: function(card, player, target){
            return !target.hasJudge('my_delay'); // 判断是否已有同名判定牌
        },
        judge: function(card){       // 判定函数
            if(get.color(card) == 'red') return 1;
            return -1;
        },
        effect: function(){          // 判定效果
            if(result.bool){
                player.draw(2);      // 判定成功摸牌
            } else {
                player.damage('thunder'); // 判定失败受到伤害
            }
        },
        cancel: function(){          // 判定牌被取消时
            player.draw();           // 摸一张牌
        },
        ai: {
            basic: {
                order: 1,
                useful: [5,1],       // [有利,不利]
                value: [5,1],
            },
            result: {
                target: function(player, target){
                    return -1.5;     // 对目标负面效果
                }
            }
        }
    }
},
translate: {
    "my_delay": "天雷",
    "my_delay_info": "出牌阶段，对一名角色使用。其判定阶段进行判定：若为红色，其摸两张牌；否则受到1点雷电伤害。"
}
```
</details>
</br>
下一章我们将学习如何开发游戏模式。 