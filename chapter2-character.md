# 2.1 角色基础属性

## 1. 角色定义格式

在无名杀中，角色的基本定义格式如下：

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
   - 建议使用英文字母、数字、下划线
   - 建议使用有意义的命名，如 `zhaoyun`、`sp_zhugeliang`
   - 不能与现有角色ID重复

2. **sex**: 性别
   - `"male"`: 男性
   - `"female"`: 女性
   - `"none"`: 无性别

3. **group**: 势力
   - `"wei"`: 魏国
   - `"shu"`: 蜀国
   - `"wu"`: 吴国
   - `"qun"`: 群雄
   - `"jin"`: 晋国
   - `"shen"`: 神将
   - 也可以[自定义势力](#自定义势力)，需要额外设置

4. **hp**: 体力值
   - 可以是字符串形式的分数，如 `"3/3"`（体力上限/体力值）
   - 可以带有额外标记，如 `"3/3/2"`（体力上限/体力值/护甲值）

5. **skills**: 技能列表
   - 数组形式，使用技能ID

6. **tags**: 特殊标签列表
   - `"des:xxx"`: 角色描述
   - `"ext:xxx/xxx.jpg"`: 角色图片路径
   - `"die:xxx.mp3"`: 阵亡配音
   - `"hiddenSkill"`: 隐藏技能
   - `"zhu"`: 主公武将
   - `"boss"`: Boss武将
   - `"forbidai"`: 禁止AI使用
   - `"unseenn"`: 不可见
   - `"doublegroup:xxx:xxx"`: 多势力标签
   - 其他自定义标记

## 2. 实例演示

### 2.1 基本武将
```javascript
// 标准包赵云
"zhaoyun": ["male", "shu", 4/4, ["longdan"], [
    "des:赵云，字子龙，常山真定人。身长八尺，姿颜雄伟。",
    "ext:my_extension/zhaoyun.jpg",
    "die:ext:my_extension/audio/die/zhaoyun.mp3"
]], // 定义一个普通武将赵云

// 神将诸葛亮
"shen_zhugeliang": ["male", "shen", 3/3, ["qixing", "kuangfeng", "dawu"], [
    "des:神诸葛亮，字孔明，号卧龙。",
    "ext:my_extension/shen_zhugeliang.jpg",
    "die:ext:my_extension/audio/die/shen_zhugeliang.mp3"
]], // 定义一个神将诸葛亮
```

### 2.2 带护甲的武将
```javascript
// 谋曹仁
"mou_caoren": ["male", "wei", "4/4/2", ["sb_jushou"], [
    "des:字子孝，曹操的从弟。以刚毅著称，善于守城。",
    "ext:my_extension/mou_caoren.jpg"
]], // 定义一个带护甲值的武将曹仁
```

### 2.3 双势力武将
```javascript
// SP马超
"sp_machao": ["male", "shu", 4/4, ["zhuiji", "ol_shichou"], [
    "des:字孟起，扶风茂陵人。五虎上将之一。",
    "ext:my_extension/sp_machao.jpg",
    "doublegroup:qun:shu"
]], // 定义一个双势力武将马超
```

## 3 自定义势力<a id="自定义势力"></a>
```javascript
// 在扩展precontent中添加
lib.group.push('my_group'); // 添加势力
lib.translate.my_group = '自定义'; // 势力翻译
lib.translate.my_groupColor="#FFFF00", // 文字颜色
lib.groupnature.my_group = 'metal'; // 描边颜色

// 标准势力的描边颜色对应
// 神: shen     - 金色
// 魏: water    - 蓝色
// 蜀: soil     - 黄色
// 吴: wood     - 绿色
// 群: qun      - 白色
// 晋: thunder  - 紫色
// 键: key      - 紫色

// 使用自定义势力
"my_character": ["male", "my_group", 4/4, ["my_skill"], []], // 使用自定义势力的武将
```

## 4. 注意事项

1. **角色ID命名规范**
   - 建议使用前缀区分不同扩展，如 `ex_xxx`
   - 尽量避免使用特殊字符
   - 注意检查ID是否重复

2. **图片文件要求**
   - 推荐尺寸：687x472像素
   - 格式：jpg或png
   - 文件名应与路径配置一致

3. **音频文件要求**
   - 格式：mp3
   - 建议大小：<100KB
   - 音质：建议96kbps以上

## 练习题

1. 创建一个基本武将：
   - 设置基本属性
   - 添加一个技能
   - 设置合适的描述

<details>
<summary>参考答案 | 🟩 Easy</summary>

```javascript
// 在扩展中添加武将
character: {
    character: {
        "ex_guanyu": ["male", "shu", 4/4, ["wusheng"], [
            "des:字云长，五虎上将之首，以忠义著称。",
            "ext:my_extension/image/ex_guanyu.jpg",
            "die:ext:my_extension/audio/die/ex_guanyu.mp3"
        ]], // 定义关羽武将
    },
    translate: {
        "ex_guanyu": "关羽", // 武将翻译
    }
}
```
</details>

2. 创建一个双势力武将：
   - 使用两个不同的势力
   - 添加对应的技能
   - 设置特殊标记

<details>
<summary>参考答案 | 🟩 Easy</summary>

```javascript
character: {
    character: {
        "ex_lvbu": ["male", "qun", 5/5, ["wushuang", "liyu"], [
            "des:字奉先，武艺超群，号称“飞将”。",
            "ext:my_extension/image/ex_lvbu.jpg",
            "die:ext:my_extension/audio/die/ex_lvbu.mp3",
            "doublegroup:qun:wei" // 双势力标记
        ]], // 定义吕布武将
    },
    translate: {
        "ex_lvbu": "吕布", // 武将翻译
    }
}
```
</details>

3. 创建一个带护甲的武将：
   - 设置初始护甲值
   - 添加护甲相关技能
   - 完善武将描述

<details>
<summary>参考答案 | 🟩 Easy</summary>

```javascript
character: {
    character: {
        "ex_dianwei": ["male", "wei", "4/4/2", ["qiangxi"], [
            "des:字曼成，陈留己吾人，曹操的贴身护卫。",
            "ext:my_extension/image/ex_dianwei.jpg",
            "die:ext:my_extension/audio/die/ex_dianwei.mp3"
        ]], // 定义典韦武将
    },
    translate: {
        "ex_dianwei": "典韦", // 武将翻译
    }
}
```
</details>
</br>
下一节我们将学习如何设计和实现技能。