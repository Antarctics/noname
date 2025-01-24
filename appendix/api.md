# 附录A：API参考

## 1. 玩家(Player)相关API

### 1.1 基础属性
```javascript
player.name          // 武将名称
player.sex           // 性别(male/female)
player.group         // 势力
player.hp            // 当前体力值  
player.maxHp         // 体力上限
player.hujia         // 护甲值
player.side          // 玩家阵营
player.identity      // 身份
```

### 1.2 状态判断
```javascript
player.isDead()      // 是否已阵亡
player.isAlive()     // 是否存活
player.isDamaged()   // 是否受伤
player.isHealthy()   // 是否满血
player.isLinked()    // 是否横置
player.isTurnedOver()// 是否翻面
player.isOut()       // 是否离场
player.isUnseen()    // 是否暗将
player.isMad()       // 是否混乱
```

### 1.3 技能相关
```javascript
// 添加技能
player.addSkill(skill)           // 添加永久技能
player.addTempSkill(skill, time) // 添加临时技能,可指定时机失效
player.addAdditionalSkill(id, skill) // 添加额外技能

// 移除技能  
player.removeSkill(skill)        // 移除技能
player.disableSkill(skill)       // 禁用技能
player.enableSkill(skill)        // 启用技能

// 技能判断
player.hasSkill(skill)           // 是否有某技能
player.hasZhuSkill()             // 是否有主公技
player.hasGlobalSkill()          // 是否有全局技能
```

### 1.4 卡牌操作
```javascript
// 获得牌
player.gain(cards)               // 获得卡牌
player.gainPlayerCard(target, position) // 获得目标角色的牌
player.draw(num)                 // 摸牌

// 失去牌
player.lose(cards)               // 失去卡牌
player.discard(cards)            // 弃置卡牌
player.chooseToDiscard(num)      // 选择弃置

// 使用牌
player.useCard(card, targets)    // 使用卡牌
player.canUse(card, target)      // 能否对目标使用卡牌
player.useSkill(skill)           // 使用技能

// 判断牌
player.countCards(position)      // 计算某区域的牌数
player.getCards(position)        // 获取某区域的牌
player.hasCard(card, position)   // 是否有某张牌
```

### 1.5 伤害与回复
```javascript
// 伤害
player.damage(num, source, nature) // 受到伤害
player.loseHp(num)                // 失去体力
player.loseMaxHp(num)             // 失去体力上限

// 回复
player.recover(num)               // 回复体力
player.gainMaxHp(num)             // 增加体力上限

// 护甲
player.changeHujia(num)           // 改变护甲值
player.addHujia(num)              // 增加护甲
player.removeHujia()              // 移除护甲
```

### 1.6 选择与交互
```javascript
// 选择目标
player.chooseTarget(options)     // 选择目标
player.chooseTargets(options)    // 选择多个目标
player.chooseBool(options)       // 是/否选择

// 选择牌
player.chooseCard(options)       // 选择手牌
player.chooseToRespond(options)  // 选择打出牌
player.chooseCardTarget(options) // 选择牌和目标

// 选择按钮
player.chooseButton(options)     // 选择按钮
player.chooseControl(options)    // 选择选项
```

### 1.7 动画效果
```javascript
player.$draw(num)                // 摸牌动画
player.$give(num, target)        // 给牌动画
player.$throw(cards)             // 弃牌动画
player.$gain2(cards)             // 获得牌动画
player.$damage(nature)           // 受伤动画
player.$recover()                // 回复动画
player.$skill(name)              // 技能动画
player.$fire()                   // 火焰动画  
player.$thunder()                // 雷电动画
```

## 2. 卡牌(Card)相关API

### 2.1 基础属性
```javascript
card.name           // 卡牌名称
card.suit           // 花色
card.number         // 点数
card.nature         // 属性
card.type          // 类型(basic/trick/equip)
```

### 2.2 状态判断
```javascript
card.hasNature(nature)    // 是否有某属性
card.hasPosition()        // 是否在场上
card.isInPile()          // 是否在牌堆
card.hasTag(tag)         // 是否有标签
```

### 2.3 卡牌操作
```javascript
// 添加/移除属性
card.addNature(nature)    // 添加属性
card.removeNature(nature) // 移除属性

// 知情者相关
card.addKnower(player)    // 添加知情者
card.removeKnower(player) // 移除知情者
card.clearKnowers()       // 清除知情者
card.isKnownBy(player)    // 是否知情

// 其他操作
card.copy()              // 复制卡牌
card.discard()           // 弃置
card.init(cardData)      // 初始化
```

## 3. 武将(Character)相关API

### 3.1 基础属性
```javascript
character.sex            // 性别
character.group          // 势力
character.hp             // 体力值
character.maxHp          // 体力上限
character.hujia          // 护甲值
character.skills         // 技能列表
```

### 3.2 特殊标记
```javascript
character.isZhugong            // 是否主公
character.isUnseen             // 是否暗将
character.isBoss               // 是否Boss
character.isAiForbidden        // 是否禁止AI使用
character.hasHiddenSkill       // 是否有隐藏技能
character.doubleGroup          // 双势力
character.clans                // 势力标记
```

## 4. 事件(Event)相关API

### 4.1 基础事件
```javascript
// 摸牌事件
event.draw = {
  num: 2,               // 摸牌数量
  log: true,            // 是否记录
  bottom: false,        // 是否从牌堆底摸牌
  visible: false        // 是否明牌
}

// 弃牌事件
event.discard = {
  position: 'h',        // 弃置区域
  cards: [],           // 弃置的牌
  discarder: player    // 弃牌者
}

// 伤害事件
event.damage = {
  num: 1,              // 伤害值
  nature: 'fire',      // 伤害属性
  source: player       // 伤害来源
}
```

### 4.2 事件处理
```javascript
// 事件创建
game.createEvent(name)   // 创建事件
event.trigger(name)      // 触发事件
event.cancel()           // 取消事件
event.finish()          // 结束事件

// 事件等待
game.delay()            // 延迟
game.delayx()           // 延迟并等待动画
game.asyncDraw()        // 异步摸牌
game.asyncDiscardPile() // 异步弃牌
```

## 5. 游戏(Game)相关API

### 5.1 游戏状态
```javascript
game.players           // 所有玩家
game.dead             // 已阵亡角色
game.me               // 当前玩家
game.phaseNumber      // 当前回合数
game.roundNumber      // 当前轮数
```

### 5.2 游戏操作
```javascript
game.swapPlayer()      // 交换玩家
game.swapControl()     // 交换控制权
game.pause()           // 暂停游戏
game.resume()          // 恢复游戏
game.over(result)      // 游戏结束
```

### 5.3 联机相关
```javascript
game.broadcast()       // 广播消息
game.broadcastAll()    // 广播给所有玩家
game.syncState()       // 同步状态
game.waitForPlayer()   // 等待玩家
```
