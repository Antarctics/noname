# 6.2 联机适配

## 1. 联机系统概述

无名杀的联机系统需要考虑：
- 数据同步
- 事件处理
- 资源加载
- 性能优化
- 错误处理

## 2. 数据同步

```javascript
"sync_skill": {
    async content(event, trigger, player){
        // 同步玩家数据(联机模式下全局调用的数据均需同步)
        game.broadcastAll(function(player, num){
            player.storage.count = num;
        }, player, 1);
        
        // 同步动画效果
        game.broadcastAll(function(player){
            player.$draw();
        }, player);
        
    }
}
```


## 3. 事件处理

### 3.1 事件同步
```javascript
"event_skill": {
    trigger: {player: 'phaseBegin'},
    async content(event, trigger, player){
        // 创建同步事件
        var next = game.createEvent('customEvent');
        next.player = player;
        next.setContent(function(){
            // 事件内容
            player.draw();
        });
        
        // 等待事件完成
        await next;
    }
}
```

### 3.2 选择同步
```javascript
"choice_skill": {
    async content(event, trigger, player){
        // 同步选择结果
        let result = await player.chooseControl('选项1', '选项2')
            .set('prompt', '请选择一个选项')
            .set('ai', ()=>{
                return '选项1';
            })
            .forResult();
            
        // 广播选择结果
        game.broadcastAll(function(player, choice){
            player.storage.choice = choice;
        }, player, result.control);
    }
}
```

## 练习

1. 创建一个基础联机技能：
   - 实现数据同步

<details>
<summary>参考答案 | 🟩 Easy</summary>

```javascript
"online_skill": {
    enable: "phaseUse",
    usable: 1,
    filter(event, player){
        return player.countCards('h') > 0;
    },
    async content(event, trigger, player){
        // 选择目标
        let result = await player.chooseTarget('选择一名角色')
            .set('ai', target => get.attitude(player, target))
            .forResult();

        if(result.bool){
            let target = result.targets[0];
            
            // 同步选择结果
            game.broadcastAll(function(player, target){
                player.line(target);
            }, player, target);
            
            // 处理效果
            await target.draw();
            
            // 同步状态
            game.broadcast(function(player, target){
                player.storage.used = true;
                target.update();
            }, player, target);
        }
    }
}
```
</details>


下一节我们将学习性能优化。 