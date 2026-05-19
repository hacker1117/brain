---
type: concept
title: parseInt(value) || default：数字配置项的 Falsy Zero 陷阱
date: '2026-05-14T00:00:00.000Z'
tags:
  - antipattern
  - configuration
  - debugging
  - gbrain
  - javascript
---

# `parseInt(value) || default`：数字配置项的 Falsy Zero 陷阱

## 发现背景

在调试 [[dream-cycle-summaries/2026-05-14]] 的 synthesize cooldown 时，发现 GBrain 代码里有如下模式：

```js
const cooldownHours = parseInt(configValue) || 12;
```

当 `configValue` 被设为 `"0"`（意图：不冷却）时，`parseInt("0")` 返回 `0`，而 `0 || 12` 返回 `12`，于是配置完全失效，系统静默地回落到默认值。

## 为什么这个 bug 特别难发现

1. **静默回落**：没有报错，没有警告，系统正常运行——只是用了错误的配置值
2. **语义歧义**：`0` 在"数量"语义下是有效值（零冷却），但在 JS truthy 语义下是 falsy
3. **配置 debug 困难**：你改了配置，重跑，行为没变，很容易以为是"别的地方的问题"

## 正确写法

```js
// ✅ 用 null/undefined 检查，不用 truthy 检查
const cooldownHours = configValue != null ? parseInt(configValue) : 12;

// ✅ 或者用 Number.isFinite 保护
const parsed = parseInt(configValue);
const cooldownHours = Number.isFinite(parsed) ? parsed : 12;
```

## 临时绕过方案（已记录）

当代码无法立即修改时，可将配置值设为 `-1`，利用 `Math.max(0, parseInt("-1"))` = `0` 绕过 falsy 判断，同时让 `Math.max` 将负值截断为 0。这是一个"已知 bug 的已知绕过"，应标记为 tech debt。

## 更广泛的模式

这是"用 JS logical OR 做默认值"这个历史模式的经典陷阱。在 ES2020+ 中，应使用 nullish coalescing：

```js
const cooldownHours = parseInt(configValue) ?? 12;
// 只有 null/undefined 才回落，0 保持为 0 ✅
```

---

*Discovered during: [[wiki/personal/reflections/2026-05-14-first-live-dream-run-debugging-eebd7d]]*
*Related infrastructure work: [[wiki/personal/patterns/gbrain-as-ongoing-system-building-project]]*
