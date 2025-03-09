# 5.5 代码规范

## 1. 代码规范概述

无名杀扩展开发的代码规范包括：
- 命名规范
- 格式规范
- 注释规范
- 结构规范
- 最佳实践

## 2. 命名规范

### 2.1 基本命名
```javascript
import { lib, game, ui, get, ai, _status } from "../../noname.js";
game.import('extension', function(){
    return {
        name: 'my_extension',      // 使用小写字母和下划线
        package: {
            // 武将命名
            character: {
                character: {
                    "ex_zhaoyun": [],      // 前缀区分扩展
                    "ex_sp_zhaoyun": []    // sp等特殊标识
                }
            },
            // 技能命名
            skill: {
                skill: {
                    "ex_longdan": {},      // 扩展前缀
                    "ex_longdan_sha": {}   // 子技能用下划线
                }
            }
        }
    };
});
```

### 2.2 变量命名
```javascript
"skill_name": {
    content(){
        // 常量使用全大写
        const MAX_CARDS = 5;
        
        // 变量使用驼峰命名
        let cardCount = player.countCards('h');
        
        // 布尔值使用is/has前缀
        let isEnabled = true;
        let hasCards = player.countCards('h') > 0;
        
        // 迭代变量使用有意义的名称
        for(let target of targets){
            // 避免使用i,j,k等无意义变量名
        }
    }
}
```

## 3. 格式规范

### 3.1 缩进与空格
```javascript
// 使用Tab缩进
"format_skill": {
    trigger: {
        player: "phaseBegin"    // 对齐冒号
    },
    filter(event, player){
        // 运算符前后加空格
        return player.hp <= 2 && 
               player.countCards('h') > 0;
    },
    content(){
        // 括号内部不加空格
        if(player.isDamaged()){
            player.recover();
        }
    }
}
```

### 3.2 换行与对齐
```javascript
// 长语句换行
"line_skill": {
    content(){
        let result = game.filterPlayer(function(current){
            return current.hp < 2 && 
                   current.countCards('h') > 0 && 
                   !current.hasSkill('some_skill');
        });
        
        // 链式调用换行
        player.chooseTarget()
            .set('prompt', '选择一名角色')
            .set('ai', function(target){
                return get.attitude(player, target);
            });
    }
}
```

## 4. 注释规范

### 4.1 基本注释
```javascript
/**
 * 技能描述
 * @param {Object} event - 触发事件
 * @param {Object} player - 技能拥有者
 * @return {Boolean} 是否满足条件
 */
filter(event, player){
    // 检查体力值
    if(player.hp < 2) return false;
    
    // 检查手牌数
    if(!player.countCards('h')) return false;
    
    return true;
},

// 技能效果
content(){
    /* 多行注释
     * 1. 首先摸牌
     * 2. 然后可能失去体力
     */
    player.draw();
}
```

### 4.2 文档注释
```javascript
// 扩展文档
help: {
    '扩展说明': 
        '### 主要功能</br>' +
        '1. 新增武将</br>' +
        '2. 新增卡牌</br>' +
        '</br>' +
        '### 注意事项</br>' +
        '- 需要本体版本1.0以上</br>',
},

// 技能文档
translate: {
    "skill_name": "技能名",
    "skill_name_info": "技能描述：出牌阶段限一次，你可以...",
    "skill_name_append": "技能补充说明"
}
```

## 5. 结构规范

### 5.1 文件结构
```
extension/
  └── 扩展名/
      ├── extension.js    # 扩展主文件
      ├── info.json       # 扩展信息
      ├── character.js    # 武将代码(可选)
      ├── card.js         # 卡牌代码(可选)
      ├── skill.js        # 技能代码(可选)
      ├── image/          # 图片文件夹
      │   ├── card/       # 卡牌图片
      │   └── character/  # 武将图片
      └── audio/          # 音频文件夹
          ├── die/        # 阵亡配音
          └── skill/      # 技能配音
```

### 5.2 代码结构
```javascript
// 按功能分组
game.import('extension', function(){
    return {
        // 基础配置
        name: 'my_extension',
        edition: '1.0',
        author: 'Author',
        
        // 核心内容
        content(){},
        precontent(){},
        
        // 扩展内容
        character: {},
        card: {},
        skill: {},
        
        // 其他配置
        config: {},
        help: {},
        package: {}
    };
});
```
## 练习

1. 规范化一个现有技能：
   - 检查命名规范
   - 优化代码格式
   - 完善注释文档
```javascript
"wusheng":{
enable:['chooseToUse','chooseToRespond'],
filterCard:function(card){return get.color(card)=='red'},
position:'he',
viewAs:{name:'sha'},
viewAsFilter:function(player){return player.countCards('he',{color:'red'})>0},
prompt:'将一张红色牌当杀使用或打出',
check:function(card){return 4-get.value(card)},
ai:{
respondSha:true,
}
},
```

<details>
<summary>参考答案 | 🟩 Easy</summary>

```javascript
// 优化后
"ex_wusheng": {
    // 武圣：可将红色牌当【杀】使用或打出
    audio: "ext:扩展名/audio/skill:2",
    enable: ["chooseToUse", "chooseToRespond"],
    position: "he",
    filterCard(card){
        return get.color(card) == 'red';
    },
    viewAs: {name: 'sha'},
    viewAsFilter(player){
        return player.countCards('he', {color: 'red'}) > 0;
    },
    prompt: "将一张红色牌当【杀】使用或打出",
    check(card){
        return 4 - get.value(card);
    },
    ai: {
        respondSha: true,
        skillTagFilter(player){
            return player.countCards('he', {color: 'red'}) > 0;
        },
        order: 4,
        useful: -1,
        value: -1
    }
}
```
</details>

至此，我们完成了进阶开发的所有内容。下一章我们将通过实战案例来综合运用所学知识。 