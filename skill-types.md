# 4.7 技能类型

## 1. 技能分类

### 1.1 基础技能类型
- 触发技: 在特定时机自动触发的技能
- 主动技: 出牌阶段主动使用的技能

### 1.2 特殊技能类型
- 锁定技: 符合条件必须发动的技能
- 持恒技: 不会因其他技能效果而失效的技能
- 转换技: 可在不同状态间切换的技能
- 限定技: 一局游戏只能使用一次的技能
- 觉醒技: 满足条件后永久转化的技能
- 使命技: 完成特定目标后获得奖励的技能
- 主公技: 身份为主公时才能使用的技能

### 1.3 新版技能类型
- 护甲技: 与护甲值相关的技能
- 阵法技: 需要特定队列才能发动的技能
- 蓄力技: 需要积累能量值的技能
- 势力技: 根据势力状态变化的技能
- 整肃技: 需要完成特定行动的技能
- 仁区技: 使用仁区牌的技能
- 智囊技: 使用智囊牌的技能
- 谋弈技: 需要进行博弈的技能
- 协力技: 需要其他角色配合的技能
- 追思技: 获得已阵亡角色技能的技能
- 议事技: 需要进行投票的技能

## 2. 技能示例

技能示例仅供参考,具体实现请参考源码！

### 2.1 基础技能类型示例

#### 触发技示例
<details>
<summary>展开示例</summary>

```javascript
// 曹操【奸雄】
"jianxiong": {
    audio: 2,
    trigger: {player: 'damageEnd'},
    forced: true,
    filter(event, player){
        return event.cards && event.cards.length > 0;
    },
    async content(event, trigger, player){
        await player.gain(trigger.cards);
        player.$gain2(trigger.cards);
    } // 锁定技,当你受到伤害后,你获得造成伤害的牌
}
```

</details>

#### 主动技示例
<details>
<summary>展开示例</summary>

```javascript
// 关羽【武圣】
"wusheng": {
    audio: 2,
    enable: ["chooseToUse", "chooseToRespond"],
    filterCard(card, player){
        return get.color(card) == 'red';
    },
    position: 'he',
    viewAs: {name: 'sha'},
    viewAsFilter(player){
        return player.countCards('he', {color: 'red'}) > 0;
    },
    prompt: "将一张红色牌当杀使用或打出",
} // 你可以将一张红色牌当【杀】使用或打出
```

</details>

### 2.2 特殊技能类型示例

#### 锁定技示例
<details>
<summary>展开示例</summary>

```javascript
// 马超【马术】
"mashu": {
    mod: {
        globalFrom(from, to, distance){
            return distance - 1;
        }
    } // 锁定技,你计算与其他角色的距离-1
}
```

</details>

#### 持恒技示例
<details>
<summary>展开示例</summary>

```javascript
// 曹髦【卫统】
mbweitong: {
	audio: 1,
	persevereSkill: true, // 持恒技
	zhuSkill: true, // 主公技
	trigger: {
		player: "mbqianlong_beginBegin",
	},
	forced: true,
	content() {},
	ai: {
		combo: "mbqianlong", // 联动技能
	},
}
```

</details>

#### 转换技示例
<details>
<summary>展开示例</summary>

```javascript
// 许攸【成略】
"nzry_chenglve": {
    mark: true,
    locked: false,
    zhuanhuanji: true, // 标记为转换技
    marktext: "☯", // 显示阴阳标记
    intro: {
        content(storage, player, skill) {
            // 技能描述会根据状态变化
            let str = player.storage.nzry_chenglve ? 
                "出牌阶段限一次，你可以摸两张牌，然后弃置一张手牌。若如此做，直到本回合结束，你使用与弃置牌花色相同的牌无距离和次数限制" : 
                "出牌阶段限一次，你可以摸一张牌，然后弃置两张手牌。若如此做，直到本回合结束，你使用与弃置牌花色相同的牌无距离和次数限制";
            
            // 显示当前可用花色
            if (player.storage.nzry_chenglve1) {
                str += "<br><li>当前花色：";
                str += get.translation(player.storage.nzry_chenglve1);
            }
            return str;
        },
    },
    enable: "phaseUse", // 出牌阶段使用
    usable: 1, // 每回合限一次
    audio: 2,
    async content(event, trigger, player) {
        let result;
        if (player.storage.nzry_chenglve == true) {
            // 阳:摸两张弃一张
            await player.draw(2);
            result = await player.chooseToDiscard("h", true).forResult();
        } else {
            // 阴:摸一张弃两张
            await player.draw();
            result = await player.chooseToDiscard("h", 2, true).forResult();
        }
        // 转换技能状态
        player.changeZhuanhuanji("nzry_chenglve");
        
        if (result.bool) {
            // 记录弃置牌的花色
            player.storage.nzry_chenglve1 = result.cards.map(
                card => get.suit(card, player)
            ).unique();
            // 添加临时效果
            player.addTempSkill("nzry_chenglve1");
        }
    },
    ai: {
        order: 2.7,
        result: {
            player(player) {
                if (!player.storage.nzry_chenglve && 
                    player.countCards("h") < 3) return 0;
                return 1;
            },
        },
    }
}, // 转换技,出牌阶段限一次,阴:摸一张弃两张;阳:摸两张弃一张,然后本回合使用与弃置牌花色相同的牌无距离和次数限制

// 成略的临时效果
"nzry_chenglve1": {
    charlotte: true,
    onremove: true,
    mod: {
        cardUsable(card, player) {
            const suit = get.suit(card);
            // 对应花色的牌无次数限制
            if (suit == "unsure" || 
                player.getStorage("nzry_chenglve1").includes(suit)) 
                return Infinity;
        },
        targetInRange(card, player) {
            const suit = get.suit(card);
            // 对应花色的牌无距离限制
            if (suit == "unsure" || 
                player.getStorage("nzry_chenglve1").includes(suit)) 
                return true;
        },
    },
}
```

</details>

#### 限定技示例
<details>
<summary>展开示例</summary>

```javascript
// 张飞【替身】
"retishen": {
    audio: 2,
    unique: true, // 独有技能
    mark: true, // 显示标记
    skillAnimation: true, // 播放技能动画
    animationColor: "soil", // 技能动画颜色
    limited: true, // 限定技
    trigger: {player: "phaseZhunbeiBegin"}, // 准备阶段触发
    
    // 初始化标记
    init(player){
        player.storage.retishen = false;
    },
    
    // 触发条件
    filter(event, player){
        if(player.storage.retishen) return false; // 已使用过则不能发动
        if(typeof player.storage.retishen2 == "number"){
            return player.hp < player.storage.retishen2; // 当前体力小于上回合体力
        }
        return false;
    },
    
    // AI判断
    check(event, player){
        if(player.hp <= 1) return true; // 濒死必发动
        return player.hp < player.storage.retishen2 - 1; // 体力差大于1时发动
    },
    
    // 技能效果
    content(){
        player.awakenSkill("retishen"); // 标记技能已发动
        // 回复体力并摸牌
        player.recover(player.storage.retishen2 - player.hp);
        player.draw(player.storage.retishen2 - player.hp);
        player.storage.retishen = true;
    },
    
    // 标记显示
    intro: {
        mark(dialog, content, player){
            if(player.storage.retishen) return;
            if(typeof player.storage.retishen2 != "number"){
                return "上回合体力：无";
            }
            return "上回合体力：" + player.storage.retishen2;
        },
        content: "limited",
    },
    
    group: ["retishen2"], // 关联技能组
}, // 限定技,准备阶段开始时,若你的体力值小于上回合结束时的体力值,你可以回复至上回合结束时的体力值并摸等量的牌

// 替身的记录效果
"retishen2": {
    trigger: {player: "phaseJieshuBegin"}, // 结束阶段触发
    priority: -10, // 优先级
    silent: true, // 不提示
    sourceSkill: "retishen", // 来源技能
    
    // 记录体力值
    content(){
        player.storage.retishen2 = player.hp;
        // 同步给其他玩家
        game.broadcast(function(player){
            player.storage.retishen2 = player.hp;
        }, player);
        // 记录录像
        game.addVideo("storage", player, ["retishen2", player.storage.retishen2]);
    },
    
    // 标记说明
    intro: {
        content(storage, player){
            if(player.storage.retishen) return;
            return "上回合体力：" + storage;
        },
    },
}
```

</details>

#### 觉醒技示例
<details>
<summary>展开示例</summary>

```javascript
// 孙策【魂姿】
"hunzi": {
    skillAnimation: true, // 播放技能动画
    animationColor: "wood", // 动画颜色
    audio: 2,
    juexingji: true, // 标记为觉醒技
    derivation: ["reyingzi", "gzyinghun"], // 派生技能(显示在技能描述中)
    unique: true, // 独有技能
    trigger: {player: "phaseZhunbeiBegin"}, // 准备阶段触发
    
    // 觉醒条件
    filter(event, player){
        return player.hp <= 1 && !player.storage.hunzi; // 体力值不大于1且未觉醒
    },
    
    forced: true, // 强制触发
    
    // 技能效果
    async content(event, trigger, player){
        player.awakenSkill(event.name); // 废除技能
        await player.loseMaxHp(); // 失去1点体力上限
        await player.addSkills(["reyingzi", "gzyinghun"]); // 获得英姿和英魂
    },
    
    // AI策略
    ai: {
        // 威胁度
        threaten(player, target){
            if(target.hp == 1) return 2; // 濒死时威胁最大
            return 0.5;
        },
        maixie: true, // 卖血标签
        effect: {
            target(card, player, target){
                if(!target.hasFriend()) return;
                // 体力值为2时,受到伤害的效果
                if(target.hp === 2 && 
                   get.tag(card, "damage") == 1 && 
                   !target.isTurnedOver() && 
                   _status.currentPhase !== target && 
                   get.distance(_status.currentPhase, target, "absolute") <= 3) 
                    return [0.5, 1];
                // 体力值为1时,回复体力的效果    
                if(target.hp === 1 && 
                   get.tag(card, "recover") && 
                   !target.isTurnedOver() && 
                   _status.currentPhase !== target && 
                   get.distance(_status.currentPhase, target, "absolute") <= 3) 
                    return [1, -3];
            },
        },
    },
} // 觉醒技,准备阶段开始时,若你的体力值不大于1,你失去1点体力上限,然后获得技能"英姿"和"英魂"
```

</details>

#### 使命技示例
<details>
<summary>展开示例</summary>

```javascript
// 王凌【密备】
"mibei": {
    audio: 2,
    trigger: {player: "useCardAfter"}, // 使用牌后触发
    dutySkill: true, // 标记为使命技
    forced: true, // 强制触发
    locked: false,
    
    // 使命完成条件
    filter(event, player){
        if(!player.storage.xingqi || !player.storage.xingqi.length) return false;
        // 检查各类型牌是否达到要求
        var map = {basic: 0, trick: 0, equip: 0};
        for(var i of player.storage.xingqi){
            var type = get.type(i);
            if(typeof map[type] == "number") map[type]++;
        }
        // 每种类型需要至少2张
        for(var i in map){
            if(map[i] < 2) return false;
        }
        return true;
    },
    
    logAudio: () => 1,
    skillAnimation: true, // 播放技能动画
    animationColor: "water", // 动画颜色
    
    // 使命完成效果
    content(){
        "step 0"
        player.awakenSkill("mibei"); // 标记完成
        game.log(player, "成功完成使命");
        // 获得三种类型的牌
        var list = ["basic", "equip", "trick"],
            cards = [];
        for(var i of list){
            var card = get.cardPile2(function(card){
                return get.type(card) == i;
            });
            if(card) cards.push(card);
        }
        if(cards.length) player.gain(cards, "gain2");
        "step 1"
        player.addSkills("xinmouli"); // 获得新技能
    },
    
    ai: {
        combo: "xingqi", // 配合星启技能
    },
    
    group: ["mibei_fail", "mibei_silent"], // 关联技能组
    derivation: "xinmouli", // 衍生技能
    
    subSkill: {
        // 沉默状态检测
        silent: {
            charlotte: true,
            trigger: {player: "phaseZhunbeiBegin"},
            silent: true,
            lastDo: true,
            filter(event, player){
                return !player.getStorage("xingqi").length;
            },
            content(){
                player.addTempSkill("mibei_mark");
            },
        },
        // 标记技能
        mark: {charlotte: true},
        // 使命失败效果
        fail: {
            audio: "mibei2.mp3",
            trigger: {player: "phaseJieshuBegin"},
            filter(event, player){
                return !player.getStorage("xingqi").length && 
                       player.hasSkill("mibei_mark");
            },
            forced: true,
            content(){
                game.log(player, "使命失败");
                player.awakenSkill("mibei");
                player.loseMaxHp();
            },
        },
    },
} // 使命技,当你使用的牌中包含至少两张基本牌、锦囊牌和装备牌后,你获得牌堆中的一张基本牌、锦囊牌和装备牌,然后获得技能"谋立";若你回合结束时未使用过牌,你失去1点体力上限
```

</details>

#### 主公技示例
<details>
<summary>展开示例</summary>

```javascript
// 曹操【护驾】
"hujia": {
    audio: 2,
    audioname: ["re_caocao"], // 音频配置
    unique: true, // 独有技能
    zhuSkill: true, // 标记为主公技
    
    // 触发时机:需要打出闪之前
    trigger: {player: ["chooseToRespondBefore", "chooseToUseBefore"]},
    
    // 触发条件
    filter(event, player){
        if(event.responded) return false; // 已响应则不触发
        if(player.storage.hujiaing) return false; // 正在询问中则不触发
        if(!player.hasZhuSkill("hujia")) return false; // 不是主公则不触发
        if(!event.filterCard({name: "shan", isCard: true}, player, event)) return false; // 不需要闪则不触发
        return game.hasPlayer(current => 
            current != player && current.group == "wei" // 场上有其他魏势力角色
        );
    },
    
    // AI判断
    check(event, player){
        if(get.damageEffect(player, event.player, player) >= 0) return false;
        return true;
    },
    
    // 技能效果
    async content(event, trigger, player){
        while(true){
            let bool;
            // 初始化询问目标
            if(!event.current) event.current = player.next;
            
            // 目标是自己则结束
            if(event.current == player) return;
            // 魏势力角色可以响应
            else if(event.current.group == "wei"){
                // 满足任一条件可以选择是否打出闪
                if((event.current == game.me && !_status.auto) || 
                   get.attitude(event.current, player) > 2 || 
                   event.current.isOnline()){
                    player.storage.hujiaing = true;
                    // 选择是否打出闪
                    const next = event.current.chooseToRespond(
                        "是否替" + get.translation(player) + "打出一张闪？",
                        {name: "shan"}
                    );
                    // AI策略
                    next.set("ai", () => {
                        const event = _status.event;
                        return get.attitude(event.player, event.source) - 2;
                    });
                    next.set("skillwarn", "替" + get.translation(player) + "打出一张闪");
                    next.autochoose = lib.filter.autoRespondShan;
                    next.set("source", player);
                    bool = await next.forResultBool();
                }
            }
            player.storage.hujiaing = false;
            
            // 有人打出闪则结束
            if(bool){
                trigger.result = {bool: true, card: {name: "shan", isCard: true}};
                trigger.responded = true;
                trigger.animate = false;
                // 更新AI显示度
                if(typeof event.current.ai.shown == "number" && 
                   event.current.ai.shown < 0.95){
                    event.current.ai.shown += 0.3;
                    if(event.current.ai.shown > 0.95) 
                        event.current.ai.shown = 0.95;
                }
                return;
            }
            // 否则询问下一名角色
            else{
                event.current = event.current.next;
            }
        }
    },
    
    // AI策略
    ai: {
        respondShan: true, // 可以响应闪
        skillTagFilter(player){
            if(player.storage.hujiaing) return false;
            if(!player.hasZhuSkill("hujia")) return false;
            return game.hasPlayer(current => 
                current != player && current.group == "wei"
            );
        },
    },
} // 主公技,当你需要使用或打出一张【闪】时,你可以令其他魏势力角色选择是否打出一张【闪】视为由你使用或打出
```

</details>

### 2.3 新版技能类型示例

#### 护甲技示例
<details>
<summary>展开示例</summary>

```javascript
// 谋于禁【节钺】
"sbjieyue": {
    audio: 4,
    trigger: {player: "phaseJieshuBegin"}, // 结束阶段触发
    direct: true, // 不触发询问
    content(){
        "step 0"
        // 选择目标
        player.chooseTarget(
            lib.filter.notMe, // 不能选择自己
            get.prompt("sbjieyue"), 
            "令一名其他角色获得1点护甲，然后该角色可以交给你一张牌。"
        ).set("ai", function(target){
            // AI策略:优先选择友方且护甲较少的角色
            return get.attitude(_status.event.player, target) / 
                   Math.sqrt(Math.min(1, target.hp + target.hujia));
        });
        
        "step 1"
        if(result.bool){
            var target = result.targets[0];
            event.target = target;
            player.logSkill("sbjieyue", target); // 记录技能发动
            target.changeHujia(1, null, true); // 目标获得1点护甲
            // 目标选择是否交牌
            target.chooseCard("he", 
                "是否交给" + get.translation(player) + "一张牌？"
            ).set("ai", card => 0.1 - get.value(card)); // AI尽量给价值低的牌
        }
        else event.finish();
        
        "step 2"
        if(result.bool){
            target.give(result.cards, player); // 交给发起者选择的牌
        }
    },
    
    // AI策略
    ai: {
        threaten: 2.7, // 较高的威胁度
        expose: 0.2, // 暴露度较低
    },
} // 结束阶段,你可以令一名其他角色获得1点护甲,然后其可以交给你一张牌
```

</details>

#### 阵法技示例
<details>
<summary>展开示例</summary>

```javascript
// 曹洪【鹤翼】
"heyi": {
    zhenfa: "inline", // 标记为阵法技,类型为直线阵法
    global: "heyi_distance", // 全局效果
}, // 阵法技,在同一直线上的角色防御距离+1

// 鹤翼的全局效果
"heyi_distance": {
    mod: {
        globalTo(from, to, distance){
            // 检查是否有拥有鹤翼的角色与目标在同一直线上
            if(game.hasPlayer(function(current){
                return current.hasSkill("heyi") && current.inline(to);
            })){
                return distance + 1; // 防御距离+1
            }
        },
    },
} // 若有拥有【鹤翼】的角色与你在同一直线上,则其他角色计算至你的距离+1
```

</details>

#### 蓄力技示例
<details>
<summary>展开示例</summary>

```javascript
// 谋赵云【龙胆】
"sblongdan": {
    audio: 2,
    enable: ["chooseToUse", "chooseToRespond"], // 可以使用或打出
    chargeSkill: 3, // 蓄力技,最大蓄力值为3
    
    // 发动条件
    filter(event, player){
        if(event.type == "wuxie" || !player.countCharge()) return false;
        var marked = player.hasSkill("sblongdan_mark", null, null, false);
        // 遍历所有基本牌
        for(var name of lib.inpile){
            if(!marked && name != "sha" && name != "shan") continue;
            if(get.type(name) != "basic") continue;
            if(player.hasCard(lib.skill.sblongdan.getFilter(name, player), "hs")){
                // 检查是否可以使用
                if(event.filterCard(get.autoViewAs({name}, "unsure"), player, event)) 
                    return true;
                // 检查是否可以使用属性杀
                if(marked && name == "sha"){
                    for(var nature of lib.inpile_nature){
                        if(event.filterCard(get.autoViewAs({name, nature}, "unsure"), 
                            player, event)) return true;
                    }
                }
            }
        }
        return false;
    },
    
    // 选择按钮
    chooseButton: {
        dialog(event, player){
            var list = [];
            var marked = player.hasSkill("sblongdan_mark", null, null, false);
            // 构建可选牌列表
            for(var name of lib.inpile){
                if(!marked && name != "sha" && name != "shan") continue;
                if(get.type(name) != "basic") continue;
                if(player.hasCard(lib.skill.sblongdan.getFilter(name, player), "hs")){
                    if(event.filterCard(get.autoViewAs({name}, "unsure"), player, event)) 
                        list.push(["基本", "", name]);
                    if(marked && name == "sha"){
                        for(var nature of lib.inpile_nature){
                            if(event.filterCard(get.autoViewAs({name, nature}, "unsure"), 
                                player, event)) 
                                list.push(["基本", "", name, nature]);
                        }
                    }
                }
            }
            return ui.create.dialog("龙胆", [list, "vcard"], "hidden");
        },
        
        // AI选择逻辑
        check(button){
            if(_status.event.getParent().type != "phase") return 1;
            var player = _status.event.player,
                card = {name: button.link[2], nature: button.link[3]};
            if(card.name == "jiu" && 
               Math.min(player.countMark("charge"), 
                       player.countCards("h", {type: "basic"})) < 2) return 0;
            return player.getUseValue(card, null, true);
        },
        
        // 选择后的处理
        backup(links, player){
            return {
                viewAs: {
                    name: links[0][2],
                    nature: links[0][3],
                },
                filterCard: lib.skill.sblongdan.getFilter(links[0][2], player),
                position: "he",
                popname: true,
                check(card){
                    return 6/Math.max(1, get.value(card));
                },
                precontent(){
                    player.removeCharge(); // 消耗蓄力值
                    player.addTempSkill("sblongdan_draw");
                },
            };
        },
        
        // 提示文本
        prompt(links, player){
            var marked = player.hasSkill("sblongdan_mark", null, null, false);
            var card = {
                name: links[0][2],
                nature: links[0][3],
                isCard: true,
            };
            if(marked) return "将一张基本牌当做" + get.translation(card) + "使用";
            return "将一张" + (card.name == "sha" ? "闪" : "杀") + 
                   "当做" + get.translation(card) + "使用";
        },
    },
    
    // 隐藏卡牌检测
    hiddenCard(player, name){
        if(get.type(name) != "basic" || !player.countCharge()) return false;
        var marked = player.hasSkill("sblongdan_mark", null, null, false);
        if(!marked && name != "sha" && name != "shan") return false;
        return player.hasCard(lib.skill.sblongdan.getFilter(name, player), "hs");
    },
    
    // AI策略
    ai: {
        respondSha: true,
        respondShan: true,
        skillTagFilter(player, tag){
            return lib.skill.sblongdan.hiddenCard(player, 
                tag == "respondSha" ? "sha" : "shan");
        },
        order: 9,
        result: {
            player(player){
                if(_status.event.dying) 
                    return get.attitude(player, _status.event.dying);
                return 1;
            },
        },
    },
    
    // 获取过滤器
    getFilter(name, player){
        if(!player.hasSkill("sblongdan_mark", null, null, false)){
            if(name == "sha") return {name: "shan"};
            if(name == "shan") return {name: "sha"};
            return () => false;
        }
        return {type: "basic"};
    },
    
    group: "sblongdan_charge", // 关联技能组
    derivation: "sblongdan_shabi", // 衍生技能
    
    // 移除技能时的处理
    onremove(player){
        player.removeSkill("sblongdan_mark");
    },
    
    // 子技能
    subSkill: {
        backup: {audio: "sblongdan"},
        mark: {charlotte: true},
        // 使用后摸牌
        draw: {
            charlotte: true,
            trigger: {player: ["useCardAfter"]},
            forced: true,
            popup: false,
            filter(event, player){
                return event.skill == "sblongdan_backup";
            },
            content(){
                player.draw();
            },
        },
        // 获得蓄力值
        charge: {
            audio: "sblongdan",
            trigger: {
                global: ["phaseBefore", "phaseEnd"],
                player: "enterGame",
            },
            forced: true,
            filter(event, player, name){
                if(!player.countCharge(true)) return false;
                return name != "phaseBefore" || game.phaseNumber == 0;
            },
            content(){
                player.addCharge();
            },
        },
    },
} // 蓄力技,你可以消耗1点蓄力值将一张基本牌当任意基本牌使用或打出(游戏开始和回合结束时获得1点蓄力值)
```

</details>

#### 势力技示例
<details>
<summary>展开示例</summary>

```javascript
// 魏势力技能【追锋】
"dbzhuifeng": {
    audio: 2,
    groupSkill: "wei", // 魏势力专属
    enable: "chooseToUse",
    usable: 2,
    viewAsFilter(player){
        return player.group == "wei" && player.hp > 0;
    },
    viewAs: {name: "juedou", isCard: true},
    filterCard: () => false,
    selectCard: -1,
    log: false,
    precontent(){
        "step 0"
        player.logSkill("dbzhuifeng");
        player.loseHp();
        event.forceDie = true;
        "step 1"
        if(player.isDead()){
            player.useResult(event.result, event.getParent()).forceDie = true;
        }
    },
    group: "dbzhuifeng_self",
    subSkill: {
        self: {
            trigger: {player: "damageBegin2"},
            forced: true,
            filter(event, player){
                var evt = event.getParent();
                return evt.skill == "dbzhuifeng" && evt.player == player;
            },
            content(){
                trigger.cancel();
                player.tempBanSkill("dbzhuifeng", {player: "phaseUseEnd"});
            }
        }
    }
}, // 魏势力技,你可以失去1点体力视为使用一张【决斗】,此技能每回合限用两次。若你因此受到伤害,则防止之并令此技能失效直到回合结束

// 吴势力技能【冲坚】
"dbchongjian": {
    audio: 2,
    groupSkill: "wu", // 吴势力专属
    hiddenCard(player, name){
        if(player.group == "wu" && 
           (name == "sha" || name == "jiu") &&
           player.hasCard(function(card){
               return get.type(card) == "equip";
           }, "hes")) return true;
        return false;
    },
    enable: "chooseToUse",
    filter(event, player){
        return player.group == "wu" &&
               player.hasCard(function(card){
                   return get.type(card) == "equip";
               }, "hes") &&
               (event.filterCard({name: "sha"}, player, event) || 
                event.filterCard({name: "jiu"}, player, event));
    },
    locked: false,
    mod: {
        targetInRange(card){
            if(card.storage && card.storage.dbchongjian) return true;
        }
    },
    chooseButton: {
        dialog(){
            var list = [];
            list.push(["基本","","sha"]);
            for(var i of lib.inpile_nature) list.push(["基本","","sha",i]);
            list.push(["基本","","jiu"]);
            return ui.create.dialog("冲坚",[list,"vcard"]);
        },
        filter(button, player){
            var evt = _status.event.getParent();
            return evt.filterCard({
                name: button.link[2],
                nature: button.link[3],
                isCard: true
            }, player, evt);
        },
        backup(links, player){
            return {
                audio: "dbchongjian",
                viewAs: {
                    name: links[0][2],
                    nature: links[0][3],
                    storage: {dbchongjian: true}
                },
                filterCard: {type: "equip"},
                position: "hes",
                popname: true,
                precontent(){
                    player.addTempSkill("dbchongjian_effect");
                }
            };
        },
        prompt(links){
            return '将一张装备牌当做'+(links[0][3]?get.translation(links[0][3]):'')+
                   '【'+get.translation(links[0][2])+'】使用';
        }
    },
    subSkill: {
        effect: {
            charlotte: true,
            mod: {
                targetInRange(card){
                    if(card.storage && card.storage.dbchongjian) return true;
                }
            },
            trigger: {source: "damageSource"},
            forced: true,
            logTarget: "player",
            filter(event, player){
                return event.parent.skill == "dbchongjian_backup" && 
                       event.card.name == "sha" && 
                       event.getParent().name == "sha" && 
                       event.player.countGainableCards(player, "e") > 0;
            },
            content(){
                player.gainPlayerCard(trigger.player, "e", true, trigger.num);
            }
        }
    }
} // 吴势力技,你可以将一张装备牌当做【杀】或【酒】使用,以此法使用的牌无距离限制。若以此法使用的【杀】造成伤害,你获得目标装备区里的牌
```

</details>

#### 整肃技示例
<details>
<summary>展开示例</summary>

```javascript
// 朱儁【厚俸】
"houfeng": {
    audio: 3,
    trigger: {global: "phaseUseBegin"},
    // 触发条件:目标角色在你的攻击范围内且未拥有全部整肃技能
    filter(event, player){
        if(!["zhengsu_leijin", "zhengsu_bianzhen", "zhengsu_mingzhi"]
            .some(i => !event.player.hasSkill(i))) return false;
        return player.inRange(event.player);
    },
    round: 1, // 每轮限一次
    logAudio: () => 1,
    logTarget: "player",
    content(){
        "step 0"
        // 选择整肃类型
        player.chooseButton([
            "选择"+get.translation(trigger.player)+"要进行的整肃类型",
            [["zhengsu_leijin","zhengsu_bianzhen","zhengsu_mingzhi"]
                .filter(i => !trigger.player.hasSkill(i)), "vcard"]
        ], true);
        "step 1"
        if(result.bool){
            var name = result.links[0][2],
                target = trigger.player;
            // 添加共享效果
            target.addTempSkill("houfeng_share", {
                player: ["phaseDiscardAfter","phaseAfter"]
            });
            target.markAuto("houfeng_share", [[player, name]]);
            // 添加整肃技能
            target.addTempSkill(name, {
                player: ["phaseDiscardAfter","phaseAfter"]
            });
            target.markAuto("houfeng", name);
            target.popup(name, "thunder");
            game.delayx();
        }
    },
    subSkill: {
        share: {
            audio: "houfeng",
            charlotte: true,
            onremove: ["houfeng","houfeng_share"],
            trigger: {player: "phaseDiscardEnd"},
            forced: true,
            // 获取整肃类型
            getIndex(event, player){
                return player.getStorage("houfeng");
            },
            // 音效处理
            logAudio(event, player, _3, data){
                if(!player.storage[data]) return "houfeng2.mp3";
                return "houfeng2.mp3";
            },
            content(){
                "step 0"
                player.unmarkAuto("houfeng", event.indexedData);
                // 整肃失败
                if(!player.storage[event.indexedData]){
                    player.popup("整肃失败", "fire");
                    game.log(player, "整肃失败");
                    event.finish();
                    return;
                }
                // 整肃成功
                player.popup("整肃成功", "wood");
                game.log(player, "整肃成功");
                // 获取可获得奖励的角色
                var list = player.getStorage("houfeng_share")
                    .filter(i => i[1] == event.indexedData && i[0].isIn())
                    .map(i => i[0]);
                list.unshift(player);
                event.list = list;
                // 选择奖励
                if(list.some(i => i.isDamaged())){
                    trigger.player.chooseControl("摸两张牌","回复体力")
                        .set("prompt", "整肃奖励：请选择"+
                            get.translation(list)+"的整肃奖励");
                }
                else event._result = {control: "摸两张牌"};
                "step 1"
                if(result.control != "cancel2"){
                    // 执行奖励效果
                    if(result.control == "摸两张牌") 
                        game.asyncDraw(event.list, 2);
                    else{
                        for(var i of event.list) i.recover();
                    }
                }
                else event.finish();
                "step 2"
                game.delayx();
            }
        }
    }
} // 整肃技,一名角色的出牌阶段开始时,若其在你的攻击范围内,你可以令其获得一个其未获得过的整肃技能直到弃牌阶段结束。若其整肃成功,则你与其可以选择摸两张牌或回复1点体力
```

</details>

#### 仁区技示例
<details>
<summary>展开示例</summary>

```javascript
// 张仲景【病论】
"binglun": {
    audio: 2,
    enable: "phaseUse",
    usable: 1,
    // 检查仁库是否有牌
    filter(event, player){
        return _status.renku.length > 0;
    },
    // 选择仁库牌
    chooseButton: {
        dialog(event, player){
            return ui.create.dialog("病论", _status.renku);
        },
        backup(links, player){
            var obj = lib.skill.binglun_backup;
            obj.card = links[0];
            return obj;
        },
        prompt: () => "请选择【病论】的目标"
    },
    subSkill: {
        backup: {
            audio: "binglun",
            filterCard: () => false,
            selectCard: -1,
            filterTarget: true,
            delay: false,
            content(){
                "step 0"
                // 将选中的仁库牌置入弃牌堆
                var card = lib.skill.binglun_backup.card;
                game.log(card, "从仁库进入了弃牌堆");
                player.$throw(card, 1000);
                game.delayx();
                game.cardsDiscard(card).fromRenku = true;
                _status.renku.remove(card);
                game.updateRenku();
                "step 1"
                // 目标选择效果
                target.chooseControl()
                    .set("choiceList", [
                        "摸一张牌",
                        "于自己的下回合结束后回复1点体力"
                    ]);
                "step 2"
                // 执行选择的效果
                if(result.index == 0) target.draw();
                else{
                    target.addSkill("binglun_recover");
                    target.addMark("binglun_recover", 1, false);
                }
            }
        },
        // 延迟回复效果
        recover: {
            trigger: {player: "phaseEnd"},
            forced: true,
            popup: false,
            onremove: true,
            charlotte: true,
            content(){
                if(player.isDamaged()){
                    player.logSkill("binglun_recover");
                    player.recover(player.countMark("binglun_recover"));
                }
                player.removeSkill("binglun_recover");
            },
            intro: {
                content: "下回合结束时回复#点体力"
            }
        }
    }
} // 出牌阶段限一次,你可以选择仁库中的一张牌置入弃牌堆,然后令一名角色选择:1.摸一张牌;2.于其下回合结束后回复1点体力
```

</details>

#### 智囊技示例
<details>
<summary>展开示例</summary>

```javascript
// 神荀彧【灵策】
"lingce": {
    audio: 2,
    // 初始化全局技能
    init: player => {
        game.addGlobalSkill("lingce_global");
    },
    trigger: {global: "useCard"},
    forced: true,
    // 触发条件:使用智囊牌或定汉记录的牌
    filter(event, player){
        if(!event.card.isCard || !event.cards || 
           event.cards.length !== 1) return false;
        return event.card.name == "qizhengxiangsheng" || 
               get.zhinangs().includes(event.card.name) || 
               player.getStorage("dinghan").includes(event.card.name);
    },
    // 技能效果:摸一张牌
    content(){
        player.draw();
    },
    // 全局子技能
    subSkill: {
        global: {
            // 移除全局技能的处理
            onremove(){
                game.removeGlobalSkill("lingce_global");
            }
        }
    }
} // 锁定技,当一名角色使用智囊牌时,你摸一张牌
```

</details>

#### 谋弈技示例
<details>
<summary>展开示例</summary>

```javascript
// 谋马超【铁骑】
"sbtieji": {
    audio: 4,
    trigger: {player: "useCardToPlayered"},
    logTarget: "target",
    logAudio: () => 1,
    // 触发条件:使用【杀】指定其他角色为目标
    filter(event, player){
        return player != event.target && 
               event.card.name == "sha" && 
               event.target.isIn();
    },
    content(){
        "step 0"
        var target = trigger.target;
        event.target = target;
        // 封印目标技能
        target.addTempSkill("fengyin");
        // 此【杀】不可被响应
        trigger.directHit.add(target);
        // 进行谋弈
        player.chooseToDuiben(target)
            .set("title", "谋弈")
            .set("namelist", [
                "出阵迎战", 
                "拱卫中军", 
                "直取敌营", 
                "扰阵疲敌"
            ])
            .set("translationList", [
                `以防止${get.translation(player)}摸2张牌`,
                `以防止${get.translation(player)}获得你1张牌`,
                `若成功，你获得${get.translation(target)}1张牌`,
                `若成功，你摸2张牌`
            ]);
        "step 1"
        if(result.bool){
            // 根据谋弈结果执行效果
            if(result.player == "db_def1") 
                player.gainPlayerCard(target, "he", true);
            else player.draw(2);
        }
    },
    // 音效子技能
    subSkill: {
        true1: {
            audio: "sbtieji",
            logAudio: () => "sbtieji2.mp3"
        },
        true2: {
            audio: "sbtieji", 
            logAudio: () => "sbtieji2.mp3"
        },
        false: {
            audio: "sbtieji",
            logAudio: () => "sbtieji4.mp3"
        }
    }
} // 当你使用【杀】指定其他角色为目标时,你可以与其进行谋弈。若你赢,你可以获得其一张牌或摸两张牌;无论谁赢,其本回合技能失效且不能响应此【杀】
```

</details>

#### 协力技示例
<details>
<summary>展开示例</summary>

```javascript
// 谋张飞【协击】
"sbxieji": {
    audio: 3,
    trigger: {player: "phaseZhunbeiBegin"},
    logAudio: () => 2,
    // 选择协力目标
    async cost(event, trigger, player){
        event.result = await player.chooseTarget(
            lib.filter.notMe, 
            get.prompt("sbxieji"), 
            "和一名其他角色进行"协力""
        ).forResult();
    },
    // 设置协力条件
    async content(event, trigger, player){
        const target = event.targets[0];
        // 添加临时技能
        player.addAdditionalSkill("cooperation", "sbxieji_effect");
        // 选择协力条件
        await player.chooseCooperationFor(target, "sbxieji");
        await game.delayx();
    },
    subSkill: {
        // 协力成功效果
        effect: {
            audio: "sbxieji2.mp3",
            charlotte: true,
            trigger: {global: "phaseJieshuBegin"},
            direct: true,
            // 检查协力是否完成
            filter(event, player){
                return player.checkCooperationStatus(event.player, "sbxieji");
            },
            content(){
                "step 0"
                game.log(player, "和", trigger.player, "的协力成功");
                // 选择【杀】的目标
                player.chooseTarget(
                    "协击：请选择【杀】的目标",
                    "你和"+get.translation(trigger.player)+
                    "协力成功，可以视为对至多三名其他角色使用一张【杀】，"+
                    "且此【杀】造成伤害时，你摸等同于伤害值的牌",
                    [1,3], true,
                    function(card, player, target){
                        return player.canUse("sha", target, false);
                    }
                );
                "step 1"
                if(result.bool){
                    player.addTempSkill("sbxieji_reward", "sbxieji_effectAfter");
                    player.useCard({
                        name: "sha",
                        isCard: true,
                        storage: {sbxieji: true}
                    }, "sbxieji_effect", result.targets);
                }
            }
        },
        // 伤害奖励
        reward: {
            charlotte: true,
            trigger: {source: "damageSource"},
            forced: true,
            popup: false,
            filter(event, player){
                return event.card && event.card.storage && 
                       event.card.storage.sbxieji && 
                       event.getParent().type == "card";
            },
            content(){
                player.draw(trigger.num);
            }
        }
    }
} // 准备阶段,你可以选择一名其他角色进行协力。若协力成功,你可以视为对至多三名角色使用一张【杀】(此【杀】造成伤害后你摸等量的牌)
```

</details>

#### 议事技示例
<details>
<summary>展开示例</summary>

```javascript
// 起刘宏【朝争】
"jsrgchaozheng": {
    audio: 4,
    trigger: {player: "phaseZhunbeiBegin"},
    // 获取议事目标
    logTarget(event, player){
        return game.filterPlayer(i => i != player);
    },
    prompt: "是否发动【朝争】？",
    // 音效处理
    logAudio: index => (typeof index === "number" ? 
        "jsrgchaozheng"+index+".mp3" : 2),
    // 发起议事
    content(){
        player.chooseToDebate(
            game.filterPlayer(i => i != player)
        ).set("callback", lib.skill.jsrgchaozheng.callback);
    },
    // 议事结果处理
    callback(){
        var result = event.debateResult;
        if(result.bool && result.opinion){
            var opinion = result.opinion,
                targets = result.red.map(i => i[0]);
            targets.sortBySeat();
            // 执行议事效果
            if(opinion && ["red","black"].includes(opinion)){
                player.logSkill("jsrgchaozheng", targets, null, null, 
                    [opinion == "red" ? 3 : 4]);
                targets.forEach(i => 
                    i[opinion == "red" ? "recover" : "loseHp"]()
                );
            }
            // 检查是否达成一致意见
            if(result.opinions.some(idea => {
                return result.targets.every(target => {
                    return result[idea].slice()
                        .map(i => i[0])
                        .includes(target);
                });
            }))
                player.draw(result.targets.length);
        }
    }
} // 准备阶段开始时,你可以令所有其他角色进行议事。若议事结果为红色方,则红色方回复1点体力;若为黑色方,则黑色方失去1点体力。若所有角色意见一致,你摸X张牌(X为参与议事的角色数)
```

</details>

## 4. 技能翻译

技能翻译包括:
1. 技能名称
2. 技能描述
2. 技能标签

<details>
<summary>展开示例</summary>

```javascript
translate: {
    "jianxiong": "奸雄",
    "jianxiong_info": "锁定技,当你受到伤害后,你获得造成伤害的牌。",
    
    "longdan": "龙胆",
    "longdan_info": "你可以将一张【闪】当【杀】使用或打出。",
    
    "luoshen": "洛神",
    "luoshen_info": "准备阶段开始时,你可以进行判定,若结果为黑色,获得此牌,你可以重复此流程。"
}
```

</details>

## 练习题

1. 创建一个触发技:
   - 在回合开始时触发
   - 可以选择摸牌或回复体力
   - 每回合限一次

<details>
<summary>参考答案 | 🟩 Easy</summary>

```javascript
"ex_trigger": {
    // 触发时机：回合开始时
    trigger: {player: 'phaseBegin'},
    
    // 发动条件：每回合限一次
    filter(event, player){
        return !player.hasSkill('ex_trigger_used');
    },
    
    // 技能效果
    async content(event, trigger, player){
        // 选择效果
        let choice = await player.chooseControl('摸两张牌', '回复1点体力')
            .set('prompt', '请选择一个效果')
            .set('ai', function(){
                // AI策略：血量低于2优先回复体力
                if(player.hp <= 2) return '回复1点体力';
                return '摸两张牌';
            })
            .forResult();
            
        // 执行效果    
        if(choice.control === '摸两张牌'){
            await player.draw(2);
        } else {
            await player.recover();
        }
        
        // 添加已使用标记
        player.addTempSkill('ex_trigger_used', 'phaseAfter');
    }
} // 回合开始时,你可以选择:1.摸两张牌;2.回复1点体力。每回合限一次。
```

</details>
</details>

2. 创建一个视为技:
   - 可以将一张红色牌当做【桃】使用
   - 可以将一张黑色牌当做【无懈可击】使用
   - 每阶段各限一次

<details>
<summary>参考答案 | 🟨 Medium</summary>

```javascript
"ex_viewas": {
    // 主动使用和响应时可发动
    enable: ["chooseToUse", "chooseToRespond"],
    
    // 使用条件检查
    filter(event, player){
        // 检查是否有可用牌
        if(!player.countCards('he')) return false;
        // 检查使用次数
        if(player.hasSkill('ex_viewas_tao') && 
           player.hasSkill('ex_viewas_wuxie')) return false;
        
        // 检查是否可以使用目标牌
        if(!player.hasSkill('ex_viewas_tao') && 
           event.filterCard({name:'tao'}, player, event)) return true;
        if(!player.hasSkill('ex_viewas_wuxie') && 
           event.filterCard({name:'wuxie'}, player, event)) return true;
        return false;
    },
    
    // 选择按钮
    chooseButton: {
        dialog(event, player){
            var list = [];
            // 添加可选牌型
            if(!player.hasSkill('ex_viewas_tao'))
                list.push(['基本','','tao']);
            if(!player.hasSkill('ex_viewas_wuxie'))
                list.push(['锦囊','','wuxie']);
            return ui.create.dialog('博识',[list,'vcard']);
        },
        
        // 检查按钮是否可选
        filter(button, player){
            var evt = _status.event.getParent();
            return evt.filterCard({
                name: button.link[2]
            }, player, evt);
        },
        
        // 选择后的处理
        backup(links, player){
            return {
                // 过滤可选牌
                filterCard(card){
                    if(links[0][2] == 'tao')
                        return get.color(card) == 'red';
                    return get.color(card) == 'black';
                },
                position: 'he',
                // 转化为目标牌
                viewAs: {name: links[0][2]},
                // 记录使用次数
                onuse(result, player){
                    player.addTempSkill('ex_viewas_'+links[0][2], 'phaseAfter');
                }
            }
        },
        
        prompt(links){
            var color = links[0][2] == 'tao' ? '红色' : '黑色';
            return '将一张'+color+'牌当做'+get.translation(links[0][2])+'使用';
        }
    },
    
    // AI策略
    ai:{
        order:function(item,player){
            if(item.viewAs.name == 'tao') return 8;
            return 4;
        },
        result:{
            player:1
        }
    }
} // 你可以将一张红色牌当【桃】使用,或将一张黑色牌当【无懈可击】使用。每种牌每阶段限一次。
```

</details>
</details>

2. 创建一个复合技能:
   - 锁定技，回合开始时获得一个标记
   - 出牌阶段限一次，可以移去一个标记令一名角色选择:
     1. 弃置一张牌，然后摸两张牌
     2. 失去1点体力，然后获得一个技能直到回合结束
   - 回合结束时，若你的标记数大于2，则失去所有标记并受到1点伤害

<details>
<summary>参考答案 | 🟨 Medium</summary>

```javascript
"ex_complex": {
    // 初始化标记
    init(player){
        if(!player.storage.ex_complex) player.storage.ex_complex = 0;
    },
    
    // 标记显示
    mark: true,
    marktext: "☯",
    intro: {
        name: '修炼',
        content: '当前有#个标记'
    },
    
    // 获得标记
    trigger: {player: 'phaseBegin'},
    forced: true,
    content(){
        player.addMark('ex_complex', 1);
    },
    
    // 主动使用部分
    group: ['ex_complex_use', 'ex_complex_damage'],
    
    subSkill: {
        // 主动使用效果
        use: {
            enable: 'phaseUse',
            usable: 1,
            filter(event, player){
                return player.countMark('ex_complex') > 0;
            },
            async content(event, trigger, player){
                // 移去标记
                player.removeMark('ex_complex', 1);
                
                // 选择目标
                let target = await player.chooseTarget(
                    '请选择【修炼】的目标',
                    true
                ).set('ai', function(target){
                    // AI策略：优先选择友方
                    return get.attitude(_status.event.player, target);
                })
                .forResult();
                
                if(target.bool){
                    event.target = target.targets[0];
                } else {
                    event.finish();
                    return;
                }
                
                // 目标选择效果
                let choice = await event.target.chooseControl(
                    '弃置摸牌',
                    '失去体力获得技能'
                ).set('prompt', '请选择一项')
                .set('ai', function(){
                    // AI策略：血量高于2时倾向于失去体力
                    if(event.target.hp > 2) return '失去体力获得技能';
                    return '弃置摸牌';
                })
                .forResult();
                
                if(choice.control === '弃置摸牌'){
                    // 弃置一张牌并摸两张
                    await event.target.chooseToDiscard(1, 'he', true);
                    await event.target.draw(2);
                } else {
                    // 失去体力并获得技能
                    await event.target.loseHp();
                    event.target.addTempSkill('ex_complex_buff', 'phaseEnd');
                }
            },
            ai: {
                order: 7,
                result: {
                    target: 1
                }
            }
        },
        
        // 回合结束检查
        damage: {
            trigger: {player: 'phaseEnd'},
            forced: true,
            filter(event, player){
                return player.countMark('ex_complex') > 2;
            },
            content(){
                player.clearMark('ex_complex');
                player.damage();
            }
        },
        
        // 临时获得的技能
        buff: {
            mark: true,
            intro: {
                content: '攻击范围+1，出牌阶段可以多使用一张【杀】'
            },
            mod: {
                attackRange(player, num){
                    return num + 1;
                },
                cardUsable(card, player, num){
                    if(card.name == 'sha') return num + 1;
                }
            }
        }
    }
} // 锁定技，回合开始时获得一个"修炼"标记。出牌阶段限一次，你可以移去一个标记并令一名角色选择：1.弃置一张牌，然后摸两张牌；2.失去1点体力，然后获得技能直到回合结束。回合结束时，若你的标记数大于2，则失去所有标记并受到1点伤害。
```

</details>
</details>
</br>
下一章我们学习创建卡牌。
