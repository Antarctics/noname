# 第三章 角色制作

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
- 不再推荐使用数组形式
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
    - `clans`：字符串数组。对应的所有宗族
    - `img`：字符串。武将对应的图片
    - `initFilters`：字符串数组。武将无法享受的红利（地主、主公加成）
    - `tempname`：字符串数组。武将的临时名称
    - 更多信息请查看`noname\library\element\character.js`

## 2. 自定义势力<a id="自定义势力"></a>
```javascript
// 在扩展的precontent中添加
lib.group.push('my_group'); // 添加势力
lib.translate.my_group = '自定义'; // 势力翻译
lib.translate.my_groupColor="#FFFF00", // 文字颜色（疑似失效）
lib.groupnature.my_group = 'metal'; // 描边颜色

/** 推荐方法
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

## 4. 角色前缀
```javascript
return {
translates: {
    sheXXX: "蛇年XXX",
    sheXXX_prefix: "蛇年" // 蛇年作为前缀，角色名为 XXX ，可参考 界XX、神XX、手杀XXX等
};

// 修改前缀显示样式
// precontent中填写，支持color,nature,showName,getSpan
lib.namePrefix.set("蛇年",{showName: "🐍"})
// 或者
lib.namePrefix.set("蛇年",{getSpan: () => {
    const span = document.createElement("span");
    span.style.fontFamily= "NonameSuits";
    span.textContent= "🐍";
    return span.outerHTML
    }})
}
```

## 练习题

1. 创建一个基本武将：
    -  设置基本属性
    -  添加技能`wusheng`、`longdan`
    -  设置合适的描述

<details>
<summary>参考答案 | 🟩 Easy</summary>

```javascript
// 在扩展中添加武将
character: {
    character: {
        "ex_guanyu": {
            sex: "male",
            group: "shu",
            hp: 4,
            skills: ["wusheng", "longdan"]
            img: "extension/新关羽/ex_guanyu.png"
            dieAudios: "ext:新关羽/audio/die/ex_guanyu.mp3"
        }
    },
    translate: {
        "ex_guanyu": "新关羽", // 武将翻译
        "ex_guanyu_prefix": "新"
    }
}
```
</details>
</br>
下一节我们将学习如何设计和实现技能。
