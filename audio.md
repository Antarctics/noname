# 5.3 音频与配音

## 1. 音频系统概述

无名杀的音频系统主要包含以下几个部分：
- 技能配音
- 死亡配音
- 皮肤专属配音
- 卡牌音效

## 2. 技能配音

### 2.1 基础配音设置
```javascript
skill: {
    "my_skill": {
        audio: 2,                    // 有2个配音文件("skill1.mp3", "skill2.mp3")
        // 或
        audio: "ext:扩展名/audio/skill:2",  // 从扩展目录读取以数字后缀命名的音频（"ext:扩展名/audio/skill/my_skill1.mp3", "ext:扩展名/audio/skill/my_skill2.mp3"）
        // 或
        audio: true,                 // 只有1个配音文件("skill.mp3")
        // 或
        audio: ["dclingxi",2]                 // 使用技能【灵犀】的两条语音
        // 技能内容...
    }
}
```

### 2.2 配音文件命名规则
- 基础命名：`技能ID + 数字`
- 示例：
  - `my_skill1.mp3`
  - `my_skill2.mp3`

### 2.3 角色专属配音
```javascript
"my_skill": {
    audio: 2,
    
    audioname: ["zhaoyun", "machao"],   // 赵云和马超使用专属配音
    // 或
    audioname2: {
        zhaoyun: "ext:扩展名/audio/skill:1",  // 赵云使用扩展配音
        machao: true                     // 马超使用默认配音
    }
}
```

## 3. 死亡配音

```javascript
character: {
    id: {
        sex: "male",
        group: "qun",
        hp: 3,
        skills: ["skill1", "skill2"],
        doubleGroup: ["wei", "qun"],
        dieAudios: ["ext:扩展名/audio/die/my_general1.mp3","ext:扩展名/audio/die/my_general2.mp3"]
    },
},
```

## 4. 配音台词

### 4.1 技能台词
```javascript
// 在translate中添加
translate: {
    "my_skill": "技能名",
    "my_skill_info": "技能描述",
    "#my_skill1": "发动台词1",
    "#my_skill2": "发动台词2",
    "#ext:扩展名/audio/skill/my_skill1": "发动台词1", // 使用扩展目录配音
    "#ext:扩展名/audio/skill/my_skill2": "发动台词2"  // 使用扩展目录配音
}
```

### 4.2 死亡台词
```javascript
translate: {
    "my_general:die": "死亡台词",
    "#ext:acg/audio/die/my_general:die": "死亡台词" // 使用扩展目录配音
}
```

## 5. 推荐目录结构
```
extension/
  └── 扩展名/
      └── audio/
          ├── skill/           # 技能配音
          │   ├── my_skill1.mp3
          │   └── my_skill2.mp3
          ├── die/            # 死亡配音
          │   └── my_general.mp3
          └── skin/           # 皮肤配音
              └── my_general/
                  └── skin_audio.mp3
```

## 6. 进阶技巧

### 6.1 条件配音
```javascript
"my_skill": {
    audio(player){
        // 根据条件返回不同的配音设置
        if(player.hp <= 2) return "ext:扩展名:2";
        return true;
    }
}
```

### 6.2 动态配音
```javascript
"my_skill": {
    audio: "ext:扩展名:2",
    content(){
        // 手动播放配音
        game.playAudio('..', 'extension', '扩展名', 'my_skill' + (_status.event.num || 1));
    }
}
```

## 练习

1. 为技能添加配音：
   - 添加基础配音文件
   - 设置触发台词
   - 测试不同角色专属配音

<details>
<summary>参考答案 | 🟩 Easy</summary>

```javascript
// 在扩展中添加技能配音
skill: {
    "my_skill": {
        // 基础配音
        audio: "ext:我的扩展/audio:2", // 有两个配音文件
        
        // 角色专属配音
        audioname: ["zhaoyun", "machao"], // 赵云和马超使用专属配音
        audioname2: {
            zhaoyun: "ext:我的扩展/audio/zhaoyun/skill:2", // 赵云专属配音
            machao: "ext:我的扩展/audio/machao/skill:2"  // 马超专属配音
        },
        
        content(){
            // 技能内容
        }
    }
},
translate: {
    "my_skill": "技能名",
    "my_skill_info": "技能描述",
    "#my_skill1": "发动台词1",
    "#my_skill2": "发动台词2",
    "#ext:我的扩展/audio/zhaoyun/skill1": "赵云专属台词1",
    "#ext:我的扩展/audio/zhaoyun/skill2": "赵云专属台词2",
    "#ext:我的扩展/audio/machao/skill1": "马超专属台词1",
    "#ext:我的扩展/audio/machao/skill2": "马超专属台词2"
}

// 目录结构
extension/
  └── 我的扩展/
      └── audio/
          ├── skill/
          │   ├── my_skill1.mp3  // 基础配音1
          │   └── my_skill2.mp3  // 基础配音2
          ├── zhaoyun/
          │   ├── skill1.mp3     // 赵云专属配音1
          │   └── skill2.mp3     // 赵云专属配音2
          └── machao/
              ├── skill1.mp3     // 马超专属配音1
              └── skill2.mp3     // 马超专属配音2
```
</details>

下一节我们将学习如何修改武将皮肤。 