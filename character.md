# 第三章 角色创建

## 1. 角色定义格式

在无名杀中，角色的基本定义格式如下：
### 对象形式（推荐）
- 本教程将以对象形式为主！

```javascript
character: {
    id: {
        sex: "male",
        group: "qun",
        hp: 3,
        skills: ["skill1", "skill2"],
        doubleGroup: ["wei", "qun"],
    },
},
translate: {
    "id": "武将名称",
}
```

### 数组形式（传统）
- 不再推荐使用的数组形式
```javascript
character: {
    "id": ["male", "shu", 4/4, ["skill1", "skill2"], [
        "des:武将描述",
        "ext:my_extension/武将图片.jpg",
        "die:ext:my_extension/audio/die/die_audio.mp3"
    ]],
},
translate: {
    "id": "武将名称",
}
```


### 1.1 参数说明

1. **id**: 角色的唯一标识符
    -  建议使用英文字母、数字、下划线
    -  建议使用有意义的命名，如 `zhaoyun`、`sp_zhugeliang`
    -  游戏内角色首字母会读取`_`后的首个字母
    -  不能与现有角色ID重复

2. **sex**: 性别
    - 字符串：
    -  `"male"`: 男性
    -  `"female"`: 女性
    -  `"none"`: 无性别

3. **group**: 势力
    - 字符串：
    -  `"wei"`: 魏国
    -  `"shu"`: 蜀国
    -  `"wu"`: 吴国
    -  `"qun"`: 群雄
    -  `"jin"`: 晋国
    -  `"shen"`: 神将
    -  也可以[自定义势力](#自定义势力)，需要额外设置

4. **hp**: 体力值
    -  数字，角色的初始体力

5. **maxHp**: 体力上限
    -  数字，角色的初始体力上限
  
6. **hujia**: 护甲
    -  数字，角色的初始护甲

7. **skills**: 技能列表
    -  数组，使用技能ID

8. **isZhugong**: 常驻主
    -  布尔值，默认为false

9.  **dieAudios**: 阵亡配音
    - 字符串、文本、布尔值、数组，具体用法请查看[配音系统](audio.md)。

10. **tags**: 特殊标签列表
    - `names`：字符串。武将姓名，无法替代翻译，仅用于判断姓、名分别是什么
      - `夏侯|null`：夏侯氏
      - `司马|懿`：司马懿
      - `关|兴-张|苞`：关兴和张苞，双头将 
    - `groupBorder`：字符串。边框色，可以实现身在曹营心在汉（势力`wei`,边框色`shu`）
    - `groupInGuozhan`：字符串。神牌在国战模式下的势力
    - `isUnseen `：布尔值。是否隐藏武将，默认为false 
    - `hasHiddenSkil`：布尔值。是否隐匿技能，默认为false
    - `dualSideCharacter`：字符串。双面武将牌，即翻面后切换至另一武将，需双方武将均持有`dualside`技能
    - `doubleGroup`：字符串数组。多势力武将。
    - `isAiForbidden`：布尔值。是否仅点将可用，默认为false
    - `extraModeData`：数组。特殊模式下读取的信息。 
    - `clans`：字符串数组。对应的宗族
    - `img`：字符串。武将对应的图片
    - `initFilters`：字符串数组。武将无法享受的红利（`noZhuHp`、`noZhuSkill`）
    - `tempname`：字符串数组。武将的临时名称
    - 更多信息请查看`noname\library\element\character.js`

## 2. 自定义势力<a id="自定义势力"></a>
```javascript
/**
 * 旧方法（不推荐）
 * 在扩展的precontent中添加 
 */ 
lib.group.push('my_group'); // 添加势力
lib.translate.my_group = '自定义'; // 势力翻译
lib.groupnature.my_group = 'metal'; // 描边颜色

/** 
 * 推荐方法
 * @param {string} id: 势力ID
 * @param {string} short: 势力名称，单字
 * @param {string} name: 势力全名，使用 get.translation(id2)可以获取，不填默认为short
 * @param {object} config:势力配置，支持color与image两种参数。
 * 
 */
game.addGroup(id,short,name,config)

// 标准势力的描边颜色对应
// 神: shen      -  金色
// 魏: water     -  蓝色
// 蜀: soil      -  黄色
// 吴: wood      -  绿色
// 群: qun       -  白色
// 晋: thunder   -  紫色
// 键: key       -  紫色
```

## 3. 角色前缀
```javascript
translates: {
    sheXXX: "蛇年XXX",
    sheXXX_prefix: "蛇年" // 蛇年作为前缀，角色名为 XXX ，可参考 界XX、神XX、手杀XXX等
}
// 修改前缀显示样式
// 支持color,nature,showName,getSpan
lib.namePrefix.set("蛇年",{showName: "🐍"})
// 或者
lib.namePrefix.set("蛇年",{getSpan: () => {
    const span = document.createElement("span");
    span.style.fontFamily= "NonameSuits";
    span.textContent= "🐍";
    return span.outerHTML
    }})
```

## 4. 角色筛选

有时候我们需要限制武将在特定模式下才可使用，或者在某些模式下无法获得主公加成。

```javascript
// 判断武将是否可在当前模式中使用
characterFilter: {
    /**
     * @param {string} mode - 游戏模式标识，例如 "identity"（身份模式）、"guozhan"（国战模式）等
     */
    guanyu(mode) => mode != "identity"
}

// 判断武将是否可在当前模式中获得加成
characterInitFilters: {
	/**
	 * @param {string} tag - 标签，例如 "noZhuSkill" 表示禁止主公技相关加成
	 * 
	 * 说明：当标签为 "noZhuSkill" 时，仅在以下两种情况下允许加成：
	 *       1. 当前模式为斗地主模式（doudizhu）
	 *       2. 当前子模式为 normal
	 *       否则返回 false，禁止加成（即斗地主 normal 子模式以外的情况都不给加成）
	 */
	zhaoyun(tag) {
		if (tag == "noZhuSkill" && (get.mode() != "doudizhu" || _status.mode != "normal")) {
			return false;
		}
	}
}
```

## 5. 换音换肤

无名杀允许同一武将拥有多种外观和音效方案，且拥有两种效果截然不同的方式：

### 方法一
这种方式适合在游戏过程中，通过技能效果临时改变武将的外观，例如觉醒、变身等场景，具有以下效果：
 - 通过技能效果对皮肤进行切换
 - 无法主动切换皮肤
 - 可同步切换音效

第一步、创建皮肤配置
首先需要在武将定义中添加皮肤配置：

```javascript
/**
 * 武将皮肤/阵亡语音替换
 * key 为武将名称，value 为皮肤配置数组
 * 
 * 数组元素格式：[skinName, [skinPath/ID,dieAudio]]
 * 示例：
 * - ["guanyu", ["wuguanyu", "die:wuguanyu"]]        // 内部ID
 * - ["guanyu", ["ext:我的扩展/skin/wuguanyu", "die:ext:我的扩展/audio/die/wuguanyu"]]  // 路径
 */
characterSubstitute: {
    guanyu:[
        ["wu_guanyu",["wuguanyu","die:wuguanyu"]]
    ]
}
```

第二步、切换皮肤
通过技能效果来进行皮肤切换：

```javascript
/**
 * 切换角色皮肤
 * @param {string|Object} map - 切换规则
 *   - 若为string：视为技能名，将所有持有该技能的角色皮肤替换为 skinName
 *   - 若为Object：精确指定切换对象，包含以下字段：
 *     @prop {string} skill - 触发切换的技能名
 *     @prop {string} characterName - 武将ID
 *     @prop {string} characterSkinName - 目标皮肤ID/路径
 *     @prop {string} [source] - 来源武将（国战模式生效）
 * @param {string} skinName - 目标皮肤ID/路径
 * 
 * @example
 * // 将所有持有"juexing_skill"技能的角色皮肤切换为武关羽
 * player.changeSkin("juexing_skill", "wu_guanyu");
 * 
 * @example
 * // 精确切换指定角色皮肤
 * player.changeSkin({
 *     characterName: "guanyu",
 * }, "wu_guanyu");
 */
```

第三步、独立音效

不同皮肤可以拥有不同的音效，其中阵亡音效由皮肤配置进行设置，技能音效需要对技能进行单独改动。具体用法请查看[角色专属配音](audio.md#角色专属配音)。

### 方法二
这种方式适合玩家主动选择的皮肤，不影响游戏性，纯视觉效果。具有以下效果：
 - 随时可切换
 - 仅对玩家生效
 - 无实际修改

在扩展文件中按以下格式创建文件夹，并将皮肤文件置入即可自动识别。

```文件结构
扩展文件/
├── image/
│   └── skin/
│       └── guanyu/           # 武将ID文件夹
│           └── skin.png      # 皮肤文件
```

## 6. 其他配置

### 6.1 角色头衔

```javascript
characterTitle: {
    "ex_guanyu": "汉寿亭侯",
    "ex_zhangfei": "西乡侯"
}
```

### 6.2 角色切换

```javascript
characterReplace: {
    guanyu: ["jie_guanyu","wu_guanyu"] // 于选择角色界面可在“界关羽”与“武关羽”之间切换。
}
```

### 6.3 角色分包

```javascript
characterSort: {
    // 扩展内部名
    ext_name: {
        yijiang: ["guanyu","zhaoyun"] // 将“关羽”、“赵云”加入“yijiang”包中
    }
}
```

下一节我们将学习如何设计和实现技能。
