# 费用中心 Tab 切换白屏闪烁问题总结

## 问题现象

费用中心页面嵌入安卓 App 的 WebView 中。在“变更明细”“算粒充值”“使用分析”“订单发票”等 Tab 之间切换时，页面会出现短暂闪烁。

部分手机会出现局部或整页白屏，尤其是顶部蓝色概览区域中的“已抵扣算粒”和“额度状态”区域变白，导致文字无法看清。

该问题并非只发生在“算粒充值”Tab，所有 Tab 切换都可能触发。

## 原因分析

### Tab 切换引起页面布局变化

页面通过 `activeTab` 条件渲染不同的 Tab 内容。切换时会卸载旧内容并挂载新内容，不同 Tab 的 DOM 结构和内容高度不同，可能触发：

```text
Tab 内容替换
→ 页面高度变化
→ 页面重新布局
→ sticky 位置重新计算
→ 相关区域重新绘制和图层合成
```

理论上的受影响区域主要是 `.content`，但异常 WebView 可能将 `.tab-bar-wrap`、`.overview-card` 甚至整个可视区域一起重新合成。因此，用户看到的闪烁或白屏不一定只发生在实际切换内容的下方区域。

### 顶部区域存在复杂 CSS 图层

`index.module.less` 中的顶部区域使用了多种可能增加 WebView 合成复杂度的样式：

- `.overview-card` 的渐变背景和阴影；
- `.overview-card::before`、`.overview-card::after` 的 `filter: blur(...)`；
- `.tab-bar-wrap` 的 `position: sticky`；
- `.tab-bar-wrap` 的半透明背景和 `backdrop-filter`；
- 多层透明背景、渐变和 active 状态阴影。

这些样式在普通浏览器中通常可以正常渲染，但部分 Android WebView、GPU 驱动或厂商 ROM 在布局变化后重新合成图层时可能出现白闪、局部白层或内容不可见。

### 手机型号不是直接根因

问题不应简单归因于“手机太老”。更准确地说，手机年龄可能只是旧 Android System WebView、旧 Chromium 内核、GPU 或厂商图形驱动的间接表现。

可能出现以下情况：

- 老手机更新了 WebView 后恢复正常；
- 新手机使用较旧 WebView 或特定厂商 GPU 时仍然异常；
- 相同页面在不同设备上的 WebView 合成结果不同。

### 当前 CSS 修改的实际边界

已移除 `.sub-metric-card` 前面的一处 `backdrop-filter` 声明，但文件后方的视觉刷新样式中已经存在：

```less
-webkit-backdrop-filter: none;
backdrop-filter: none;
```

因此该删除可能没有改变最终生效样式，不能据此认定问题已经解决。

## 解决方案

### 第一阶段：降低顶部图层合成复杂度

优先移除或降级以下样式，并在问题设备上逐项验证：

1. 移除 `.tab-bar-wrap` 的 `backdrop-filter`；
2. 移除 `.overview-card::before`、`.overview-card::after` 的 `filter: blur(...)`；
3. 将半透明背景改为纯色或高不透明度背景；
4. 必要时将复杂渐变和阴影替换为简单样式。

### 第二阶段：减少 Tab 切换时的布局波动

如果简化 CSS 后仍然存在闪烁，再考虑：

- 给内容区设置稳定的最小高度；
- 减少切换时的页面高度突变；
- 保留 Tab 容器结构，仅切换内容显示状态。

### 第三阶段：核对安卓运行环境

在出现问题的设备上记录：

- Android 系统版本；
- Android System WebView 版本；
- Chrome 版本；
- 手机型号和 GPU；
- 是否只有首次切换时闪烁；
- 更新 WebView 后是否恢复正常。

## 验证状态

- 已移除一处顶部子卡片的 `backdrop-filter` 声明；
- `npm run build` 已通过；
- `git diff --check` 已通过；
- `npm run lint` 受项目已有 8 个错误影响，未通过；
- 尚未在实际安卓 App WebView 设备上完成视觉验收；
- 当前不能确认问题已经完全解决。

## 结论

当前最合理的结论是：

> Tab 切换引起页面布局变化，触发顶部渐变、透明、模糊、阴影和 sticky 图层重新绘制或合成；部分 Android WebView 或 GPU 合成实现兼容性不足，导致顶部闪烁、局部白屏或整页白屏。

因此，后续应优先简化 `.tab-bar-wrap` 和 `.overview-card` 的复杂图层效果，再通过问题安卓设备进行验证。
