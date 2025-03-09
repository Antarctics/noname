# 4.4 技能标记

## 1. 标记系统概述

标记系统是无名杀中记录技能状态和数值的重要机制,主要包括:
- 基础标记
- 数值标记
- 临时标记
- 持续标记

## 2. 基础标记

### 2.1 标记定义
```javascript
"mark_skill": {
    mark: true,                    // 显示标记
    marktext: "标",                // 标记显示的文字
    intro: {
        name: "标记名称",
        content: "标记描述",
        markcount: "标记数量"
    },
}
```

### 2.2 标记操作
```javascript
async content(event, trigger, player){
    // 添加标记
    player.addMark('mark_skill');
    
    // 移除标记
    player.removeMark('mark_skill');
    
    // 判断是否有标记
    if(player.hasMark('mark_skill')){
        // 有标记时的操作
    }
}
```

## 3. 数值标记

### 3.1 基本用法
```javascript
"count_mark": {
    mark: true,
    marktext: "数",
    intro: {
        content: "当前标记数：#",   // #会被替换为实际数值
    },
    init(player){
        player.storage.count_mark = 0;  // 初始化标记值
    },
    content(){
        // 增加标记
        player.addMark('count_mark', 1);
        // 或
        player.storage.count_mark++;
        
        // 减少标记
        player.removeMark('count_mark', 1);
        // 或
        player.storage.count_mark--;

        // 更新所有标记
        player.updateMarks();
        // 更新标记
        player.markSkill('count_mark');
        // 删除标记
        player.unmarkSkill('count_mark')
    }
}
```

### 3.2 高级用法
```javascript
"complex_mark": {
    mark: true,
    marktext: "复",
    intro: {
        content(storage, player){
            // 自定义标记显示内容
            let str = '当前状态：</br>';
            if(storage.count > 0){
                str += '可用次数：' + storage.count;
            }
            return str;
        }
    },
    init(player){
        // 复杂标记数据结构
        player.storage.complex_mark = {
            count: 0,
            used: false,
            targets: []
        };
    }
}
```

## 4. 临时标记

### 4.1 回合内标记
```javascript
"temp_mark": {
    mark: true,
    intro: {
        content: "本回合内可以发动",
    },
    content(){
        // 添加临时标记
        player.addTempSkill('temp_mark', 'phaseEnd');
        
        // 或指定多个时机
        player.addTempSkill('temp_mark', {
            phaseEnd: true,
            damageEnd: true
        });
    }
}
```

### 4.2 条件标记
```javascript
"condition_mark": {
    mark: true,
    intro: {
        content(storage, player){
            if(player.hp < 3){
                return "触发条件已满足";
            }
            return "触发条件未满足";
        }
    },
    onremove(player){
        // 标记移除时的操作
        delete player.storage.condition_mark;
    }
}
```

## 5. 持续标记

### 5.1 回合计数
```javascript
"round_mark": {
    mark: true,
    marktext: "轮",
    intro: {
        content: "剩余回合：#",
    },
    init(player){
        player.storage.round_mark = 3;  // 持续3回合
    },
    trigger: {player: 'phaseEnd'},
    forced: true,
    content(){
        player.storage.round_mark--;
        if(player.storage.round_mark <= 0){
            player.removeSkill('round_mark');
        }
    }
}
```

### 5.2 状态标记
```javascript
"state_mark": {
    mark: true,
    marktext: "状",
    intro: {
        content(storage, player){
            let states = {
                ready: "准备状态",
                active: "激活状态",
                exhaust: "消耗状态"
            };
            return states[storage] || "未知状态";
        }
    },
    init(player){
        player.storage.state_mark = 'ready';
    }
}
```

## 6. 进阶技巧

### 6.1 标记联动
```javascript
group: ["mark_skill_1", "mark_skill_2"],
subSkill: {
    "1": {
        trigger: {player: 'phaseBegin'},
        filter(event, player){
            return player.storage.mark_count > 0;
        },
        content(){
            // 标记数值影响技能效果
        }
    },
    "2": {
        trigger: {player: 'damageEnd'},
        content(){
            // 伤害影响标记数值
            player.addMark('mark_count', trigger.num);
        }
    }
}
```

### 6.2 标记显示
```javascript
"visual_mark": {
    mark: true,
    marktext: "★",
    intro: {
        name: "特殊标记",
        mark(dialog, storage, player){
			// 自定义标记显示界面
			dialog.addText('当前手牌:');
			dialog.add([player.getCards('h'),'vcard']);
        }
    }
}
```

## 7. 注意事项

1. **标记命名**
   - 使用有意义的名称
   - 避免与现有标记重名
   - 建议使用前缀区分

2. **性能优化**
   - 及时清理无用标记
   - 避免过多的标记更新
   - 合理使用临时标记

3. **联机兼容**
   - 确保标记同步
   - 使用标准API

## 练习

1. 创建一个计数标记：
   - 记录技能使用次数
   - 显示剩余次数
   - 达到条件后清除

<details>
<summary>参考答案 | 🟩 Easy</summary>

```javascript
"count_mark": {
    enable: "phaseUse",
    mark: true,
    intro: {
        name: "计数",
        content(storage) {
            return `已使用${storage["used"]}次，剩余可用次数：${storage["max"] - storage["used"]}`
        }
    },
    init(player){
        // 初始化标记
        player.storage.count_mark = {
            used: 0,
            max: 3
        };
    },
    filter(event, player){
        // 检查使用次数
        return player.storage.count_mark.used < player.storage.count_mark.max;
    },
    async content(event, trigger, player){
        // 增加使用次数
        player.storage.count_mark.used++;
        player.markSkill('count_mark');
        player.syncStorage('count_mark');
        
        // 执行效果
        await player.draw(2);
        
        // 达到次数后清除
        if(player.storage.count_mark.used >= player.storage.count_mark.max){
            player.unmarkSkill('count_mark');
            game.log(player, '的技能使用次数已达上限');
        }
    },
    ai: {
        order: 5,
        result: {
            player: 1
        }
    }
}
```
</details>

2. 创建一个状态标记：
   - 记录多个状态
   - 状态间可以转换
   - 不同状态有不同效果

<details>
<summary>参考答案 | 🟩 Easy</summary>

```javascript
"state_mark": {
    enable: "phaseUse",
    mark: true,
    intro: {
        name: "状态",
        content(storage){
            let states = {
                ready: "准备状态：可以发动技能",
                active: "激活状态：攻击距离+1",
                exhaust: "消耗状态：手牌上限+1"
            };
            return states[storage.state] || "未知状态";
        }
    },
    init(player){
        player.storage.state_mark = {
            state: 'ready'
        };
    },
    filter(event, player){
        return player.storage.state_mark.state == 'ready';
    },
    async content(event, trigger, player){
        // 选择转换的状态
        let states = ['active', 'exhaust'];
        let result = await player.chooseControl(states)
            .set('prompt', '请选择要转换的状态')
            .set('ai', function(){
                if(player.hp <= 2) return 'exhaust';
                return 'active';
            })
            .forResult();

        // 转换状态
        player.storage.state_mark.state = result.control;
        player.markSkill('state_mark');
        
        // 根据状态添加效果
        if(result.control == 'active'){
            player.addTempSkill('state_mark_active');
        } else {
            player.addTempSkill('state_mark_exhaust');
        }
    },
    group: ["state_mark_reset"],
    subSkill: {
        active: {
            mod: {
                attackRange(player, num){
                    return num + 1;
                }
            }
        },
        exhaust: {
            mod: {
                maxHandcard(player, num){
                    return num + 1;
                }
            }
        },
        reset: {
            trigger: {player: 'phaseEnd'},
            forced: true,
            filter(event, player){
                return player.storage.state_mark.state != 'ready';
            },
            content(){
                player.storage.state_mark.state = 'ready';
                player.markSkill('state_mark');
            }
        }
    }
}
```
</details>

3. 创建一个复杂标记：
   - 包含多个数据项
   - 实现标记联动
   - 自定义显示界面

<details>
<summary>参考答案 | 🟥 Hard</summary>

```javascript
"complex_mark": {
    mark: true,
    intro: {
        name: "复合标记",
        mark(dialog, storage, player){
            dialog.addText('当前状态:');
            // 显示能量值
            dialog.addText('能量: ' + storage.energy + '/' + storage.maxEnergy);
            // 显示buff列表
            if(storage.buffs && storage.buffs.length){
                dialog.addText('增益效果:');
                for(let buff of storage.buffs){
                    dialog.add([[buff.card], 'vcard']);
                    dialog.addText(buff.name + '(' + buff.duration + '回合)');
                }
            }
        },
    },
    init(player){
        player.storage.complex_mark = {
            energy: 0,
            maxEnergy: 5,
            buffs: []
        };
    },
    enable: "phaseUse",
    filter(event, player){
        return player.storage.complex_mark.energy > 0;
    },
    async content(event, trigger, player){
        // 选择效果
        let choices = ['获得增益', '造成伤害'];
        let result = await player.chooseControl(choices)
            .set('prompt', '请选择消耗能量的效果')
            .set('ai', function(){
                if(player.hp <= 2) return '获得增益';
                return '造成伤害';
            })
            .forResult();

        // 消耗能量
        player.storage.complex_mark.energy--;
        
        // 执行效果
        if(result.control == '获得增益'){
            // 添加buff
            player.storage.complex_mark.buffs.push({
                name: '攻击加成',
                duration: 2,
                card: ["heart","","sha","fire"]
            });
        } else {
            // 选择目标造成伤害
            let target = await player.chooseTarget('选择一名目标角色').forResult();
            if(target.bool){
                await target.targets[0].damage();
            }
        }
        
        // 更新标记
        player.markSkill('complex_mark');
    },
    group: ["complex_mark_gain", "complex_mark_update","complex_mark_buff"],
    subSkill: {
        gain: {
            trigger: {player: 'phaseBegin'},
            forced: true,
            content(){
                // 回合开始获得能量
                let storage = player.storage.complex_mark;
                if(storage.energy < storage.maxEnergy){
                    storage.energy++;
                    player.markSkill('complex_mark');
                }
            }
        },
        update: {
            trigger: {player: 'phaseEnd'},
            forced: true,
            content(){
                // 更新buff持续时间
                let storage = player.storage.complex_mark;
                if(storage.buffs && storage.buffs.length){
                    for(let buff of storage.buffs){
                        buff.duration--;
                    }
                    // 移除过期buff
                    storage.buffs = storage.buffs.filter(buff => buff.duration > 0);
                    player.markSkill('complex_mark');
                }
            }
        },
        buff: {
            trigger: {
                source: "damageBegin"
            },
            filter (event, player) {
                // 筛选加成目标
                let storage = player.storage.complex_mark;
                if (storage.buffs.some(buff => buff.name == "攻击加成")) {
                    return event.card.name == "sha";
                }
            },
            content () {
                // 实现效果
                trigger.num++;
                trigger.nature = "fire";
            },
        }
    }
}
```
</details>

下一节我们将学习技能的条件判断。 