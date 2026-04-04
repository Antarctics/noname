# 附录A：API参考

此处仅列出部分API，更多函数请查看\noname\library\element\中的相关文件。

## 1. 玩家(Player)相关API

### 1.1 基础属性
```javascript
player.name          // 武将名称
player.sex           // 性别(male/female)
player.group         // 势力
player.hp            // 当前体力值
player.getDamagedHp  // 已损失体力值
player.maxHp         // 体力上限
player.hujia         // 护甲值
player.side          // 玩家阵营
player.identity      // 身份
player._trueMe       // 控制权
player.getSeatNum    // 座位号
```

### 1.2 状态相关
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
player.changeGroup() // 修改势力
player.isPhaseUsing()     // 是否为出牌阶段
player.canCompare(target) // 能否拼点
player.inRange(target)    // 是否在攻击范围
player.inRangeOf(source)  // 是否处于攻击范围
// 区域相关
/**
 * 废除装备栏
 * - 'equip1'：武器。
 * - 'equip2'：防具。
 * - 'equip3'：防御马。
 * - 'equip4'：进攻马。
 * - 'equip5'：宝物。
 * @param {string|number} arg - 废除装备栏的位置。
 */
player.disableEquip(arg)
player.enableEquip()       // 恢复装备栏
player.isDisabled()        // 是否废除
player.isEmpty()           // 是否为空
player.swapEquip(target)   // 交换装备
player.countDisabled()     // 废除数量

player.disableJudge()     // 废除判定区
player.enableJudge()      // 恢复判定区

/**
 * 快速获取一名角色当前轮次/倒数第X轮次的历史
 * @param {(event:GameEventPromise)=>boolean} filter 筛选条件，不填写默认为lib.filter.all
 * @param {number} [num] 获取倒数第num轮的历史，默认为0，表示当前轮
 * @param {boolean} [keep] 若为true,则获取倒数第num轮到现在的所有历史
 * @param {GameEventPromise} last 代表最后一个事件，获取该事件之前的历史
 */
player.getRoundHistory(key, filter, num, keep, last)
/**
 * 返回角色本回合历史
 * 
 * @param { (event: GameEventPromise) => boolean } [filter] 过滤条件
 * @param { GameEventPromise } [last] 若有改参数，则该参数事件之后的将被排除掉
 */
player.getHistory(key, filter, last)
/**
 * 遍历历史
 * @param { (event: GameEventPromise) => void } filter 遍历过程需要执行的函数
 * @param { GameEventPromise } [last]
 */
player.checkHistory(key, filter, last)
player.hasHistory(key, filter, last) // 本回合是否存在历史
player.getLastHistory(key, filter, last) // 返回最后一回合历史
player.checkAllHistory(key, filter, last) // 遍历全局历史
player.hasAllHistory(key, filter, last) // 本局是否存在历史
player.getAllHistory(key, filter, last) // 返回本局历史
player.getAttackRange(raw) // 返回攻击距离，是否为基础值
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
/**
 * 技能使用次数
 * 
 * @param {string} skill - 要获取的技能名称
 */
player.countSkill(skill)
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
 * 令玩家弃置其区域内一些能被弃置的牌
 *
 * @description
 * 该方法支持多种参数类型：
 * - `player` 类型参数：指定牌的来源玩家。
 * - `cards` 或 `card` 类型参数：指定要弃掉的牌。
 * - `position` 参数：指定弃置到的区域
 * - `log` 参数：因对应Mod技能导致部分牌未被弃置时，是否显示对应技能名。默认'popup'
 *
 */
player.modedDiscard()
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
/**
 * 获取玩家本回合内使用倒数第X+1张牌
 * 
 * @param {number} num - 第X+1张牌，默认为0。
 */
player.getLastUsed(num)
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
 * - `nohujia` 参数：是否无视护甲
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
 * 默认行为：
 * - 如果没有指定 `forced`，则默认为 `false`。
 * - 如果没有指定 `filterButton`，则使用默认的按钮过滤逻辑。
 * - 如果没有指定 `selectButton`，则默认为 `[1, 1]`。
 * - 如果没有指定 `ai`，则使用默认的 AI 逻辑（始终选择第一个选项）。
 * - 如果没有指定 `complexSelect`，则默认为 `true`。
 */
player.chooseButton()
player.chooseControl()    // 选择选项
/**
 * 选择废除一个未废除的装备栏
 * 
 * @description
 * 该方法支持多种参数类型，用于配置选择行为：
 * - `boolean` 类型参数：是否同时废除两个坐骑栏。
 * - `player` 类型参数：指定目标角色。
 * - `select` 类型参数：指定选择按钮的配置。
 * - `number` 类型参数：指定选择按钮的范围。
 */
player.chooseToDisable()
player.chooseToEnable()   // 选择恢复一个废除的装备栏
player.chooseToPSS()      // 选择一名角色进行猜拳
player.chooseToGuanxing() // 执行一次卜算
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
player.$throwEmotion(target,'egg') // 投掷动画
```

## 2. 卡牌(Card)相关API

### 2.1 基础属性
```javascript
card.name           // 卡牌名称
card.suit           // 花色
card.number         // 点数
card.nature         // 属性
card.type          // 类型(basic/trick/equip)
card.extraDamage    // 额外伤害
card.baseDamage    // 基础伤害
card.directHit    // 强中目标
card.effectCount  // 执行次数
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
/**
 * 给此牌添加特定的cardtag（如添加应变条件）
 * 
 * @param { string } tag
 */
card.addCardtag(tag)
/** 
 * 给此牌移除特定的cardtag（如移除应变条件）
 * 
 * @param { string } tag
 */
card.removeCardtag(tag)
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

### 4.1 事件处理
```javascript
// 事件创建
game.createEvent(name)   // 创建事件
event.trigger(name)      // 触发事件
event.cancel()           // 取消事件
event.finish()          // 结束事件
event.getParent()       // 父级事件

// 事件等待
game.delay()            // 延迟
game.delayx()           // 根据动画速度延迟
```
## 4.2.各事件AI参数
```javascript
/**
 * @description
 * @param {Event} event - 当前事件的父级事件
 * @param {Player} player - 当前玩家对象
 * 
 */
chooseControl.ai(event, player)
chooseToEnable.ai((event, player, list))
chooseToDisable.ai((event, player, list))
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
game.cardsDiscard()    // 将cards移动到弃牌区
game.cardsGotoSpecial() // 将cards移动到特殊区
game.createCard()     // 创建卡牌（一次性）
game.createCard2()    // 创建卡牌
```

### 5.3 联机相关
```javascript
game.broadcast()       // 广播消息
game.broadcastAll()    // 广播给所有玩家
game.syncState()       // 同步状态
game.waitForPlayer()   // 等待玩家
```

## 6、读取（Get）相关API
### 6.1 卡牌相关
```javascript
/**
 * 从指定区域获得一张牌
 * @param { function | string | object | true } name 牌的筛选条件或名字，true为任意一张牌
 * @param { string | boolean } [position] 筛选区域，默认牌堆+弃牌堆：
 *
 * cardPile: 仅牌堆；discardPile: 仅弃牌堆；filed: 牌堆+弃牌堆+场上
 *
 * 若为true且name为string | object类型，则在筛选区域内没有找到卡牌时创建一张name条件的牌
 *
 * @param { string } [start] 遍历方式。默认top
 *
 * top: 从牌堆/弃牌堆顶自顶向下遍历
 * bottom: 从牌堆/弃牌堆底自底向上遍历
 * random: 随机位置遍历
 * @returns { Card | ChildNode | null }
 */
get.cardPile(name, position, start)
/**
 * 从牌堆获得一张牌
 * @param { function | string | object | true } name 牌的筛选条件或名字，true为任意一张牌
 * @param { string } [start] 遍历方式。默认top
 *
 * top：从牌堆顶自顶向下遍历
 * bottom：从牌堆底自底向上遍历
 * random: 随机位置遍历
 * @returns { Card | ChildNode | null }
 */
get.cardPile2(name, start)
/**
 * 从弃牌堆获得一张牌
 * @param { function | string | object | true } name 牌的筛选条件或名字，true为任意一张牌
 * @param { string } [start] 遍历方式。默认top
 *
 * top：从弃牌堆顶自顶向下遍历
 * bottom：从弃牌堆底自底向上遍历
 * random: 随机位置遍历
 * @returns { Card | ChildNode | null }
 */
get.discardPile(name, start)
/**
 * 返回数字在扑克牌中的表示形式
 * @param { number } num
 * @param { boolean } [forced] 未获取点数字母对应元素时，若此参数不为false，则返回字符串格式
 * @returns { string }
 */
get.strNumber(num, forced)
get.numString(str, forced) // 返回扑克牌中的表示形式对应的数字
get.cards(num,boolean) // 返回牌堆顶的牌
get.bottomCards(num,boolean) // 返回牌堆底的牌
get.effect()          // 返回收益
get.order()          // 返回优先级
get.value()          // 返回价值
get.is.yingbian()    // 是否能应变
```

### 6.2 技能相关
```javascript
/**
 * 获取一个技能或事件的某个属性的源技能
 * 
 * @param { string | Object } skill - 传入的技能或事件
 * @param { string } text - 要获取的属性（不填写默认获取sourceSkill）
 */
get.sourceSkillFor(skill, text)
```

### 6.3 其他
```javascript
get.isLuckyStar()      // 是否开启幸运星模式
/**
 * 返回指定角色的所有id
 *
 * @param {Player} player
 */
get.nameList()
get.player() // 返回当前事件角色
```

## 7. 窗口(UI)相关API
### 7.1 对话框（Dialog）
```javascript
/**
 * 创建游戏内对话框组件，支持：
 * - 文本/卡牌/角色/按钮/自定义元素
 * 
 * @param {string} title - 对话框标题（支持HTML）
 * @param {Array} content - 内容定义数组，结构为：
 *   [
 *     contentData,   // 内容数据（类型取决于contentType）
 *     contentType    // 内容类型标识符（"vcard"/"character"/"textbutton"等）
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
 * @property {string} vcard - 虚拟卡牌（数据格式：[suit, number, name, natrue]或Cards数组）
 * @property {string} card - 实体卡牌（数据格式：[]Card数组）
 * @property {string} character - 角色信息（数据格式：[]角色名称）
 * @property {string} player - 玩家列表（数据格式：[]对象数组）
 * @property {string} textbutton - 文本按钮（数据格式：[link, text]）
 * @property {string} tdnodes - 表格按钮（数据格式：[]文本数组）
 * @property {string} blank - 卡牌背面（数据格式：[]Card数组）

 */

/**
 * 对话框实例方法
 * @method open() - 打开对话框
 * @method close() - 强制关闭对话框
 * @method add(content) - 添加内容
 * @method setCaption(text) - 设置标题
 * @method addSmall(content) - 添加缩小内容
 * @method addText(text, center) - 添加文本段落（center: 是否居中）
 * @method addNewRow({item,ratio}) - 添加横向按钮行
 * @property {boolean} supportsPagination - 分页支持开关（默认false）
 * @property {Map} paginationMap - 分页控制器映射表（需开启分页）
 * @property {boolean} static - 静态模式开关（禁用关闭）
 */
 ```
