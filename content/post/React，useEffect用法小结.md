---
title: "React，useEffect用法小结"
date: 2022-05-25T10:32:30+08:00
categories:
- React
---

1. 每次 render 后执行：不提供第二个依赖项参数。比如：

```js
useEffect(() => {})
```

2. 仅第一次 render 后执行：提供一个空数组作为依赖项。比如

```js
useEffect(() => {}, [])
```

3. 第一次及依赖发生变化后执行，提供依赖数组

```js
useEffect(() => {}, [deps])
```

4. 组件unmount后执行，提供空数组依赖并返回一个回调函数

```js
useEffect(() => {
 return () => {
  // 组件unmount后执行
 }
}, [])
```
