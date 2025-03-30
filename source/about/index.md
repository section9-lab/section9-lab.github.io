---
title: me
date: 2025-03-30 02:55:00
categories: 
- web前端
tags: test

---
# test

txt


```js
export default defineContentScript({
  registration: "runtime",
  matches: [],
  cssInjectionMode: "ui",
  async main(ctx) {
    console.log("AI-mail start ... ");
    waitForPageLoad();
    const ui = await createUi(ctx);
    // 检查ui是否存在再调用mount方法
    if (ui) {
      ui.mount();
    }
    return "插件已执行";
  },
});
```