# 4.2 触发时机

## 1. 触发时机概述

触发时机是技能发动的关键时间点，主要分为：
- [阶段类触发](#阶段类)
- [事件类触发](#事件类)
- [全局类触发](#全局类)
- [主动类触发](#主动类)

<details>
<summary>触发器列表</summary>

```javascript
    // 带有 ？ 即为特殊模式触发事件。
    // 事件细分，可细分事件必须携带此类后缀
   "${EventWithTrigger}Before"                 // 事件发生前
   "${EventWithTrigger}Begin"                  // 事件开始时
   "${EventWithTrigger}End"                    // 事件结束时
   "${EventWithTrigger}After"                  // 事件发生后
   "${EventWithTrigger}Skipped"                // 事件被跳过时
   "${EventWithTrigger}Canceled"               // 事件被取消时
    // 可细分事件
   "${_CardName}"                            // 卡牌使用时
   "${_CardName}Cancel"                      // 卡牌取消时
   "${_CardName}ContentAfter"                // 卡牌执行后
   "${_CardName}ContentBefore"               // 卡牌执行前
   "[skillname]"                             // 技能使用时
   "[skillname]ContentAfter"                 // 技能执行后
   "[skillname]ContentBefore"                // 技能执行前
   "[skillname]_cost"                        // 技能执行cost(选择)时
   "addFellowAuto"                           // 自动添加随从时？
   "addJudge"                                // 添加判定牌时
   "addToExpansion"                          // 置于武将牌上时
   "boss_jingjia"                            //  boss 执行精甲时？
   "callSubPlayer"                           // 切换至随从时
   "caochuan_gain"                           // 草船借箭获得牌时
   "cardsDiscard"                            // 卡牌弃置时
   "cardsGotoOrdering"                       // 卡牌进入处理区时
   "cardsGotoPile"                           // 卡牌进入牌堆时
   "cardsGotoSpecial"                        // 卡牌进入特殊区时
   "carryOutJunling"                         // 执行军令时？
   "changeCharacter"                         // 修改角色时
   "changeGroup"                             // 修改势力时
   "changeHp"                                // 修改血量时
   "changeHujia"                             // 修改护甲时
   "changeVice"                              // 修改副将时
   "changeSkills"                            // 修改技能时
   "chessMech"                               // 塔防放置时？
   "chessMechRemove"                         // 塔防移除时？
   "chooseBool"                              // 选择是否时
   "chooseButton"                            // 选择按钮时
   "chooseButtonOL"                          // 联机选择按钮时
   "chooseCard"                              // 选择卡牌时
   "chooseCardOL"                            // 联机选择卡牌时
   "chooseCardTarget"                        // 选择卡牌目标时
   "chooseCharacter"                         // 选择角色时
   "chooseCharacterOL"                       // 联机选择角色时
   "chooseControl"                           // 选择选项时
   "chooseCooperationFor"                    // 选择协力时
   "chooseJunlingControl"                    // 选择军令选项时？
   "chooseJunlingFor"                        // 选择军令时？
   "choosePlayerCard"                        // 选择玩家卡牌时
   "chooseSkill"                             // 选择技能时
   "chooseTarget"                            // 选择目标时
   "chooseToCompare"                         // 选择拼点时
   "chooseToDebate"                          // 选择议事时
   "chooseToDisable"                         // 选择废弃装备时
   "chooseToDiscard"                         // 选择弃置时
   "chooseToDuiben"                          // 选择谋弈时
   "chooseToEnable"                          // 选择恢复装备时
   "chooseToGive"                            // 选择给予时
   "chooseToGuanxing"                        // 选择卜算时
   "chooseToMove"                            // 选择移动牌时
   "chooseToMoveChess"                       // 选择移动塔防时？
   "chooseToMove_new"                        // 选择移动_新时（有什么区别？暂不清楚）
   "chooseToPSS"                             // 选择猜拳时
   "chooseToPlayBeatmap"                     // 选择播放音谱时
   "chooseToRespond"                         // 选择响应时
   "chooseToUse"                             // 选择使用时
   "chooseUseTarget"                         // 选择使用目标时
   "compareMultiple"                         // 范围拼点时
   "damage"                                  // 伤害时
   "die"                                     // 死亡时
   "disableEquip"                            // 禁用装备栏时
   "disableJudge"                            // 禁用判定区时
   "discard"                                 // 弃置时
   "discardPlayerCard"                       // 弃置玩家卡牌时
   "discoverCard"                            // 发现卡牌时
   "doubleDraw"                              // 额外抽牌时？
   "draw"                                    // 摸牌时
   "dying"                                   // 濒死时
   "enableEquip"                             // 恢复装备栏时
   "enableJudge"                             // 恢复判定区时
   "equip"                                   // 使用装备时
   "equip_${_CardName}"                      // 使用指定装备时
   "executeDelayCardEffect"                  // 执行延迟锦囊牌效果时
   "exitSubPlayer"                           // 移除随从时
   "expandEquip"                             // 扩展装备区时
   "finish_game"                             // 游戏结束时
   "gain"                                    // 获得牌时
   "gainMaxHp"                               // 获得最大血量时
   "gainPlayerCard"                          // 获得玩家卡牌时
   "game"                                    // 游戏开始时
   "gameDraw"                                // 开局摸牌时
   "gift"                                    // 给予赠物时
   "guozhanDraw"                             // 国战开局摸牌时?
   "gzzhenxi_use"                            // 国战震袭使用时?
   "hideCharacter"                           // 隐藏角色时
   "judge"                                   // 判定时
   "link"                                    // 横置时
   "loadMap"                                 // 加载地图时?
   "loadPackage"                             // 加载扩展包时
   "lose"                                    // 失去牌时
   "loseAsync"                               // 异步失去牌时
   "loseHp"                                  // 失去血量时
   "loseMaxHp"                               // 失去体力上限时
   "loseToDiscardpile"                       // 失去到弃牌堆时
   "lose_${_CardName}"                       // 指定卡牌失去时
   "lose_[VEquip.name]"                      // 指定装备失去时
   "mayChangeVice"                           // 可以更换副将时?
   "moveCard"                                // 移动卡牌时
   "nvzhuang_lose"                           // 女装失去时
   "phaseDiscard"                            // 弃牌阶段时
   "phaseDraw"                               // 摸牌阶段时
   "phaseJieshu"                             // 结束阶段时
   "phaseJudge"                              // 判定阶段时
   "phaseLoop"                               // 阶段流转时
   "phaseZhunbei"                            // 准备阶段时
   "pre_[event.wuxieresult2]"                // 事件结果预处理时
   "pre_[skillname]"                         // 技能预处理时
   "qinglong_guozhan"                        // 国战使用青龙刀时
   "recast"                                  // 重铸时
   "recover"                                 // 回血时
   "removeCharacter"                         // 移除角色时
   "replaceChessPlayer"                      // 替换塔防角色时?
   "replaceEquip"                            // 替换装备时
   "replaceHandcards"                        // 替换手牌时
   "replacePlayer"                           // 替换角色时
   "respond"                                 // 响应时
   "showCards"                               // 展示卡牌时
   "showHandcards"                           // 展示手牌时
   "stratagemCamouflage"                     // 计谋伪装时
   "stratagemInsight"                        // 计谋洞察时
   "swapEquip"                               // 交换装备时
   "toggleSubPlayer"                         // 更换随从时
   "turnOver"                                // 翻面时
   "useCard"                                 // 使用卡牌时
   "useSkill"                                // 使用技能时
   "versusDraw"                              // 对战摸牌时
   "viewCards"                               // 查看卡牌时
   "viewCharacter"                           // 查看角色时
   "yingbianEffect"                          // 应变效果时
   "yingbianZhuzhan"                         // 应变助战时
   "zhuque_clear"                            // 朱雀扇执行时

    // 不可细分事件
   "addShownCardsAfter"                      // 添加展示卡牌之后
   "addToExpansionBefore"                    // 置于武将牌上之前
   "boss_baonuwash"                          // boss 暴怒清洗时？
   "compare"                                 // 拼点时
   "compareCardShowBefore"                   // 拼点牌展示前
   "compareFixing"                           // 拼点修正时
   "damageBegin1"                            // 伤害开始阶段1
   "damageBegin2"                            // 伤害开始阶段2
   "damageBegin3"                            // 伤害开始阶段3
   "damageBegin4"                            // 伤害开始阶段4
   "damageSource"                            // 伤害来源确定时
   "damageZero"                              // 伤害为零时
   "debateShowOpinion"                       // 议事展示意见时
   "enterGame"                               // 进入游戏时
   "eventNeutralized"                        // 事件被抵消时
   "fellow"                                  // 获得随从时
   "gameStart"                               // 游戏开始相关
   "giftAccept"                              // 赠礼接受时
   "giftAccepted"                            // 赠礼接受后
   "giftDenied"                              // 赠礼拒绝时
   "giftDeny"                                // 赠礼拒绝后
   "hideShownCardsAfter"                     // 隐藏展示牌之后
   "jiananUpdate"                            // 建安更新时？
   "judgeFixing"                             // 判定修正时
   "phaseAfter"                              // 阶段结束后
   "phaseBefore"                             // 阶段开始前
   "phaseBeforeEnd"                          // 阶段开始前的结束时
   "phaseBeforeStart"                        // 阶段开始之前
   "phaseBegin"                              // 阶段开始时
   "phaseBeginStart"                         // 阶段开始时
   "phaseChange"                             // 阶段变化时
   "phaseDrawBegin1"                         // 摸牌阶段开始时1
   "phaseDrawBegin2"                         // 摸牌阶段开始时2
   "phaseEnd"                                // 阶段结束时
   "recastingGain"                           // 重铸获得牌时
   "recastingGained"                         // 重铸获得牌后
   "recastingLose"                           // 重铸失去牌时
   "recastingLost"                           // 重铸失去牌后
   "removeCharacterBefore"                   // 移除角色之前
   "removeSubPlayer"                         // 移除随从时
   "rewriteDiscardResult"                    // 重写弃牌结果时
   "rewriteGainResult"                       // 重写获得结果时
   "roundStart"                              // 每轮开始时
   "shaDamage"                               // 杀造成伤害时
   "shaHit"                                  // 杀命中时
   "shaMiss"                                 // 杀未命中时
   "shaUnhirt"                               // 杀未造成伤害时
   "showCharacterAfter"                      // 展示角色之后
   "showCharacterEnd"                        // 展示角色结束时
   "skillAfter"                              // 使用技能后
   "subPlayerDie"                            // 随从死亡时
   "triggerAfter"                            // 触发之后
   "triggerHidden"                           // 隐藏触发时
   "triggerInvisible"                        // 不可见触发时
   "useCard"                                 // 使用卡牌时
   "useCard0"                                // 使用卡牌时0
   "useCard1"                                // 使用卡牌时1
   "useCard2"                                // 使用卡牌时2
   "washCard"                                // 洗牌时
   "wuguRemained"                            // 五谷剩余时
   "yingbian"                                // 应变时
   "zhuUpdate"                               // 主公更新时
   "[eventname]Inserted"                     // 事件插入时
   "addShownCards"                           // 添加展示牌时
   "arrangeTrigger"                          // 安排触发时
   "chooseDrawRecover"                       // 选择摸牌回血时
   "debateCallback"                          // 议事回调时
   "delay"                                   // 延迟时
   "delayx"                                  // 延迟时
   "dieAfter"                                // 死亡后
   "gainMultiple"                            // 群体获得时
   "judgeCallback"                           // 判定回调时
   "leaderView"                              // 君主视图时？
   "loadMode"                                // 加载模式时
   "logSkill"                                // 记录技能时
   "logSkillBegin"                           // 记录技能开始时。
   "orderingDiscard"                         // 处理区弃置时
   "qianlidanji_replace"                     // 千里单骑切换难度时
   "replacePlayer"                           // 替换角色时
   "replacePlayerSingle"                     // 替换单个角色时
   "replacePlayerTwo"                        // 替换两个角色时
   "shidianyanluo_huanren"                   // 十殿阎罗换人时
   "showCharacter"                           // 展示角色时
   "showYexings"                             // 展示野心时
   "trigger"                                 // 触发时
   "useCardToExcluded"                       // 使用卡牌被排除时
   "useCardToPlayer"                         // 使用卡牌指定玩家时
   "useCardToPlayered"                       // 使用卡牌指定玩家后
   "useCardToTarget"                         // 使用卡牌指定目标时
   "useCardToTargeted"                       // 使用卡牌指定目标后
   "video"                                   // 放视频时
   "waitForPlayer"                           // 等待玩家时
   "wuxianhuoli_reward"                      // 无限火力奖励时
   "year_limit_pop"                          // 年限弹出时
   "hideShownCards"                          // 隐藏展示卡牌时
   "phase"                                   // 阶段时
   "phaseUse"                                // 阶段使用时
   "swapHandcards"                           // 交换手牌时
```

</details>

## 2. 阶段类触发<a id="阶段类"></a>

### 2.1 角色阶段
```javascript
trigger: {
    player: [
    // 核心阶段触发
        "enterGame",                                // 角色进入游戏时（初始化技能）
        "phaseBefore",                              // 阶段开始前（可跳过阶段）
        "phaseBegin",                               // 阶段开始时
        "phaseEnd",                                 // 阶段结束时
        "phaseAfter",                               // 阶段结束后（切换阶段前）

    // 细分阶段控制
        "phaseBeforeStart",                         // 阶段开始前的初始化（优先级最高）
        "phaseBeforeEnd",                           // 阶段开始前的收尾（跳过阶段前最后时机）
        "phaseBeginStart",                          // 阶段开始时的初始化（优先级最高）

    // 标准回合阶段
        "phaseZhunbei",                             // 准备阶段（回合初始）
        "phaseJudge",                               // 判定阶段（处理延时锦囊）
        "phaseDraw",                                // 摸牌阶段（默认摸2牌）
        "phaseUse",                                 // 出牌阶段（主要行动阶段）
        "phaseDiscard",                             // 弃牌阶段（手牌调整）
        "phaseJieshu",                              // 结束阶段（回合收尾）

    // 阶段细分事件
        "phaseDrawBegin1",                          // 摸牌阶段开始时（可修改摸牌数）
        "phaseDrawBegin2",                          // 摸牌阶段第二触发点（稳定触发）
        "phaseUseBegin",                            // 出牌阶段开始时（初始化出牌次数）
        "phaseUseEnd",                              // 出牌阶段结束时（清理出牌状态）
        "phaseUseSkipped"                           // 出牌阶段被跳过时
    ]
}
```
#### 阶段顺序：
- enterGame
- phaseBefore
- phaseBeforeStart
- phaseBeforeEnd
- phaseBeginStart
- phaseBegin
- phaseZhunbei
- phaseJudge
- phaseDraw
- phaseUse
- phaseDiscard
- phaseJieshu
- phaseEnd
- phaseAfter

## 3. 事件类触发<a id="事件类"></a>

### 3.1 伤害相关
```javascript
trigger: {
    // 造成伤害（来源方）
    source: [
        "damageBegin1",                             // 伤害计算阶段1：可修改伤害值（如裸衣、酒）
        "damageBegin2",                             // 伤害计算阶段2：不可改值但可执行效果（寒冰剑弃牌）
        "damageSource",                             // 伤害来源确定时（最终来源判定）
        "shaDamage",                                // 杀造成伤害时（命中后触发）
        "damageBegin",                              // 造成伤害时
        "damageEnd"                                 // 伤害结算完成后
    ],
    // 受到伤害（目标方）
    player: [
        "damageBegin3",                             // 【受到伤害阶段1】可转移/修改伤害（标准天香）
        "damageBegin4",                             // 【受到伤害阶段2】可取消伤害（界天香）
        "damageZero",                               // 伤害被无效时（仁王盾防黑杀）
        "damageBegin",                              // 受到伤害时
        "damageEnd"                                 // 受到伤害后
    ],
}
```

### 3.2 卡牌相关
```javascript
trigger: {
    // 主动使用
    player: [
        "useCard",                                  // 使用卡牌时（包括技能转化）
        "useCard0",                                 // 使用卡牌时（原始牌）
        "useCard1",                                 // 使用卡牌时（转换后的牌）
        "useCard2",                                 // 使用卡牌时（最终生效的牌）
        "useCardTo",                                // 使用卡牌指定目标时
        "respond",                                  // 打出响应牌时（如闪响应杀）
        "juedou",                                   // 使用决斗时
        "shaHit",                                   // 杀命中时
        "shaMiss",                                  // 杀未命中时
        "shaDamage",                                // 杀命中且造成伤害时
        "shaUnhirt",                                // 杀未造成伤害时(此处hirt为源码拼写错误，实际为hurt，调用时请使用hirt)
        "wuguRemained",                             // 五谷有多余展示牌时
        "useCardToPlayer",                          // 使用牌指定目标时
        "useCardToPlayered"                         // 使用牌指定目标后
    ],
    // 成为目标
    target: [
        "useCardToTarget",                          // 成为卡牌目标时（指定目标阶段）
        "useCardToTargeted"                         // 成为卡牌目标后（目标确定完成）
    ],
    // 卡牌移动
    player: [
        "gain",                                     // 获得牌时（包括摸牌、获得其他角色牌）
        "lose",                                     // 失去牌时（包括弃置、被拿走）
        "recast",                                   // 重铸牌时
        "draw",                                     // 摸牌时
        "discard",                                  // 弃牌时
        "addToExpansion"                            // 置于武将牌时
    ]
}
```

### 3.3 状态相关
```javascript
trigger: {
    // 血量变化
    player: [
        "changeHp",                                 // 血量变化时（包括增减）
        "loseHp",                                   // 失去体力时
        "recover"                                   // 回复体力时
    ],
    // 装备变更
    player: [
        "equip",                                    // 使用装备牌时
        "lose_${equipName}",                        // 失去特定装备时
        "replaceEquip"                              // 替换装备时
    ],
    // 角色状态
    player: [
        "die",                                      // 角色死亡时
        "dying",                                    // 进入濒死状态时
        "dyingEnd",                                 // 脱离濒死状态时
        "turnOver",                                 // 翻面时
        "changeCharacter",                          // 切换武将时
        "showCharacterEnd"                          // 展示角色牌后
    ]

}
```

## 4. 全局触发<a id="全局类"></a>

### 4.1 基本用法
```javascript
trigger: {
    global: [
        "gameStart",                          // 游戏开始时
        "roundStart",                         // 轮数开始时
        "phaseBegin",                         // 任意角色回合开始时
        "damage",                             // 任意伤害事件（全局监听）
        "damageEnd",                          // 伤害结算完成后（反馈类技能）
        "washCard",                           // 洗牌时
    ]
}
```

### 5. 主动触发<a id="主动类"></a>

### 5.1 基本用法
```javascript
trigger:{
    enable: [
        "phaseUse",                                 // 出牌阶段可用
        "chooseToUse",                              // 主动使用牌时可用
        "chooseToRespond",                          // 响应阶段可用
        "chooseCard",                               // 选牌操作时可用
    ]

}
```

## 6. 触发优先级

### 6.1 优先级设置
```javascript
"priority_skill": {
    trigger: {player:"phaseBegin"},
    priority: 5,                          // 设置优先级(默认为1)
    forced: true,
    content(){}
}
```

## 7. 触发条件

### 7.1 基本判断
```javascript
filter(event, player){
    // 血量条件
    return player.hp < 3;
    
    // 手牌条件
    return player.countCards("h") > 0;
    
    // 目标条件
    return event.player != player;
    
    // 牌名条件
    return event.card && event.card.name =="sha";
}
```

### 7.2 复杂条件
```javascript
filter(event, player){
    // 多重条件
    if(player.hp < 3 && player.countCards("h") > 0){
    return event.player.isAlive() && 
     event.player.countCards("h") > 2;
    }
    return false;
}
```

## 8. 进阶用法

### 8.1 触发顺序控制
```javascript
"order_skill": {
    trigger: {player:"phaseBegin"},
    async content(event, trigger, player){
    // 打断后续触发
    trigger.cancel();
    
    // 自定义触发
    event.trigger("order_skill")
    
    // 跳过特定阶段
    player.skip("phaseDraw");
    }
}
```

### 8.2 条件触发
```javascript
"condition_skill": {
    trigger: {player:"phaseBegin"},
    direct: true,                          // 锁定技且不输出日志
    check(event, player){
    return player.hp < 3;                   // AI发动条件
    },
    async content(event, trigger, player){
    let target = await player.chooseTarget("对一名角色造成伤害，然后你失去一点体力").forResult();
    if (target.bool){
        player.logSkill("condition_skill",target.targets[0])
        await target.targets[0].damage()
        await player.loseHp()
    }
    }
}
```

## 练习

1. 创建一个多重触发技能：
   - 在回合开始和结束时触发
   - 根据不同时机有不同效果
   - 添加触发条件

<details>
<summary>参考答案 
-  🟩 Easy</summary>

```javascript
"multi_trigger": {
    // 多个触发时机
    trigger: {
    player: ["phaseBegin", "phaseEnd"]
    },
    // 触发条件
    filter(event, player){
    if(event.name =="phaseBegin"){
    return player.countCards("h") < 3;                  // 回合开始时手牌少于3
    }
    return player.hp < 3;                   // 回合结束时体力值少于3
    },
    // 根据时机执行不同效果
    async content(event, trigger, player){
        if(event.triggername =="phaseBegin"){
            // 回合开始时摸牌
            await player.draw(2);
            game.log(player,"触发了回合开始效果");
        } else {
            // 回合结束时回血
            await player.recover();
            game.log(player,"触发了回合结束效果");
        }
    },
    ai: {
    threaten: 1.5
    }
}
```
</details>

2. 创建一个全局触发技能：
   - 监听所有角色的特定事件
   - 根据条件决定是否触发
   - 实现连锁效果

<details>
<summary>参考答案 
-  🟨 Medium</summary>

```javascript
"global_trigger": {
    // 监听所有角色的伤害事件
    trigger: {
    global: "damageEnd"
    },
    // 触发条件
    filter(event, player){
        return event.player != player &&                    // 不是自己受伤
        event.player.isAlive() &&                  // 目标存活
        event.num > 0;                 // 伤害大于0
    },
    // 连锁效果
    async content(event, trigger, player){
    // 记录伤害数值
    event.num = trigger.num;
    // 选择效果
    let choices = ["摸牌","回血"];
    if(player.countCards("h") > 0) choices.push("弃牌造成伤害");
    
    let result = await player.chooseControl(choices)
    .set("prompt","请选择一个效果")
    .set("ai", function(){
        if(player.hp <= 2) return"回血";
        if(player.countCards("h") < 2) return"摸牌";
        return"弃牌造成伤害";
    })
    .forResult();
     // 执行效果
    switch(result.control){
        case"摸牌":
            await player.draw(event.num);
            break;
        case"回血":
            await player.recover(event.num);
            break;
        case"弃牌造成伤害":
            await player.chooseToDiscard(1, true);
            let target = await player.chooseTarget("选择一名角色造成伤害").forResult();
            if(target.bool){
                await target.targets[0].damage();
            }
            break;
    }
    },
    ai: {
        threaten: 2,
        expose: 0.2
    }
}
```
</details>

3. 创建一个优先级技能：
   - 设置高优先级
   - 控制其他技能的触发
   - 实现特殊效果

<details>
<summary>参考答案 
-  🟨 Medium</summary>

```javascript
"priority_skill": {
    // 设置高优先级
    priority: 10,
    // 触发时机
    trigger: {
    target: "useCardToTargeted"
    },
    forced: true,
    // 触发条件
    filter(event, player){
    return event.card.name =="sha" &&                   // 目标是【杀】
     player.countCards("h") > 0;                    // 有手牌
    },
    // 特殊效果
    async content(event, trigger, player){
        // 取消原有事件
        trigger.cancel();
        game.log(player,"触发了优先级技能");
        // 展示一张手牌
        let card = await player.chooseCard("h", true,"请展示一张手牌").forResult();
        if(card.bool){
            await player.showCards(card.cards);
            
            // 根据花色执行效果
            if(get.color(card.cards[0]) =="red"){
                await player.draw();
                game.log(player,"展示红色牌，摸一张牌");
            } else {
                await trigger.player.draw();
                game.log(trigger.player,"展示黑色牌，使用者摸一张牌");
            }
        }
    },
    ai: {
    effect: {
    target(card, player, target){
    if(card.name =="sha") return 0.5;
    }
    }
    }
}
```
</details>
</br>
下一节我们将学习如何实现技能效果。 
