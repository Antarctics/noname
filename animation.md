# 4.6 技能动画

## 1. 动画系统概述

无名杀的动画系统仅有动效，而无实际效果，总计包括:
- 技能发动动画
- 特效动画
- 卡牌动画
- 状态动画

## 2. 技能发动动画

### 2.1 基础动画
```javascript
"animation_skill": {
    // 技能动画
    skillAnimation: true,           // 开启技能动画
    animationStr: "技能发动",      // 动画文字
    animationColor: "fire",        // 动画颜色(fire/thunder/water/metal/soil)
    
    content(){
        // 技能效果
    }
}
```

### 2.2 自定义动画
```javascript
"custom_animation": {
    async content(event, trigger, player){
        // 播放指定技能动画
        player.$skill('技能名');
        
        // 自定义动画效果
        player.$fire();            // 火焰效果
        player.$thunder();         // 雷电效果
        player.$fullscreenpop('文字', 'fire'); // 全屏特效
    }
}
```

## 3. 特效动画

### 3.1 基础特效
```javascript
async content(event, trigger, player){
    // 基础特效
    player.$gain(cards);          // 获得牌动画
    player.$give(num, target);    // 给牌动画
    player.$throw(cards);         // 弃牌动画
    
    // 状态特效
    player.$damage('fire');       // 受伤特效
    player.$recover();            // 回复特效
    player.$shield();             // 护盾特效
}
```

### 3.2 高级特效
```javascript
"effect_skill": {
    async content(event, trigger, player){
        // 连续特效
        player.$fire();
        await game.delay(0.5);
        player.$thunder();
        
        // 多目标特效
        for(let target of targets){
            target.$damage('fire');
            await game.delay(0.2);
        }
    }
}
```

## 4. 卡牌动画

### 4.1 使用动画
```javascript
"card_animation": {
    async content(event, trigger, player){
        // 卡牌使用动画
        player.$useCard(card, targets);
        
        // 卡牌打出动画
        player.$throw(card);
        
        // 卡牌获得动画
        player.$draw(num);
        player.$gain(cards);
    }
}
```

### 4.2 特殊动画
```javascript
"special_card": {
    async content(event, trigger, player){
        // 判定动画
        player.$judge(card);
        
        // 展示动画
        player.$showCards(cards);
        
        // 比较动画
        player.$compare(card1, target, card2);
    }
}
```

## 5. 状态动画

### 5.1 基础状态
```javascript
"state_animation": {
    async content(event, trigger, player){
        // 翻面动画
        player.$turnOver();
        
        // 横置动画
        player.$link();
        
        // 濒死动画
        player.$die();
    }
}
```

### 5.2 标记动画
```javascript
"mark_animation": {
    async content(event, trigger, player){
        // 添加标记动画
        player.$mark('标记名', {
            name: '标记名称',
            content: '标记描述'
        });
        
        // 移除标记动画
        player.$removeMark('标记名');
    }
}
```

## 6. 进阶技巧

### 6.1 动画序列
```javascript
"sequence_animation": {
    async content(event, trigger, player){
        // 创建动画序列
        await player.$fire();
        await game.delay(0.5);
        await player.$thunder();
        await game.delay(0.3);
        await player.$fullscreenpop('技能发动', 'fire');
    }
}
```

### 6.2 条件动画
```javascript
"condition_animation": {
    async content(event, trigger, player){
        // 根据条件播放不同动画
        if(player.hp < 3){
            await player.$fire();
        } else {
            await player.$thunder();
        }
        
        // 动态特效
        let type = player.hp < 3 ? 'fire' : 'thunder';
        await player.$damage(type);
    }
}
```

</br>
下一章我们将以无名杀源码实例展示所有技能类型。  

若无需回顾技能类型，可以直接学习  
- [第四章：卡牌开发 | 🟩 Easy](../chapter4-card.md)
