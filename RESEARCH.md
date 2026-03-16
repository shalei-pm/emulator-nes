# NES 模拟器项目 - 版本存档

> 最后更新：2026-03-15

---

## 当前版本：v5.1

### 版本说明
- **文件**：`index.html`
- **核心功能**：基础 NES 模拟器 + A/B 键连发
- **状态**：连发代码已实现，但未验证是否有效

### 关键代码片段

#### 1. 按键映射
```javascript
var keyMap = {
  'g': 'A', 'h': 'B', '2': 'SELECT', '1': 'START',
  'w': 'UP', 's': 'DOWN', 'a': 'LEFT', 'd': 'RIGHT'
};
```

#### 2. 连发逻辑（gameLoop 中）
```javascript
var frameCount = 0;
var turboKeys = { 'g': false, 'h': false }; // 记录连发键状态

function gameLoop() {
  if (nes) {
   // 连发逻辑：每3帧触发一次
   if (frameCount % 3 === 0) {
    // A键连发
    if (holdingKeys['g']) {
     if (turboKeys['g']) {
      nes.buttonUp(1, 'A');
      turboKeys['g'] = false;
     } else {
      nes.buttonDown(1, 'A');
      turboKeys['g'] = true;
     }
    }
    // B键连发
    if (holdingKeys['h']) {
     if (turboKeys['h']) {
      nes.buttonUp(1, 'B');
      turboKeys['h'] = false;
     } else {
      nes.buttonDown(1, 'B');
      turboKeys['h'] = true;
     }
    }
   }
   
   nes.frame();
   frameCount++;
  }
  requestAnimationFrame(gameLoop);
}
```

#### 3. 按键事件处理
```javascript
document.addEventListener('keydown', function(e) {
  if (!nes) return;
  var key = e.key.toLowerCase();
  var btn = keyMap[key];
  if (btn) {
   if (nes.controllers[1].state) {
    nes.controllers[1].state[jsnes.Controller['BUTTON_' + btn]] = true;
   } else {
    nes.buttonDown(1, btn);
   }
   holdingKeys[key] = true;
   e.preventDefault();
  }
});

document.addEventListener('keyup', function(e) {
  if (!nes) return;
  var key = e.key.toLowerCase();
  var btn = keyMap[key];
  if (btn) {
   if (nes.controllers[1].state) {
    nes.controllers[1].state[jsnes.Controller['BUTTON_' + btn]] = false;
   } else {
    nes.buttonUp(1, btn);
   }
   holdingKeys[key] = false;
  }
});
```

---

## 历史版本

### v5.0 - 官方TURBO连发
- 尝试使用 jsnes 内置 TURBO 功能
- 状态：无效

### v3.9t - 精确复制v3.9
- 早期稳定版本
- 按键使用 `nes.controllers[1].state[]` 直接赋值

---

## v6.0 - EmulatorJS 版本（测试中）

### 版本说明
- **文件**：`index_v6_emulatorjs.html`
- **核心**：EmulatorJS (fceumm 核心)
- **优势**：支持更多 NES Mapper，兼容性好
- **状态**：待测试

### 使用方式
1. 打开 index_v6_emulatorjs.html
2. 上传 ROM 文件
3. 使用默认按键或自定义

### 按键配置
- W/S/A/D: 上下左右
- G: A
- H: B
- 1: Start
- 2: Select
- EmulatorJS 默认也支持游戏手柄

### 参考
- EmulatorJS 官网: https://emulatorjs.org/
- CDN: https://cdn.emulatorjs.org/stable/

---

## 待验证
- [ ] 连发功能是否正常工作
- [ ] 不同频率（2帧 vs 3帧）的效果对比

---

## 下次修改建议
1. 先查看此存档，了解当前代码结构
2. 基于 v5.1 版本修改，不要重新写
3. 每次修改后更新此存档
