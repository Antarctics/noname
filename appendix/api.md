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
/**
 * 添加技能到玩家的技能列表中。
 * 
 * @param {string|Array} skill - 要添加的技能，可以是一个技能字符串或技能字符串数组。
 * @param {boolean} checkConflict - 是否在添加技能后检查技能冲突。默认为true。
 * @param {boolean} nobroadcast - 是否禁止广播技能添加事件。默认为false。
 * @param {boolean} addToSkills - 是否将技能添加到玩家的技能列表中。默认为true。
 */
player.addSkill(skill, checkConflict, nobroadcast, addToSkills)
/**
 * 添加临时技能到玩家的技能列表中。
 * 
 * @param {string|string[]} skill - 要添加的临时技能，可以是一个技能字符串或技能字符串数组。
 * @param {SkillTrigger|SAAType<Signal>} [expire] - 技能的过期条件，可以是触发器对象或触发器名称（字符串或数组）。
 * @param {boolean} [checkConflict] - 是否在添加技能后检查技能冲突。默认为true。
 */
player.addTempSkill(skill, expire, checkConflict)
/**
 * 添加额外技能到玩家的技能列表中。
 * 
 * @param {string} skill - 技能的主分类或标识符，用于指定要将新技能添加到哪个技能中。
 * @param {string|Array} skillsToAdd - 要添加的技能，可以是一个技能字符串或技能字符串数组。
 * @param {boolean} keep - 是否保留原有的额外技能。如果为false，则会移除原有技能后再添加新技能。
 */
player.addAdditionalSkill(skill, skillsToAdd, keep)

// 移除技能
/**
 * 从玩家的技能列表中移除技能。
 * 
 * @param {string|string[]} skill - 要移除的技能，可以是一个技能字符串或技能字符串数组。
 */
player.removeSkill(skill)
/**
 * 禁用指定的技能或技能组。
 * 
 * @param {string} skill - 要禁用的技能名称。
 * @param {string|string[]} skills - 要禁用的技能或技能组名称，可以是单个技能名称或技能名称数组。
 */
player.disableSkill(skill, skills)
/**
 * 启用指定的技能，将其从禁用列表中移除。
 * 
 * @param {string} skill - 要启用的技能名称。
 */
player.enableSkill(skill)

// 技能判断
/**
 * 检查当前对象是否拥有指定的技能。
 * 
 * @param {string} skill - 要检查的技能名称。
 * @param {Parameters<this['getSkills']>[0]} arg2 - 传递给 `getSkills` 方法的第一个参数。
 * @param {Parameters<this['getSkills']>[1]} arg3 - 传递给 `getSkills` 方法的第二个参数。
 * @param {Parameters<this['getSkills']>[2]} arg4 - 传递给 `getSkills` 方法的第三个参数。
 */
player.hasSkill(skill, arg2, arg3, arg4)
/**
 * 检查当前对象是否拥有指定的主公技。
 * 
 * @param {string} skill - 要检查的主技能名称。
 */
player.hasZhuSkill(skill)
```

### 1.4 卡牌操作
```javascript
// 获得牌
/**
 * 令玩家获得一些牌
 * 
 * @description
 * 该方法支持多种参数类型：
 * - `player` 类型参数：指定牌的来源玩家。
 * - `cards` 或 `card` 类型参数：指定要获得的牌。
 * - `log` 参数：是否记录日志。
 * - `fromStorage` 参数：牌是否来自存储区。
 * - `fromRenku` 参数：牌是否来自“仁库”。
 * - `bySelf` 参数：是否由玩家自己操作。
 * - `string` 类型参数：指定动画类型。
 * - `boolean` 类型参数：是否延迟执行。
 * 
 */
player.gain()
/**
 * 从其他玩家处获得牌。
 * 
 * @description
 * 该方法支持多种参数类型：
 * - `player` 类型参数：指定目标玩家。
 * - `number` 类型参数：指定选择按钮的范围。
 * - `select` 类型参数：指定选择按钮的配置。
 * - `boolean` 类型参数：指定是否强制选择或复杂选择。
 * - `position` 类型参数：指定牌的位置。
 * - `visible` 参数：是否可见。
 * - `visibleMove` 参数：是否可见移动。
 * - `function` 类型参数：指定 AI 逻辑或按钮过滤逻辑。
 * - `object` 类型参数：指定按钮过滤条件。
 * - `string` 类型参数：指定提示信息。
 * 
 */
player.gainPlayerCard()
/**
 * 令玩家摸牌
 * 
 * @description
 * 该方法支持多种参数类型：
 * - `player` 类型参数：指定牌的来源玩家。
 * - `number` 类型参数：指定摸牌的数量。
 * - `boolean` 类型参数：指定是否启用动画。
 * - `nodelay` 参数：禁用延迟并立即执行摸牌。
 * - `visible` 参数：是否可见摸牌。
 * - `bottom` 参数：是否从牌堆底部摸牌。
 * - `object` 类型参数：指定牌堆配置。
 * 
 * 如果未指定摸牌数量，则默认摸 1 张牌。如果摸牌数量小于等于 0，则直接跳过事件。
 * 在特定游戏模式下（如 "stone" 和 "deck"），会调整牌堆配置。
 */
player.draw()
/**
 * 将指定的卡牌添加到扩展区中，需要自行添加gaintag
 * 
 * @param {string|string[]} arg - 可以是以下类型：
 *   - "player"：指定玩家作为来源。
 *   - "cards"：指定一组卡牌。
 *   - "card"：指定单张卡牌。
 *   - "log"：是否记录日志。
 *   - "fromStorage"：是否从存储中添加。
 *   - "fromRenku"：是否从Renku中添加，并标记为从存储中添加。
 *   - "bySelf"：是否由自己操作。
 *   - "string"：指定动画类型。
 *   - "boolean"：指定是否延迟执行。
 */
player.addToExpansion()

// 失去牌
/**
 * 令玩家失去牌
 * 
 * @description
 * 该方法支持多种参数类型：
 * - `player` 类型参数：指定牌的来源玩家。
 * - `cards` 或 `card` 类型参数：指定要失去的牌。
 * - `div` 或 `fragment` 类型参数：指定牌的目标位置。
 * - `toStorage` 参数：是否将牌移动到存储区。
 * - `toRenku` 参数：是否将牌移动到“仁库”。
 * - `visible` 参数：是否可见失去牌。
 * - `insert` 参数：是否插入牌。
 * 
 * 该方法会过滤掉玩家不拥有的牌。如果失去的牌为空，则直接跳过事件。
 * 如果未指定目标位置，则默认将牌移动到弃牌堆。
 */
player.lose()
/**
 * 令玩家弃牌
 * 
 * @description
 * 该方法支持多种参数类型：
 * - `player` 类型参数：指定牌的来源玩家。
 * - `cards` 或 `card` 类型参数：指定要弃掉的牌。
 * - `boolean` 类型参数：指定是否启用动画。
 * - `div` 或 `fragment` 类型参数：指定牌的目标位置。
 * - `notBySelf` 参数：是否不由玩家自己操作。
 * 
 * 如果未指定要弃掉的牌，则直接跳过事件。
 */
player.discard()
/**
 * 将卡牌添加到弃牌区域。
 * 
 * @description
 * 该方法支持多种参数类型：
 * - `player` 类型参数：指定目标玩家。
 * - `cards` 类型参数：指定要添加的卡牌列表。
 * - `card` 类型参数：指定要添加的单个卡牌。
 * - `log` 参数：是否记录日志。
 * - `fromStorage` 参数：是否从存储区域添加。
 * - `fromRenku` 参数：是否从 Renku 区域添加。
 * - `bySelf` 参数：是否由玩家自己添加。
 * - `string` 类型参数：指定动画类型。
 * - `boolean` 类型参数：指定是否延迟执行。
 */
player.chooseToDiscard()

// 使用牌
/**
 * 让玩家使用卡牌。
 * 
 * @description
 * 该方法支持多种参数类型：
 * - `cards` 类型参数：指定使用的卡牌列表。
 * - `players` 或 `player` 类型参数：指定卡牌的目标玩家。
 * - `card` 类型参数：指定使用的卡牌。
 * - `object` 类型参数：指定卡牌信息。
 * - `string` 类型参数：指定技能名称或特殊选项（如 "noai" 或 "nowuxie"）。
 * - `boolean` 类型参数：指定是否增加计数。
 * 
 * 该方法会根据卡牌信息调整目标玩家列表，并记录 AI 日志（如果适用）。
 * 如果卡牌是单目标卡牌，则会调整目标玩家列表以匹配单目标逻辑。
 */
player.useCard()
/**
 * 检查当前玩家是否可以对目标玩家使用指定卡牌。
 * 
 * @param {Card|VCard|object|string} card - 要使用的卡牌，可以是卡牌对象、虚拟卡牌、卡牌信息对象或卡牌名称。
 * @param {Player} target - 目标玩家。
 * @param {false} [distance] - 是否忽略距离限制。如果为 `false`，则无距离限制。
 * @param {boolean|GameEvent} [includecard] - 是否受使用次数限制。可以传入用于检测的事件对象。
 */
player.canUse(card, target, distance, includecard)
/**
 * 检查场上是否存在可以对其使用指定卡牌的目标玩家。
 * 
 * @param {Card|VCard|object|string} card - 要使用的卡牌，可以是卡牌对象、虚拟卡牌、卡牌信息对象或卡牌名称。
 * @param {false} [distance] - 是否忽略距离限制。如果为 `false`，则无距离限制。
 * @param {boolean|GameEvent} [includecard] - 是否受使用次数限制。可以传入用于检测的事件对象。
 * @description
 * 该方法会遍历场上所有玩家，并调用 `canUse` 方法检查当前玩家是否可以对目标玩家使用该卡牌。
 * 如果存在至少一个满足条件的目标玩家，则返回 `true`，否则返回 `false`。
 */
player.hasUseTarget(card, distance, includecard)

// 判断牌
/**
 * 获取符合条件的卡牌数量
 * 
 * @param {string} [arg1] - 指定卡牌的位置类型，默认为 'h'（手牌）。支持以下值：
 * - 'h'：手牌。
 * - 's'：特殊区的牌。
 * - 'e'：装备区的卡牌。
 * - 'j'：判定区的卡牌。
 * - 'x'：扩展区的卡牌。
 * @param {string|Record<string, any>|((card: Card) => boolean)} [arg2] - 过滤条件，可以是以下类型：
 * - `string`：卡牌名称。
 * - `Array`：卡牌名称列表。
 * - `object`：卡牌属性过滤条件。
 * - `function`：自定义过滤函数。
 */
player.countCards(arg1, arg2)
/**
 * 获取符合条件的卡牌
 * 
 * @param {string} [arg1] - 指定卡牌的位置类型，默认为 'h'（手牌）。支持以下值：
 * - 'h'：手牌。
 * - 's'：特殊区的牌。
 * - 'e'：装备区的卡牌。
 * - 'j'：判定区的卡牌。
 * - 'x'：扩展区的卡牌。
 * @param {string|Record<string, any>|((card: Card) => boolean)} [arg2] - 过滤条件，可以是以下类型：
 * - `string`：卡牌名称。
 * - `Array`：卡牌名称列表。
 * - `object`：卡牌属性过滤条件。
 * - `function`：自定义过滤函数。
 */
player.getCards(arg1, arg2)
/**
 * 检查当前玩家是否拥有符合条件的卡牌。
 * 
 * @param {string|function} name - 卡牌名称或过滤函数。
 * @param {string} [position] - 卡牌的位置类型。
 */
player.hasCard(name, position)
/**
 * 统计具有指定标签的扩展区卡牌数量。
 * 
 * @param {string} tag - 要匹配的标签。
 */
player.countExpansions(tag)
/**
 * 获取具有指定标签的扩展区卡牌。
 * 
 * @param {string} tag - 要匹配的标签。
 */
player.getExpansions(tag)
/**
 * 检查是否存在具有指定标签的扩展区卡牌。
 * 
 * @param {string} tag - 要匹配的标签。
 */
player.hasExpansions(tag)
```

### 1.5 伤害与回复
```javascript
// 伤害
/**
 * 对当前玩家造成伤害。
 * 
 * @description
 * 该方法支持多种参数类型：
 * - `cards` 类型参数：指定与伤害相关的卡牌列表。
 * - `card` 类型参数：指定与伤害相关的卡牌。
 * - `number` 类型参数：指定伤害值。
 * - `player` 类型参数：指定伤害来源玩家。
 * - `nocard` 参数：是否忽略卡牌。
 * - `nosource` 参数：是否忽略伤害来源。
 * - `notrigger` 参数：是否禁用触发效果。
 * - `unreal` 参数：是否虚拟伤害。
 * - `nature` 或 `natures` 类型参数：指定伤害属性（如 "fire"、"poison" 等）。
 * 
 * 该方法会根据伤害属性（如 "poison"）和虚拟伤害标志调整触发逻辑。
 * 如果伤害值小于等于 0，则触发 "damageZero" 事件并结束。
 */
player.damage()
/**
 * 流失当前玩家的体力。
 * 
 * @param {number} num - 要扣减的体力值。
 */
player.loseHp(num)
/**
 * 扣减当前玩家的体力上限。
 * 
 * @description
 * 该方法支持以下参数：
 * - `number` 类型参数：指定扣减的体力上限值，默认为 1。
 * - `boolean` 类型参数：指定是否强制扣减。
 */
player.loseMaxHp()

// 回复
/**
 * 对当前玩家进行回复。
 * 
 * @description
 * 该方法支持多种参数类型：
 * - `cards` 类型参数：指定与回复相关的卡牌列表。
 * - `card` 类型参数：指定与回复相关的卡牌。
 * - `player` 类型参数：指定回复来源玩家。
 * - `number` 类型参数：指定回复值。
 * - `nocard` 参数：是否忽略卡牌。
 * - `nosource` 参数：是否忽略回复来源。
 * 
 * 如果回复值小于等于 0 或玩家已满血，则直接结束事件。
 */
player.recover()
/**
 * 增加当前玩家的体力上限。
 * 
 * @description
 * 该方法支持以下参数：
 * - `number` 类型参数：指定增加的体力上限值，默认为 1。
 * - `boolean` 类型参数：指定是否强制增加。
 */
player.gainMaxHp()

// 护甲
/**
 * 改变当前玩家的护甲值。
 * 
 * @param {number} [num] - 要改变的护甲值，默认为 1。
 * @param {"gain" | "lose" | "damage" | "null"} [type] - 改变的类型，默认为根据 `num` 自动判断：
 * - `gain`：增加护甲。
 * - `lose`：减少护甲。
 * - `damage`：护甲受到伤害。
 * - `null`：无变化。
 * @param {number} [limit] - 护甲上限。如果护甲值超过上限，则调整 `num` 以确保不超过上限。
 */
player.changeHujia(num, type, limit)
```
// 后续函数的参数注释将在后续编辑中添加。
### 1.6 选择与交互
```javascript
// 选择目标
/**
 * 创建选择目标事件。
 * 
 * @description
 * 该方法支持多种参数类型，用于配置选择目标的行为：
 * - `number` 类型参数：指定选择目标的数量范围（最小和最大值相同）。
 * - `select` 类型参数：指定选择目标的配置。
 * - `dialog` 类型参数：指定关联的对话框，并禁用提示。
 * - `boolean` 类型参数：指定是否强制选择（`forced`）。
 * - `function` 类型参数：
 *   - 第一个 `function` 参数：指定目标过滤逻辑。
 *   - 第二个 `function` 参数：指定 AI 逻辑。
 * - `string` 类型参数：指定提示信息。
 * 
 * @description
 * 默认行为：
 * - 如果没有指定 `filterTarget`，则使用默认的目标过滤逻辑（`lib.filter.all`）。
 * - 如果没有指定 `selectTarget`，则默认为 `[1, 1]`。
 * - 如果没有指定 `ai`，则使用默认的 AI 逻辑（`get.attitude2`）。
 * 
 */

player.chooseTarget()
player.chooseBool()       // 是/否选择

// 选择牌
player.chooseCard()       // 选择手牌
/**
 * 创建一个选择响应卡牌的事件。
 * 
 * @description
 * 支持的参数类型：
 * - `number` 或 `select` 类型参数：指定选择卡牌的数量或配置。
 * - `boolean` 类型参数：指定是否强制选择（`forced`）。
 * - `position` 类型参数：指定卡牌的位置。
 * - `function` 类型参数：
 *   - 如果未指定 `filterCard`，则作为卡牌过滤逻辑。
 *   - 如果已指定 `filterCard`，则作为 AI 逻辑。
 * - `object` 类型参数：指定卡牌过滤条件。
 * - `nosource` 参数：指定是否无来源玩家。
 * - `string` 类型参数：指定提示信息。
 * 
 * @description
 * 默认行为：
 * - 如果没有指定 `filterCard`，则使用默认的卡牌过滤逻辑（`lib.filter.all`）。
 * - 如果没有指定 `selectCard`，则默认为 `[1, 1]`。
 * - 如果没有指定 `source` 且未设置 `nosource`，则默认来源玩家为当前事件的玩家。
 * - 如果没有指定 `ai`，则使用默认的 AI 逻辑（`get.unuseful2`）。
 * - 如果没有指定 `ai2`，则使用默认的 AI 逻辑（始终选择第一个选项）。
 * - 如果没有指定 `prompt`，则自动生成提示信息。
 * - 默认卡牌位置为 `"hs"`。
 */
player.chooseToRespond()
player.chooseToUse() // 选择去使用
/**
 * 创建一个选择卡牌和目标事件。
 * 
 * @param {object} choose - 配置对象，用于指定选择卡牌和目标的行为。
 * 
 * @description
 * 配置对象支持的属性：
 * - `filterCard`：卡牌过滤逻辑，可以是函数或对象。如果未指定，则使用默认的卡牌过滤逻辑（`lib.filter.all`）。
 * - `filterTarget`：目标过滤逻辑，可以是函数或对象。如果未指定，则使用默认的目标过滤逻辑（`lib.filter.all`）。
 * - `selectCard`：选择卡牌的数量。如果未指定，则默认为 `1`。
 * - `selectTarget`：选择目标的数量。如果未指定，则默认为 `1`。
 * - `ai1`：卡牌选择的 AI 逻辑。如果未指定，则使用默认逻辑（`get.unuseful2`）。
 * - `ai2`：目标选择的 AI 逻辑。如果未指定，则使用默认逻辑（`get.attitude2`）。
 * 
 */
player.chooseCardTarget(choose)

// 选择按钮
/**
 * 创建并配置一个选择按钮事件。
 * 
 * @description
 * 该方法支持多种参数类型，用于配置选择按钮的行为：
 * - `boolean` 类型参数：
 *   - 第一个 `boolean` 参数：指定是否强制选择（`forced`）。
 *   - 第二个 `boolean` 参数：指定是否为复杂选择（`complexSelect`）。
 * - `dialog` 类型参数：指定关联的对话框，并自动关闭对话框。
 * - `select` 类型参数：指定选择按钮的配置。
 * - `number` 类型参数：指定选择按钮的范围（最小和最大值相同）。
 * - `function` 类型参数：
 *   - 第一个 `function` 参数：指定 AI 逻辑。
 *   - 第二个 `function` 参数：指定按钮过滤逻辑。
 * - `array` 类型参数：指定用于创建对话框的配置。
 * 
 * @description
 * 默认行为：
 * - 如果没有指定 `forced`，则默认为 `false`。
 * - 如果没有指定 `filterButton`，则使用默认的按钮过滤逻辑。
 * - 如果没有指定 `selectButton`，则默认为 `[1, 1]`。
 * - 如果没有指定 `ai`，则使用默认的 AI 逻辑（始终选择第一个选项）。
 * - 如果没有指定 `complexSelect`，则默认为 `true`。
 */
player.chooseButton()
player.chooseControl()    // 选择选项
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

## 6. 窗口(UI)相关API
### 6.1 对话框（Dialog）
```javascript
/**
 * 创建游戏内对话框组件，支持：
 * - 文本/卡牌/角色/按钮/自定义元素
 * 
 * @param {string} title - 对话框标题（支持HTML）
 * @param {Array} content - 内容定义数组，结构为：
 *   [
 *     contentData,   // 内容数据（类型取决于contentType）
 *     contentType    // 内容类型标识符（"vcard"/"character"/"textbuttons"等）
 *   ]
 * @param {...(string|boolean)} options - 配置选项：
 *   - "hidden"        : 创建后不自动打开（需手动调用open()）
 *   - true            : 静态模式（禁用关闭功能）
 *   - "forcebutton"   : 强制显示按钮
 *   - "notouchscroll" : 禁用触摸滚动
 *   - "noforcebutton" : 强制隐藏按钮
 * 
 * @example 基础对话框
 * const dialog = ui.create.dialog("提示", ["系统错误！", "text"]);
 * 
 * @example 卡牌选择对话框
 * const dialog = ui.create.dialog("选择手牌", [player.getCards("h"), "vcard"], "forcebutton");
 */

ui.create.dialog(title,content,options)

/**
 * 对话框内容类型定义
 * @property {string} vcard - 虚拟卡牌（数据格式：[]Card数组）
 * @property {string} card - 卡牌列表（数据格式：[]Card数组）
 * @property {string} character - 角色信息（数据格式：角色名称）
 * @property {string} player - 玩家列表（数据格式：[]对象数组）
 * @property {string} textbutton - 文本按钮（数据格式：[link, text]）
 * @property {string} tdnodes - 表格按钮（数据格式：[]文本数组）
 * @property {string} blank - 卡牌背面（数据格式：[]Card数组）

 */

/**
 * 对话框实例方法
 * @method open() - 打开对话框
 * @method close() - 强制关闭对话框
 * @method add(content) - 动态添加内容（参数格式同构造函数）
 * @method addText(text, center) - 添加文本段落（center: 是否居中）
 * @method addNewRow(buttons) - 添加横向按钮行（按钮配置数组）
 * @property {boolean} supportsPagination - 分页支持开关（默认false）
 * @property {Map} paginationMap - 分页控制器映射表（需开启分页）
 * @property {boolean} static - 静态模式开关（禁用关闭）
 */
 ```
