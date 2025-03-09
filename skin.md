# 5.4 角色皮肤

## 1. 皮肤系统概述

无名杀的皮肤系统允许为武将设置多个不同的皮肤，每个皮肤可以包含：
- 皮肤图片
- 专属配音
- 死亡皮肤
- 皮肤描述

## 2. 基础皮肤设置

### 2.1 皮肤定义
```javascript
// 以此法添加的皮肤只能使用代码更换。

// 定义武将皮肤
characterSubstitutes: {
    "my_general": [
        ["my_general2", ["ext:我的扩展/image/my_general2.png"]], // 皮肤2
        ["my_general3", ["ext:我的扩展/image/my_general3.png"]], // 皮肤3
    ],
};
```

### 2.2 皮肤文件结构
```
extension/
  └── 扩展名/
      └── image/
          ├── my_general.png     # 默认皮肤
          ├── my_general2.png    # 皮肤2
          └── my_general3.png    # 皮肤3
```

### 2.3 本体换肤方法
- 于本体image文件夹中新建skin文件夹。
- 于skin中新建对应角色ID的文件夹
- 与角色ID文件夹中按照顺序以数字命名

#### 皮肤文件结构
```
image/
  └── skin/
      └── 角色名/
          ├── l.jpg    # 皮肤1
          ├── 2.jpg    # 皮肤2
          └── 3.jpg    # 皮肤3
```
## 3. 皮肤配音

### 3.1 皮肤专属配音
```javascript
skill: {
    "my_skill": {
        audio: "ext:我的扩展/audio/skill/my_skill",
        // 皮肤专属配音重定向
        audioname2: {
            "my_general2": "ext:我的扩展/audio/skill/my_skill2",
            "my_general3": "ext:我的扩展/audio/skill/my_skill3",
        },
        content(){
            // 技能内容
        }
    }
}
```

### 3.2 推荐音频文件结构
```
extension/
  └── 扩展名/
      └── audio/
          └── skill/
              ├── my_skill.mp3   # 默认配音
              ├── my_skill2.mp3  # 皮肤2配音
              └── my_skill3.mp3  # 皮肤3配音
```

</br>
下一章我们学习代码规范