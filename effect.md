# 4.3 技能效果

## 1. 基础效果

### 1.1 摸牌与弃牌
```javascript
async content(event, trigger, player){
    // 摸牌
    await player.draw(2);                    // 摸两张牌
    await player.drawTo(5);                  // 摸至五张牌
    
    // 弃牌
    await player.discard(player.getCards('h')); // 弃置所有手牌
    await player.chooseToDiscard(1, true);      // 强制弃置一张牌
}
```

### 1.2 体力操作
```javascript
async content(event, trigger, player){
    // 回复体力
    await player.recover();                  // 回复1点体力
    await player.recover(2);                 // 回复2点体力
    
    // 失去体力
    await player.loseHp();                   // 失去1点体力
    await player.loseMaxHp();                // 失去1点体力上限
    await player.gainMaxHp();                // 获得1点体力上限
    
    // 造成伤害
    await player.damage('fire');             // 造成1点火焰伤害
    await player.damage(2, 'thunder');       // 造成2点雷电伤害
    await player.damage("nosource");         // 造成1点无来源伤害
}
```

### 1.3 获得与给予
```javascript
async content(event, trigger, player){
    // 获得牌
    await player.gain(trigger.cards, 'gain2');   // 获得牌并展示
    await player.gainPlayerCard(target, true);   // 获得目标角色的一张牌
    
    // 给予牌
    await player.give(cards, target);            // 给予目标角色牌
    await target.$give(1, player);               // 播放给予动画(实际不给)
}
```

## 2. 选择效果

### 2.1 选择角色
```javascript
async content(event, trigger, player){
    // 选择一名角色
    let result = await player.chooseTarget('请选择一名角色', true).forResult();
    if(result.bool){
        let target = result.targets[0];
        await target.draw();
    }
    
    // 选择多名角色
    let result = await player.chooseTarget(2, true, '请选择两名角色').forResult();
    if(result.bool){
        for(let target of result.targets){
            await target.damage();
        }
    }
}
```

### 2.2 选择牌
```javascript
async content(event, trigger, player){
    // 选择手牌
    let result = await player.chooseCard('h', '请选择一张手牌').forResult();
    if(result.bool){
        await player.discard(result.cards);
    }
    
    // 选择特定牌
    let result = await player.chooseCard('he', {color:'red'}, '请选择一张红色牌').forResult();
    if(result.bool){
        await player.give(result.cards, target);
    }
}
```

### 2.3 选择选项
```javascript
async content(event, trigger, player){
    // 选择一个选项
    let result = await player.chooseControl('选项1', '选项2')
        .set('prompt', '请选择一个选项')
        .set('ai', ()=>{
            return player.hp < 3 ? '选项1' : '选项2';
        })
        .forResult();
    
    if(result.control === '选项1'){
        await player.draw();
    } else {
        await player.recover();
    }
}
```

## 3. 判定效果

### 3.1 基础判定
```javascript
async content(event, trigger, player){
    // 进行判定
    let result = await player.judge();
    if(result.color == 'red'){
        await player.draw(2);
    } else {
        await player.draw();
    }
}
```

### 3.2 自定义判定
```javascript
async content(event, trigger, player){
    let result = await player.judge(card => {
        if(get.color(card) == 'red') return 1;
        return 0;
    });
    
    if(result.bool){
        await player.draw(2);
    }
}
```

## 4. 复杂效果

### 4.1 连续效果
```javascript
async content(event, trigger, player){
    // 选择目标并执行连续效果
    let targets = await player.chooseTarget(2, true, '请选择两名角色').forResult();
    if(targets.bool){
        for(let target of targets.targets){
            await target.damage('fire');
            await target.draw();
        }
    }
}
```

### 4.2 条件分支
```javascript
async content(event, trigger, player){
    // 根据条件执行不同效果
    if(player.hp <= 2){
        let choice = await player.chooseControl('摸牌', '回血').forResult()
            .set('prompt', '请选择一项');
        if(choice.control === '摸牌'){
            await player.draw(2);
        } else {
            await player.recover();
        }
    } else {
        await player.draw();
    }
}
```

## 5. 特殊效果

### 5.1 技能获得与失去
```javascript
async content(event, trigger, player){
    // 获得技能
    player.addTempSkill('new_skill');  // 获得临时技能
    player.addSkill('permanent_skill');            // 获得永久技能
    
    // 失去技能
    player.removeSkill('some_skill');              // 失去技能
    player.awakenSkill('awaken_skill');            // 失效技能
}
```

### 5.2 状态变化
```javascript
async content(event, trigger, player){
    // 翻面与横置
    await player.turnOver();                       // 翻面
    await player.link();                           // 横置
    
    // 跳过阶段
    player.skip('phaseUse');                       // 跳过出牌阶段
    player.skip('phaseDraw');                      // 跳过摸牌阶段
}
```

## 练习

1. 创建一个复合效果技能：
   - 选择一名角色
   - 根据判定结果执行不同效果
   - 添加连续效果

<details>
<summary>参考答案 | 🟨 Medium</summary>

```javascript
"compound_skill": {
    enable: "phaseUse",
    usable: 1,
    filter(event, player){
        return player.countCards('h') > 0;
    },
    filterTarget(card, player, target){
        return target != player;
    },
    async content(event, trigger, player){
        // 选择一名角色
        let result = await player.chooseTarget('选择一名目标角色', true)
            .set('ai', target => -get.attitude(player, target))
            .forResult();
        if(result.bool){
            event.target = result.targets[0];
            // 进行判定
            let judge = await event.target.judge(function(card){
                if(get.color(card) == 'red') return 1;
                return 0;
            });
            // 根据判定结果执行效果
            if(result.bool){
                // 红色:目标摸牌
                await event.target.draw(2);
                // 连续效果:其他角色可以选择摸牌
                let others = game.filterPlayer(current => 
                    current != player && current != event.target
                );
                for(let other of others){
                    let choice = await other.chooseBool('是否摸一张牌？').forResult();
                    if(choice.bool){
                        await other.draw();
                    }
                }
            } else {
                // 黑色:目标受到伤害
                await event.target.damage('thunder');
                // 连续效果:其他角色可以选择弃牌
                let others = game.filterPlayer(current => 
                    current != player && current != event.target
                );
                for(let other of others){
                    if(other.countCards('h') > 0){
                        let choice = await other.chooseBool('是否弃置一张手牌？').forResult();
                        if(choice.bool){
                            await other.chooseToDiscard(1, true);
                        }
                    }
                }
            }
        }
    },
    ai: {
        order: 7,
        result: {
            target: -1.5
        }
    }
}
```
</details>

2. 创建一个状态变化技能：
   - 改变角色状态
   - 添加技能获得/失去
   - 实现阶段跳过

<details>
<summary>参考答案 | 🟩 Easy</summary>

```javascript
"state_skill": {
    enable: "phaseUse",
    usable: 1,
    filter(event, player){
        return !player.hasSkill('state_skill_effect');
    },
    async content(event, trigger, player){
        // 选择状态
        let choices = ['翻面并获得技能', '横置并跳过阶段', '废除装备栏并摸牌'];
        let choice = await player.chooseControl(choices)
            .set('prompt', '请选择一个状态效果')
            .set('ai', function(){
                if(player.isTurnedOver()) return '翻面并获得技能';
                if(player.countCards('h') <= 1) return '废除装备栏并摸牌';
                return '横置并跳过阶段';
            })
            .forResult();
            
        // 执行选择的效果
        switch(result.control){
            case '翻面并获得技能':
                await player.turnOver();
                player.addTempSkill('state_skill_effect');
                break;
                
            case '横置并跳过阶段':
                await player.link(true);
                player.skip('phaseUse');
                player.skip('phaseDiscard');
                break;
                
            case '废除装备栏并摸牌':
                player.disableEquip(1);
                player.disableEquip(2);
                await player.draw(3);
                break;
        }
    },
    subSkill: {
        effect: {
            mark: true,
            intro: {
                content: '获得技能效果'
            },
            mod: {
                maxHandcard(player, num){
                    return num + 2;
                }
            }
        }
    }
}
```
</details>

3. 创建一个选择类技能：
   - 提供多个选项
   - 根据条件限制选择
   - 实现不同效果

<details>
<summary>参考答案 | 🟨 Medium</summary>

```javascript
"choice_skill": {
    enable: "phaseUse",
    filter(event, player){
        if(player.storage.choice_skill_used) return false;
        if(player.hp < 2 && !player.countCards('h')) return false;
        return true;
    },
    async content(event, trigger, player){
        // 准备选项
        let choices = [];
        if(player.isDamaged()) choices.push('回复体力');
        if(player.countCards('h') < player.hp) choices.push('摸牌');
        if(player.countCards('h') > 0) choices.push('弃牌造成伤害');
        
        let choice = await player.chooseControl(choices)
            .set('prompt', '请选择一个效果')
            .set('ai', function(){
                if(player.hp <= 2 && choices.contains('回复体力')) 
                    return '回复体力';
                if(player.countCards('h') <= 1 && choices.contains('摸牌'))
                    return '摸牌';
                return '弃牌造成伤害';
            })
            .forResult();

        // 执行效果
        switch(result.control){
            case '回复体力':
                await player.recover();
                break;
                
            case '摸牌':
                await player.draw(player.hp - player.countCards('h'));
                break;
                
            case '弃牌造成伤害':
                let targets = await player.chooseTarget('选择一名目标角色', true).forResult();
                if(targets.bool){
                    await player.chooseToDiscard(1, true);
                    await targets.targets[0].damage();
                }
                break;
        }
        
        // 记录使用
        player.storage.choice_skill_used = true;
        player.markSkill('choice_skill_used');
    },
    group: "choice_skill_clear",
    subSkill: {
        used: {
            mark: true,
            intro: {
                content: '本回合已使用'
            },
            onremove: true
        },
        clear: {
            trigger: {player: 'phaseEnd'},
            forced: true,
            content(){
                delete player.storage.choice_skill_used;
                player.unmarkSkill('choice_skill_used');
            }
        }
    }
}
```
</details>
</br>
下一节我们将学习如何使用技能标记系统。 