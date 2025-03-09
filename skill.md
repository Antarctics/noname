# 4.1 技能系统

## 1. 技能基本结构

技能是武将最核心的组成部分，一个完整的技能通常包含以下部分：

<details>
<summary>展开示例</summary>

```javascript
"skill_name": {
    // 基础属性
    /**
     * 筛选条件
	 * @param event 触发事件
	 * @param player 持有角色
     * @param name 触发事件名
     * @param target 本次触发目标，需要有getIndex才可用
     * @description
     * 返回true时才可执行技能。
     */
    filter(event, player, name, target){},
    /**
     * 执行要求
	 * @param event 技能事件
     * @param trigger 触发事件
	 * @param player 持有角色
     * @description
     * 技能满足筛选条件后触发。
     * 此时不视为执行技能，可以用来做筛选。
     * 如：张辽【突袭】请选择至多两名角色获得其手牌
     * 若取消则不执行技能。
     * event.result.bool为true时执行技能。
     */
    async cost(event, trigger, player){},
     /**
     * 技能效果
	 * @param event 技能事件
     * @param trigger 触发事件
	 * @param player 持有角色
     * @description
     * 技能执行的效果
     */
    async content(event, trigger, player){}, // 技能效果
    
    // 可选属性
    /**
     * 初始化
	 * @param player 持有角色
	 * @param skill 当前技能名
     * @description
     * 获得技能时执行的事件
     */
    init(player, skill){},
    /**
     * 技能配音
     * @param {string | number | boolean | [string, number]} 详情请查看 配音系统 章节
     */
    audio: 2,
    /**
     * 触发时机，类似被动技能，不能与 主动使用 同时存在。
     * @param {object} 具体触发时机，详细参数请查看 触发时机 章节
     */
    trigger: {},
    /**
     * 主动使用，主动技，不能与 触发时机 同时存在
     * @param {string | string[]} 使用时机
     * @description
     * 该方法所支持的参数类型：
     * - `chooseCard` 参数：选牌时可用
     * - `chooseToRespond` 参数：打出牌时可用
     * - `chooseToUse` 参数：使用牌时可用
     * - `phaseUse` 参数：出牌时可用
     */
    enable: "",
    /**
     * 每回合使用次数
	 * @param skill 当前技能
	 * @param player 持有角色
     */
    usable: ((skill: string, player: Player) => number) | number,
    /**
     * 每轮使用次数
	 * @param {number} num 使用次数
     */
    round: 1,
    /**
     * 技能发动次数
	 * @param event 当前事件
	 * @param player 持有角色
     * @param triggername 触发名称
     * @description
     * 返回的数组长度即为本次技能执行次数
     * 第X次执行的目标为数组中的第X-1个元素。
     */
	getIndex: ((event, player, triggername)=>object[]),
    /**
     * 是否强制发动
	 * @param {boolean} 强制发动
     * @description
     * 若为true，默认为锁定技。
     * 需要将locked修改为false才可实现强制发动的非锁定技。
     */
    forced: true,
    /**
     * 是否锁定
	 * @param {boolean} 锁定
     * @description
     * 能否被“非锁定技失效”封印
     */
    locked: true,
    /**
     * 自动确认
	 * @param {boolean} 是否自动发动
     * @description
     * 可在游戏中自选是否自动，仅仅只是跳过询问这一流程。
     */
    frequent: true,
    /**
     * 衍生技
	 * @param {string | string[]} 技能ID
     * @description
     * 在技能下面显示的对应技能的描述
     */
    derivation: [],
    charlotte: true,            // 是否为锁定技
    vanish: true,               // 一次性技能，使用resetSkills重置技能时直接移除此技能。
    popup: false,               // 发动技能是否记录
    nopop: true,                // 是否显示技能描述
    direct: true,               // 是否强制发动技能且无记录
    skillAnimation: true,       // 是否播放动画
	animationColor: "gray",     // 动画文字颜色
    sourceSkill: "XXX",         // 源技能，若存在，则当前技能实际id为"XXX_skill"
    group: ['subskill1'],       // 关联子技能，持有此技能会同时视为持有子技能
    logTarget: "target",        // 技能显示的目标
    mark: "auto",               // 是否显示标记，同时也支持布尔值。
    equipSkill: true,           // 是否为装备技能
    prompt: "XXX",              // 发动技能提示
    filterCard: {},             // 是否需要筛选卡牌
    position: "h",               // 指定卡牌位置
    filterTarget: (),           // 是否需要筛选目标
    selectTarget: (),           // 需要选择的目标数
    viewAs: {},                 // 视为使用卡牌
    viewAsFilter: {},           // 视为使用条件
    onuse: {},                  // 视为后执行的效果
    onremove: {},               // 失去技能后执行的效果
    intro: {},                  // 标记内容
    check: {},                  // AI是否发动技能(被动技)
    mod: {},                    // 属性修改(视为锁定技)
    ai: {},                     // AI策略
                                // ....更多选项请查看源码
}
```

</details>


## 2. 技能类型

下文仅列出部分常见技能类型，全部技能类型请查看
- [3.7 技能类型概述](./chapter3-skill/3.7-skill-types.md)

### 2.1 主动技能
```javascript
"my_skill": {
    enable: "phaseUse",         // 出牌阶段使用
    usable: 1,                  // 每回合限一次
    filter(event, player){
        return player.countCards('h') > 0; // 需要有手牌
    },
    filterTarget(card, player, target){ // 此效果意为需要选择目标，返回值为数组，传参为event.targets。
        return target != player; // 不能选择自己
    },
    async content(event, trigger, player){
        await event.targets[0].damage();   // 对目标造成伤害
    }
} // 出牌阶段限一次，你可以对一名其他角色造成1点伤害
```

### 2.2 触发技能
```javascript
"trigger_skill": {
    trigger: {
        player: "phaseBegin",   // 回合开始时
        global: "damageEnd",    // 任何角色受到伤害后
    },
    forced: true,               // 强制使用
    filter(event, player){
        return player.hp < 3;    // 体力值小于3时触发
    },
    async content(event, trigger, player){
        await player.draw();     // 摸一张牌
    }
} // 锁定技，你的回合开始时或任意角色受到伤害后，若你的体力值小于3，则你摸一张牌。
```

### 2.3 视为技能
```javascript
"viewas_skill": {
    enable: ["chooseToUse", "chooseToRespond"], // 可以使用或打出
    filterCard: {color: "red"}, // 红色牌
    viewAs: {name: "sha"},      // 视为【杀】
    viewAsFilter(player){ // 视为技条件
        return player.countCards('h', {color: 'red'}) > 0;
    }, // 需要有手牌
    prompt: "将一张红色牌当【杀】使用或打出",
    ai: {
        respondSha: true,       // 告诉AI，此技能可以用来响应杀
        skillTagFilter(player){
            return player.countCards('h', {color: 'red'}) > 0;
        } // 有红色的手牌时才告诉AI
    }
} // 你可以将一张红色牌当【杀】使用或打出
```

### 2.4 锁定技
```javascript
"lock_skill": {
    charlotte: true,              // 锁定技标记
    trigger: {player: 'damageBegin4'},
    filter(event, player){
        return event.nature == 'fire'; // 火焰伤害
    },
    content(){
        trigger.cancel();      // 取消事件
    }
} // 锁定技，你防止即将受到的火焰伤害
```

### 2.5 限定技
```javascript
"limit_skill": {
    unique: true,              // 独有技能(不会被“化身”获取)
    limited: true,             // 限定技标记
    skillAnimation: true,      // 播放技能动画
    animationColor: "fire",    // 动画颜色
    enable: "phaseUse",        // 出牌阶段使用
    filter(event, player){
        return !player.storage.limit_skill; // 未使用过
    },
    async content(event, trigger, player){
        player.awakenSkill('limit_skill');  // 废除此技能
        await player.draw(3);               // 摸三张牌
        await player.recover();             // 回复1点体力
    }
} // 限定技，出牌阶段，你可以摸三张牌并回复1点体力
```

## 3. 技能效果实现

### 3.1 基础效果
```javascript
async content(event, trigger, player){
    // 摸牌
    await player.draw(2);
    
    // 摸至
    await player.drawTo(5);

    // 回复体力
    await player.recover();

    // 回复至
    await player.recoverTo(5)
    
    // 受到伤害
    await target.damage('fire');
    
    // 失去体力
    await player.loseHp();
    
    // 获得牌
    await player.gain(trigger.cards, 'gainAuto');
    
    // 弃置牌
    await player.discard(player.getCards('h'));
} // 基础效果示例
```

### 3.2 选择效果
```javascript
async content(event, trigger, player){
    // 选择角色
    let result = await player.chooseTarget('请选择一名角色', true).forResult();
    if(result.bool){
        let target = result.targets[0];
        await target.draw();
    }
    
    // 选择牌
    let cards = await player.chooseCard('h', '请选择一张手牌').forResult();
    if(cards.bool){
        await player.discard(cards.cards);
    }
    
    // 选择选项
    let choice = await player.chooseControl('选项1', '选项2')
        .set('prompt', '请选择一个选项')
        .forResult();
    if(choice.control === '选项1'){
        await player.draw();
    }
} // 选择效果示例
```

### 3.3 条件判断
```javascript
async content(event, trigger, player){
    // 体力值判断
    if(player.hp <= 2){
        await player.draw();
    }
    
    // 手牌数判断
    if(player.countCards('h') < 2){
        await player.draw(2);
    }
    
    // 距离判断
    if(player.inRange(target)){
        await target.damage();
    }
    
    // 势力判断
    if(target.group === 'shu'){
        await target.draw();
    }
} // 条件判断示例
```

## 4. 技能标记系统

### 4.1 基础标记
```javascript
"mark_skill": {
    mark: true,               // 显示标记
    marktext: "标",           // 标记文字
    intro: {
        content: "标记内容",  // 标记描述
    },
    async content(event, trigger, player){
        player.addMark('mark_skill', 1);    // 添加标记
        // 或
        player.removeMark('mark_skill', 1); // 移除标记
    }
} // 标记系统示例
```

### 4.2 存储标记
```javascript
"storage_skill": {
    init(player){
        player.storage.storage_skill = 0; // 初始化存储值
    },
    mark: true,
    intro: {
        content: "当前持有#个标记"
    },
    async content(event, trigger, player){
        player.storage.storage_skill++;    // 增加存储值
        player.markSkill('storage_skill'); // 更新标记显示
    }
} // 存储标记示例
```

## 5. 技能组合

### 5.1 子技能
```javascript
"main_skill": {
    group: ["main_skill_1", "main_skill_2"], // 关联子技能
    subSkill: {
        "1": {
            trigger: {player: 'phaseBegin'},
            async content(event, trigger, player){
                player.draw();
            },
        },
        "2": {
            trigger: {player: 'phaseEnd'},
            async content(event, trigger, player){
                player.recover();
            },
        }
    }
} // 回合开始时摸一张牌，回合结束时回复1点体力
```

### 5.2 技能联动
```javascript
"skill_1": {
    trigger: {player: 'phaseBegin'},
    async content(event, trigger, player){
        player.addTempSkill('skill_2'); // 获得临时技能
        player.addTempSkill('skill_3',{ global: "roundStart" }); // 获得临时技能，持续到本轮结束
    }
},
"skill_2": {
    trigger: {player: 'damageEnd'},
    async content(event, trigger, player){
        player.draw();
    }
} // 回合开始时获得临时技能，受到伤害后摸一张牌
```

## 练习题

1. 创建一个触发技能：
   - 回合开始时触发
   - 可以选择摸牌或回复体力

<details>
<summary>参考答案 | 🟩 Easy</summary>

```javascript
"trigger_example": {
    usable: 1,
    trigger: {player: 'phaseBegin'},
    async content(event, trigger, player){
        // 选择效果
        let choice = await player.chooseControl('摸两张牌', '回复1点体力')
            .set('prompt', '请选择一个效果')
            .set('ai', function(){ // AI策略
                if(player.hp <= 2) return '回复1点体力'; // 血量较低时选择回血
                return '摸两张牌'; // 否则选择摸牌
        }).forResult();
        // 执行效果
        if(choice.control == '摸两张牌'){
            await player.draw(2);
        } else {
            await player.recover();
        }
    },
} // 每回合限一次，回合开始时,你可以选择以下一项:1.摸两张牌;2.回复1点体力。
```
</details>

2. 创建一个主动技能：
   - 出牌阶段限一次
   - 弃置一张牌并指定一名角色
   - 目标角色受到1点伤害

<details>
<summary>参考答案 | 🟩 Easy</summary>

```javascript
"active_example": {
    enable: "phaseUse",
    usable: 1,
    filter(event, player){
        return player.countCards('h') > 0;
    },
    filterTarget(card, player, target){
        return target != player;
    },
    filterCard: true, // 持有此效果意为需要选择牌
    position: "h",
    async content(event, trigger, player){
        await event.targets[0].damage();
    },
    ai: {
        order: 7,
        result: {
            target(player, target){ // AI选人逻辑，正数选队友，负数选敌方，0不选。
                var att = get.attitude(player, target) // 持有人对目标的态度，负数为敌方，正数为友方。
                if(target.hp == 1) att = att * 2;   // 若角色血量为1，则优先级更高
                if(att < 0) return att; // 若为敌方，则使用技能。
            }
        },
        expose: 0.2
    }
} // 出牌阶段限一次,你可以弃置一张手牌并对一名其他角色造成1点伤害。
```
</details>

3. 创建一个复杂技能：
   - 包含触发和主动两部分
   - 使用技能标记系统
   - 实现技能联动

<details>
<summary>参考答案 | 🟨 Medium</summary>

```javascript
"complex_example": {
    // 初始化标记
    init(player){
        player.setMark('complex_example', 0);
    },
    // 标记显示
    mark: true,
    intro: {
        content: '当前标记数：#'
    },
    // 主动部分
    enable: "phaseUse",
    usable: 1,
    filter(event, player){
        return player.countMark('complex_example') > 0;
    },
    async content(event, trigger, player){
        // 消耗标记发动效果
        player.removeMark('complex_example', 1);
        
        // 选择目标造成伤害
        let result = await player.chooseTarget('选择一名目标角色').forResult();
        if(result.bool){
            await result.targets[0].damage();
        }
    },
    // 触发部分
    group: ['complex_example_damage'],
    // 子技能
    subSkill: {
        damage: {
            trigger: {player: 'damageEnd'},
            forced: true,
            async content(event, trigger, player){
                // 受到伤害获得标记
                player.addMark('complex_example', trigger.num); // 获得标记
            },
        }
    },
    // AI策略
    ai: {
        order: 6, // 使用技能的优先级。
        result: {
            target: -1
        },
        threaten: 1.5 // 威胁度，AI会优先激活威胁度最高的角色。
    }
} // 锁定技,当你受到伤害后,你获得相同个数的"示例"标记。出牌阶段限一次,你可以移去一个"示例"标记并对一名角色造成1点伤害。
```
</details>
</br>
下一节我们将学习技能的触发器。
