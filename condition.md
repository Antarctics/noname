# 4.5 技能条件

## 1. 条件判断概述

技能条件判断主要包括:
- [filter函数判断](#filter)
- [check函数判断](#check)
- [mod条件判断](#mod)
- [特殊条件判断](#特殊)

## 2. Filter函数判断<a id="filter"></a>

### 2.1 基本用法
```javascript
"condition_skill": {
    trigger: {player: 'phaseBegin'},
    filter(event, player){
        // 基础条件
        return player.hp < 3;                    // 体力值小于3
        
        // 手牌条件
        return player.countCards('hs').some(card=> card.name == "sha");       // 有【杀】
        
        // 目标条件
        return target.isDamaged();               // 目标已受伤
        
        // 场上条件
        return game.hasPlayer(function(current){ // 场上存在满足条件的角色
            return current.hp < 2;
        });
    }
}
```

### 2.2 复合条件
```javascript
filter(event, player){
    // 多个条件同时满足
    return player.hp < 3 && 
           player.countCards('h') > 0 &&
           !player.hasSkill('some_skill');
    
    // 多个条件满足其一
    return player.hp < 3 || 
           player.countCards('h') == 0 ||
           player.isDamaged();
}
```

## 3. Check函数判断<a id="check"></a>

### 3.1 AI判断
```javascript
"check_skill": {
    enable: 'phaseUse',
    check(event, player){
        // 基础判断
        if(player.hp < 2) return 1;        // 推荐发动
        if(player.hp > 3) return 0;       // 不推荐发动
        
        // 形势判断
        if(player.hasUnknown()) return 0;  // 存在未知情况
        if(game.hasPlayer(function(current){
            return get.attitude(player,current) > 0 && current.hp == 1;
        })) return 2;                      // 队友濒死优先度高
    }
}
```

### 3.2 选择判断
```javascript
"choice_skill": {
    chooseButton: {
        dialog(event, player){
            return ui.create.dialog('选择一项', [
                ['sha', 'tao'], 'vcard'
            ]);
        },
        check(button){
            // 按钮选择判断
            if(button.link[2] == 'sha'){
                if(_status.event.player.hp < 2) return 0;
                return 1;
            }
            return 2; // 桃的优先度最高
        },
        backup(links, player){
            // 选择结果处理
        }
    }
}
```

## 4. Mod条件判断<a id="mod"></a>

### 4.1 基本用法
```javascript
"mod_skill": {
    mod: {
        // 使用条件
        cardEnabled(card, player){
            if(player.hp < 2) return false;
        },
        // 目标条件
        targetEnabled(card, player, target){
            if(target.hp < 2) return false;
        },
        // 数值条件
        cardUsable(card, player, num){
            if(card.name == 'sha') return num + 1;
        }
    }
}
```

### 4.2 复杂条件
```javascript
"complex_mod": {
    mod: {
        // 多重条件判断
        cardEnabled(card, player){
            if(!player.countCards('h')) return false;
            if(player.hasSkill('some_skill')) return false;
            if(_status.currentPhase != player) return false;
            return true;
        },
        // 动态数值判断
        maxHandcard(player, num){
            if(player.hp < 3) return num - 1;
            if(player.hasSkill('some_skill')) return num + 1;
            return num;
        }
    }
}
```

## 5. 特殊条件判断<a id="特殊"></a>

### 5.1 时机条件
```javascript
"timing_skill": {
    enable: 'phaseUse',
    filter(event, player){
        // 判断当前时机
        if(_status.currentPhase != player) return false;
        if(event.parent.name == 'phaseUse') return true;
        return false;
    }
}
```

### 5.2 状态条件
```javascript
"state_skill": {
    trigger: {player: 'damageEnd'},
    filter(event, player){
        // 判断角色状态
        if(player.isTurnedOver()) return false;
        if(player.isLinked()) return false;
        if(!player.isAlive()) return false;
        return true;
    }
}
```

## 6. 进阶技巧

### 6.1 条件缓存
```javascript
"cache_skill": {
    trigger: {player: 'phaseBegin'},
    filter(event, player){
        // 缓存复杂计算结果
        if(player.storage.cache_result === undefined){
            player.storage.cache_result = game.countPlayer(function(current){
                return current.hp < 2;
            });
        }
        return player.storage.cache_result > 0;
    },
    content(){
        // 使用后清除缓存
        delete player.storage.cache_result;
    }
}
```

### 6.2 动态条件
```javascript
"dynamic_skill": {
    mod: {
        cardEnabled(card, player){
            // 根据场上形势动态判断
            var enemies = game.countPlayer(function(current){
                return get.attitude(player, current) < 0;
            });
            if(enemies > 2) return false;
            return true;
        }
    }
}
```

## 7. 注意事项

1. **性能优化**
   - 避免复杂循环
   - 合理使用缓存
   - 减少不必要判断

2. **条件设计**
   - 条件要明确
   - 避免矛盾条件
   - 考虑边界情况

## 练习

1. 创建一个多重条件技能：
   - 包含体力值判断
   - 包含手牌数判断
   - 包含目标状态判断

<details>
<summary>参考答案 | 🟩 Easy</summary>

```javascript
"multi_condition": {
    enable: "phaseUse",
    filter(event, player){
        // 基础条件:体力值小于3且有手牌
        if(player.hp >= 3 || !player.countCards('h')) return false;
        
        // 场上条件:存在受伤角色
        return game.hasPlayer(function(current){
            return current.isDamaged();
        });
    },
    filterTarget(card, player, target){
        // 目标条件
        return target.isDamaged() && // 目标已受伤
               target.countCards('h') < target.hp && // 手牌数小于体力值
               !target.hasSkill('multi_condition_effect'); // 没有临时效果
    },
    async content(event, trigger, player){
        // 根据条件给予不同效果
        if(target.hp <= 2){
            await target.recover();
        } else {
            await target.draw(2);
        }
        target.addTempSkill('multi_condition_effect');
    },
    ai: {
        order: 7,
        result: {
            target(player, target){
                if(target.hp <= 2) return 2;
                return 1;
            }
        }
    }
}
```
</details>

2. 创建一个动态条件技能：
   - 根据场上形势变化
   - 包含AI判断
   - 实现条件缓存

<details>
<summary>参考答案 | 🟥 Hard</summary>

```javascript
"dynamic_condition": {
    // 缓存机制
    init(player){
        player.storage.dynamic_condition = {
            situation: null,
            lastUpdate: 0
        };
    },
    
    // 场势评估(自建函数)
    getSituation(player){
            let situation = 0;
            // 计算我方状态
            situation += player.hp;
            situation += player.countCards('h');
            
            // 计算敌方状态
            game.countPlayer(function(current){
                if(get.attitude(player, current) < 0){
                    situation -= current.hp;
                    situation -= current.countCards('h');
                }
            });
            
        return situation
    },
    
    enable: "phaseUse",
    filter(event, player){
        // 动态条件判断
        let situation = lib.skill.dynamic_condition.getSituation(player);
        
        if(situation > 0){
            // 优势时需要有手牌
            return player.countCards('h') > 0;
        } else {
            // 劣势时需要体力值大于1
            return player.hp > 1;
        }
    },
    async content(event, trigger, player){
        let situation = lib.skill.dynamic_condition.getSituation(player);
        
        if(situation > 0){
            // 优势时进攻
            let target = await player.chooseTarget('选择一名目标角色').forResult();
            if(target.bool){
                await target.targets[0].damage();
            }
        } else {
            // 劣势时防守
            await player.draw(2);
        }
    },
    ai: {
        order(item, player){
            let situation = lib.skill.dynamic_condition.getSituation(player);
            if(situation > 0) return 8;
            return 4;
        },
        result: {
            player(player){
                let situation = lib.skill.dynamic_condition.getSituation(player);
                if(situation > 0) return 1;
                return 2;
            }
        }
    }
}
```
</details>

3. 创建一个复杂mod技能：
   - 修改多个游戏数值
   - 包含多重条件判断
   - 实现动态修改

<details>
<summary>参考答案 | 🟥 Hard</summary>

```javascript
"complex_mod": {
    // 初始化
    init(player){
        player.storage.complex_mod = {
            attackRange: 0,
            maxHandcard: 0,
            cardUsable: 0
        };
    },
    
    // 更新数值(自建函数)
    updateMod(player){
        let storage = player.storage.complex_mod;
        
        // 根据体力值修改攻击距离
        storage.attackRange = Math.max(0, 3 - player.hp);
        
        // 根据手牌数修改手牌上限
        storage.maxHandcard = Math.floor(player.countCards('h') / 2);
        
        // 根据装备数修改出牌次数
        storage.cardUsable = player.countCards('e');
    },
    
    // 触发更新
    trigger: {
        player: ["changeHp", "gainAfter", "loseAfter", "equipAfter"]
    },
    forced: true,
    popup: false,
    filter(event, player){
        return true;
    },
    content(){
        lib.skill.complex_mod.updateMod(player);
    },
    
    // 数值修改
    mod: {
        attackRange(player, num){
            return num + player.storage.complex_mod.attackRange;
        },
        maxHandcard(player, num){
            return num + player.storage.complex_mod.maxHandcard;
        },
        cardUsable(card, player, num){
            if(card.name == 'sha'){
                return num + player.storage.complex_mod.cardUsable;
            }
        },
        globalTo(from, to, distance){
            // 特殊条件:被翻面时防御距离+1
            if(to.isTurnedOver()) return distance + 1;
        },
        targetEnabled(card, player, target){
            // 特殊条件:目标体力值大于自己时不能指定
            if(target.hp > player.hp && get.tag(card, 'damage')){
                return false;
            }
        },
        ignoredHandcard(card, player){
            // 特殊条件:红色手牌不计入手牌上限
            if(get.color(card) == 'red'){
                return true;
            }
        }
    },
    
    // 显示提示
    mark: true,
    intro: {
        content(storage, player){
            lib.skill.complex_mod.updateMod(player);
            let str = '当前效果:n';
            str += '攻击距离+' + storage.attackRange + 'n';
            str += '手牌上限+' + storage.maxHandcard + 'n';
            str += '出杀次数+' + storage.cardUsable;
            return str;
        }
    }
}
```
</details>
</br>