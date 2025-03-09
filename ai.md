# 6.1 AI设计

## 1. AI系统概述

无名杀的AI系统主要包括：
- 基础判断
- 行为决策
- 目标选择
- 卡牌评估
- 技能策略

## 2. 基础判断

### 2.1 态度判断
```javascript
skill: {
    "ai_skill": {
        // 基础态度判断
        ai: {
            threaten: 1.5,  // 威胁度(默认为1)
            effect: { // 对出牌的影响
                /*
                * 自身作为目标时的影响，会影响其他角色AI的出牌策略。
                * 可用 A ，[A,B],[A,B,C,D]格式
                * A：基础值 × 系数A
                * [A,B]：基础值 × 系数A + B
                * [A,B,C,D]：对你的影响：基础值 × 系数A + B | 对使用者的影响：基础值 × 系数C + D 
                */
                target(card, player, target){
                    if(get.tag(card, 'damage')) return 1.5;  // 其他AI倾向于对持有者使用伤害牌
                }
                /*
                * 自身使用牌时的影响，会影响自身AI的出牌策略。
                * 可用 A ，[A,B],[A,B,C,D]格式
                * A：基础值 × 系数A
                * [A,B]：基础值 × 系数A + B
                * [A,B,C,D]：对你的影响：基础值 × 系数A + B | 对被使用者的影响：基础值 × 系数C + D 
                */
                player (card, player, target) {
                    if (get.tag(card, 'damage')) return [1,1];  // 你倾向于使用伤害牌
                }
            }
        }
    }
}
```

### 2.2 形势判断
```javascript
ai: {
    // 价值判断
    value(card, player){
        if(card.name == 'tao' && player.hp <= 2) return 8;  // 濒死状态桃价值高
        if(card.name == 'shan' && player.hp == 1) return 7;  // 濒死状态闪价值高
        return get.value(card);  // 默认价值
    }
}
```

### 2.3 AI 优先级
```javascript
ai: {
    // 技能使用优先级(1-10)
    order(item, player){
        if(player.hp < 2) return 10;  // 濒死优先使用
        if(player.hasSkill('skill_name')) return 8;  // 配合其他技能
        return 4; // 默认优先级
    },
    
    // 发动技能的收益评估
    result: { // 二者选一个即可
        player(player){ // 对自身的收益，正数使用，负数取消
            if(player.hp < 2) return 2;  // 濒死收益加倍
            return 1;
        },
        target(player, target){ // 对目标的收益，正数选友方，负数选敌方
            if(target.hp == 1) return -2;  // 目标濒死收益加倍
            return -1; 
        }
    },
    
    // 特殊标签
    tag: {
        recover: 0.5,    // 回复技能
        gain: 1,         // 摸牌技能
        multitarget: 1,  // 多目标技能
                         // ....
    }
}
```

## 3. 行为决策

### 3.1 技能使用
```javascript
"ai_skill": {
    enable: "phaseUse",
    filter(event, player){
        return player.countCards('h') > 0;
    },
    check(event, player){ // 为True发动，为False取消
        // 基础判断
        if(player.hp <= 2) return 0;  // 生命危险不发动
        
        // 形势判断
        var enemies = game.countPlayer(function(current){
            return get.attitude(player, current) < 0;
        });
        if(enemies <= 1) return 0;  // 敌人太少不发动
        
        // 收益判断
        if(player.countCards('h') >= 4) return 1;  // 手牌充足可发动
        return 0;  // 默认不发动
    },
    ai: {
        order: 7,  // 发动优先级
        result: {
            player(player){
                if(player.hp <= 2) return -1;  // 生命危险收益为负
                return 1;  // 默认正收益
            },
            target(player, target){
                return get.damageEffect(target, player, player) > 0 ? -1 : 0;  // 伤害收益
            }
        }
    }
}
```

### 3.2 目标选择
```javascript
"target_skill": {
    enable: "phaseUse",
    filterTarget(card, player, target){
        return target != player;
    },
    ai: {
        // 目标价值判断
        result: {
            target(player, target){
                // 基础态度
                var att = get.attitude(player, target);
                if(att > 0) return 0;  // 不选择友方
                
                // 目标状态
                if(target.hp == 1) return -2;  // 濒死目标优先
                if(target.countCards('h') <= 2) return -1.5;  // 手牌少目标优先
                
                // 威胁判断
                if(target.hasSkillTag('threaten')) return -1.2;  // 威胁目标优先
                
                return -1;  // 默认价值
            }
        },
        
        // 目标排序
        expose: 0.2,  // 身份暴露度
        threaten: 1.5,  // 威胁度
    }
}
```

## 4. 卡牌评估
```javascript
ai: {
    basic: {
        // 基本牌评估
        useful: [4, 1],
        
        // 装备评估
        equipValue(card, player){
            if(card.name == 'bagua') return 5;  // 八卦阵价值
            return 3;  // 默认装备价值
        }
    }
}
```

## 5. 技能策略

### 5.1 主动技能
```javascript
"active_skill": {
    enable: "phaseUse",
    usable: 1,
    ai: {
        // 发动优先级
        order(item, player){
            if(player.hp <= 2) return 10;  // 生命危险优先发动
            return 4;  // 默认优先级
        },
        
        // 发动条件
        result: {
            player(player){
                // 收益评估
                var benefit = 0;
                if(player.hp <= 2) benefit += 2;  // 生命危险收益高
                if(player.countCards('h') <= 2) benefit += 1;  // 缺牌收益高
                return benefit;
            }
        }
    }
}
```

### 5.2 触发技能
```javascript
"trigger_skill": {
    trigger: {player: 'damageEnd'},
    filter(event, player){
        return player.countCards('h') > 0;
    },
    check(event, player){
        // 触发收益判断
        if(player.hp <= 2) return true;  // 生命危险必定发动
        if(player.countCards('h') >= 4) return false;  // 手牌多不发动
        return true;  // 默认发动
    },
    ai: {
        // 收益效果
        effect: {
            target(card, player, target){
                if(get.tag(card, 'damage')){
                    if(player.hasSkillTag('jueqing')) return [1,-2];
                    if(target.hp <= 1) return [1,0,0,-2];
                    return [1,0,0,-1.5];
                }
            }
        },
        
        // 触发优先级
        threaten: 0.8
    }
}
```

## 6.特殊标签
```javascript
ai: {
    // 技能标签的生效限制条件
    skillTagFilter(player, tag, target) {
        if (player == target && tag == "viewHandcard") return false;
    }, // 可看见除自己外所有人的手牌

    /*
    * 实际效果标签
    */
    combo: "XXX", // 组合技，持有XXX时收益增加
    directHit_ai: true, // 可强中
    filterDamage: true, // 伤害减免
    fireAttack: true, // 可造成火属性伤害
    freeShan: true, // 无消耗的【闪】
    guanxing: true, // 可观星
    halfneg: true, // 半负面技
    ignoreSkill: true // 忽略技能检测
    jiuOther: true, // 可响应【酒】
    left_hand: true, // 逆时针计算距离
    maixie_defend: true, // 卖血防御技
    maixie_hp: true, // 优先回血
    maixie: true, // 卖血技
    neg: true, // 负面技，强制发动
    noautowuxie: true, // 无法自动无懈
    noCompareSource: true, // 无法发起拼点
    noCompareTarget: true, // 无法作为拼点目标
    nodamage: true, // 不受伤害
    nodiscard: true, // 弃置牌无收益
    nodu: true, // 不受毒影响
    noe: true, // 失去装备时正收益
    nofire: true, // 不受火焰伤害
    nogain: true, // 获得牌无收益
    noh: true, // 没有手牌时正收益
    nohujia: true, // 无视护甲
    noLink: true, // 不能被横置
    nolose: true, // 失去牌无收益
    nomingzhi: true, // 无法明置
    norespond: true, // 无法响应牌
    noShan: true, // 不用【闪】
    nothunder: true, // 不受雷电伤害
    notrick: true, // 免疫锦囊
    notricksource: true, // 免疫锦囊牌造成的伤害
    noTurnover: true, // 不能被翻面
    playernowuxie: true, // 不响应无懈
    rejudge: true, // 可修改判定
    respondSha: true, // 可响应【杀】
    respondShan: true, // 可响应【闪】
    respondTao: true, // 可响应【桃】
    reverseEquip: true, // 反转装备优先级
    right_hand: true, // 顺时针计算距离
    save: true, // 濒死状态可救人
    undist: true, // 自身不计入距离
    unequip_ai: true, // 可无视护甲
    unequip: true, // 无视防具
    unequip2: true, // 自身防具无效
    usedu: true, // 使用毒有收益
    useShan: true, // 【闪】能用则用
    viewHandcard: true, // 可看见其他角色的手牌
}
```

## 练习

1. 创建一个基础AI：
   - 实现基本判断
   - 添加目标选择
   - 设计使用策略

<details>
<summary>参考答案 | 🟩 Easy</summary>

```javascript
"basic_ai": {
    enable: "phaseUse",
    usable: 1,
    filterTarget(card, player, target){
        return target != player;
    },
    filter(event, player){
        return player.countCards('h') > 0;
    },
    async content(event, trigger, player){
        await target.damage();
    },
    ai: {
        // 基础判断
        order(item, player){
            if(player.hp <= 2) return 0; // 生命危险时不发动
            return 4;
        },
        
        // 目标选择
        result: {
            target(player, target){
                // 基础态度判断
                let attitude = get.attitude(player, target);
                if(attitude > 0) return 0; // 不选择友方
                
                // 目标状态判断
                if(target.hp == 1) return 2; // 濒死目标优先
                if(target.hasSkillTag('threaten')) return 1.5; // 威胁目标优先
                
                return -1;
            }
        },
        
        // 使用策略
        effect: {
            target(card, player, target){
                if(get.tag(card, 'damage')){
                    if(player.hasSkillTag('jueqing')) return [1,-2];
                    if(target.hp == 1) return [1,0,0,-2];
                    return [1,0,0,-1.5];
                }
            }
        },
        
        // 威胁度
        threaten: 1.2
    }
}
```
</details>

2. 创建一个技能AI：
   - 设计发动条件
   - 实现目标选择
   - 添加收益判断

<details>
<summary>参考答案 | 🟩 Easy</summary>

```javascript
"skill_ai": {
    trigger: {player: 'phaseBegin'},
    direct: true,
    filter(event, player){
        return player.countCards('h') > 1;
    },
    async content(event, trigger, player){
        // 发动条件判断
        let check = false;
        let enemies = game.filterPlayer(function(current){
            return get.attitude(player, current) < 0;
        });
        
        if(player.hp <= 2 && enemies.length >= 2){
            check = true; // 生命危险且敌人多时发动
        }
        
        let prompt = '是否发动【技能名】?';
        let next = player.chooseBool(prompt).forResult();
        next.set('ai', function(){
            return check;
        });
        
        if(next.bool){
            player.logSkill('skill_ai');
            
            // 目标选择
            let next = player
                .chooseTarget(2, '选择两名目标', function(card, player, target){
                    return target != player;
                })
                .set('ai', function(target){
                let player = _status.event.player;
                let att = get.attitude(player, target);
                
                // 收益判断
                if(att < 0){
                    if(target.hp == 1) return 10; // 濒死敌人优先
                    if(target.hasSkillTag('threaten')) return 8; // 威胁目标优先
                    return 6;
                }
                return 0;
                })
                .forResult();
        }

        if(next.bool && next.targets && next.targets.length == 2){
            let targets = next.targets;
            for(let target of targets){
                await target.damage();
            }
        }
    },
    ai: {
        expose: 0.3, // 暴露程度
        threaten: 1.5 // 威胁度
    }
}
```
</details>

3. 创建一个复杂AI：
   - 实现动态策略
   - 添加场景判断
   - 优化性能

<details>
<summary>参考答案 | 🟨 Medium</summary>

```javascript
"complex_ai": {
    // 缓存计算结果
    init(player){
        player.storage.aiCache = {
            situation: null,
            lastUpdate: 0
        };
    },
    
    // 场景评估
    getSituation(player){
        let now = get.utc();
        if(!player.storage.aiCache.situation || 
           now - player.storage.aiCache.lastUpdate > 1000){
            
            let situation = 0;
            // 我方状态
            situation += player.hp;
            situation += player.countCards('h');
            
            // 敌方状态
            game.countPlayer(function(current){
                if(get.attitude(player, current) < 0){
                    situation -= current.hp;
                    situation -= current.countCards('h');
                }
            });
            
            player.storage.aiCache.situation = situation;
            player.storage.aiCache.lastUpdate = now;
        }
        return player.storage.aiCache.situation;
    },
    
    enable: "phaseUse",
    usable: 1,
    filter(event, player){
        return player.countCards('h') > 0;
    },
    async content(event, trigger, player){
        // 动态策略选择
        let situation = lib.skill.complex_ai.getSituation(player);
        let strategy;
        
        if(situation > 5){
            strategy = 'aggressive'; // 优势时进攻
        } else if(situation < -5){
            strategy = 'defensive'; // 劣势时防守
        } else {
            strategy = 'neutral'; // 均势时中庸
        }
        
        // 根据策略选择效果
        let choices = ['效果1', '效果2', '效果3'];
        let next = player
            .chooseControl(choices)
            .set('ai', function(){
                switch(strategy){
                    case 'aggressive':
                        return '效果1';
                    case 'defensive':
                        return '效果2';
                    default:
                        return '效果3';
                }
            })
            .forResult();

        // 执行选择的效果
        switch(next.control){
            case '效果1':
                let target = await player.chooseTarget(
                    function(card, player, target){
                        return target != player;
                    }
                ).set('ai', function(target){
                    return -get.attitude(player, target);
                })
                .forResult();
                if(target.bool){
                    await target.targets[0].damage();
                }
                break;
                
            case '效果2':
                await player.draw(2);
                break;
                
            case '效果3':
                await player.recover();
                break;
        }
    },
    ai: {
        // 动态优先级
        order(item, player){
            let situation = lib.skill.complex_ai.getSituation(player);
            if(situation > 5) return 8;
            if(situation < -5) return 4;
            return 6;
        },
        
        // 动态收益
        result: {
            player(player){
                let situation = lib.skill.complex_ai.getSituation(player);
                if(situation > 5) return 2;
                if(situation < -5) return 0.5;
                return 1;
            }
        },
        
        // 威胁度
        threaten(player, target){
            let situation = lib.skill.complex_ai.getSituation(player);
            if(situation > 5) return 2;
            return 1;
        }
    }
}
```
</details>
</br>
下一节我们将学习联机适配。 
