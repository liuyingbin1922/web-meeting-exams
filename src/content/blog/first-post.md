---
title: 'web front end quesitons'
description: 'Lorem ipsum dolor sit amet'
pubDate: 'July 08 2025'
heroImage: '../../assets/first-blog.png'
---

下面是一个 **≥1 小时的字节跳动（ByteDance）高级前端工程师面试流程示例**，结合假设简历重点（如工程化、缓存/二次缓存、SSR、大型框架、性能、安全），在真实面经基础上，进行**多轮深入追问**，并提供**5 道高难度代码题**，侧重测试基础功与系统设计。

---

## 🎙️ 第一部分：项目与架构深挖（约15–20 分钟）

1. **简历中提到“大型低代码平台”**

   * Q：低代码平台的核心架构是怎样的？你负责哪些模块？
   * **追问**：如何做组件懒加载、按需渲染、样式隔离（Shadow DOM/module CSS）？
   * **追问**：团队协作时，如何保证组件版本一致？有无用到 module federation 或类似机制？

2. **你做过“二次缓存系统”**

   * 除了浏览器缓存/CDN缓存，你还做了哪些层次？（如 cache-first 模式）
   * **追问**：二次缓存如何处理版本兼容？跨浏览器失效机制？
   * **追问**：缓存命中 vs 回源时，上层如何触发刷新策略？

3. **SSR 和 前端性能监控**

   * Q：SSR 的缓存策略是怎样做的？fragment cache 有哪些关键点？
   * **追问**：如何避免 hydration mismatch？预取策略是怎样设计的？
   * **追问**：如何监控首屏时间？PWA下 OfflineFallback 怎么设计？

4. **性能优化案例**

   * 谈一个具体案例，包括监控指标、定位过程与效果
   * 逐步追问 DOM Reflow、Memory Leak、Bundle Splitting、Tree-shaking 细节
   * **追问**：你用 Lighthouse/CWF 监控指标的自动检测方式是什么？

5. **HTTP/缓存/CORS/Security 深挖**

   * Q：谈谈 HTTP 缓存策略和协商缓存流程
   * **追问**：跨域下协商缓存是否生效？浏览器会怎样处理 opaque responses？
   * **追问**：你用 HTTP2 或 HTTP3 有什么实战经验？有什么问题？
   * Q：你提到做 CSP 和无感刷新 token，背后实现机制是什么？

---

## 🧩 第二部分：JavaScript & React 高级原理追问（20–25 分钟）

1. **JS Foundation**

   * Q：0.1+0.2 !== 0.3 的底层原因？如何彻底解决？
   * Q：Event Loop 机制详细如何？举例 micro/macro task 执行顺序。
   * **追问**：Node vs Browser Event Loop 区别在哪？

2. **模块与系统设计**

   * Q：你是如何实现模块联邦或沙箱加载的？
   * **追问**：代码分包后的命名空间如何隔离？热点更新方案怎么配合版本控制？

3. **React 深度探究**

   * Q：useState 如何在多个 Hooks 场景下保证状态一致？
   * **追问**：useReducer vs Redux 的选择理由？
   * Q：Fiber 调度如何解决长任务阻塞主线程，工作循环是如何分片？

4. **异步与并发模型**

   * Q：谈谈最近用过哪些 web worker、shared worker 或 node worker 的使用场景
   * **追问**：如何取消任务？如何 onmessage/onerror 做错误重试防御等？

---

## 💻 第三部分：5 道高难度代码题（共25–30 分钟、扩展阅读）

### 1. 实现 `Promise.mapLimit`

```js
/**
 * @param {Array} items
 * @param {number} limit
 * @param {function(item): Promise} fn
 * @return Promise< results[] >
 */
function mapLimit(items, limit, fn) {
  // 实现并发限制，出错应立即 reject，正确时按顺序返回结果
}
```

* **考察点**：异步并发控制、队列、Promise 手动执行顺序
* **拓展追问**：cancel 多余任务、task 超时机制、retry 机制

---

### 2. 手写深度挂载版本的 `throttle` + `debounce` 复合节流

```js
/**
 * createRateLimiter({wait, leading, trailing})
 * 返回节流防抖结合体，支持 cancel/flush 方法
 */
function createRateLimiter({wait, leading, trailing}) {
  // ...
}
```

* **考察点**：闭包、定时器、this 绑定、边界条件、多种参数组合处理
* **追问**：flush 如何立即触发尾部？如何在卸载阶段 cancel？

---

### 3. 实现一个 `LRUCache` 支持 maxSize 和 sizeEstimator

```js
class LRUCache {
  constructor(maxSize, sizeEstimator) { }
  get(key)
  set(key, value)
}
```

* **要求**：按字节估算值大小 `sizeEstimator(value)`, 总计超过 maxSize 时开始删除最近最少使用， O(1) 复杂度
* **拓展**：如何持久化到 IndexedDB？如何做多 tab 状态同步？

---

### 4. 实现 React hook `useEventCallback`（引用 stable callback）

```ts
function useEventCallback<T extends (...args: any[]) => any>(fn: T): T;
```

* **目的**：在事件中保持对最新 state/readRef，不影响依赖数组
* **追问**：和 useCallback 有什么区别？与 useRef 实现异步效用有什么联系？

---

### 5. 实现一个泛型版 `deepMerge<T>`，带类型支持

```ts
function deepMerge<T extends Record<string, any>>(a: T, b: Partial<T>): T
```

* **要求**：对嵌套 object 做深度 merge，不破坏原始结构，TS类型保持正确
* **追问**：如何处理数组、Map/Set？如何防止循环引用？

---

## ⏳ 结束及评估（5 分钟）

* 考察点覆盖面广：**异步并发、数据结构、TS 泛型、React Hooks、HTTP 缓存、性能、安全、系统设计**
* 追问节奏：每题后均紧接深入追问技术细节、优化方案、扩展场景
* 时间控制灵活，根据候选人回答情况做增删

---


