---
title: "LocalStorage实现带过期时间的缓存功能"
date: 2021-11-09T17:44:07+08:00
draft: true
---
## 前言

前端缓存一般有三种实现方式，`cookie`, `sessionStorage`, `localStorage`。

1. `cookie`是由服务器发送到浏览器并保存在本地的一小块数据，它会在浏览器下次向同一服务器再发起请求时被携带并发送到服务器上。它的存储空间较小，并且每次访问服务器都会携带着，会造成资源浪费，对性能有一定影响。
2. `sessionStorage`即为会话存储，页面关闭时存储的数据会被清除，此外需要注意的是，*在新标签或窗口打开一个页面时会复制顶级浏览会话的上下文作为新会话的上下文, 打开多个相同的URL的Tabs页面，会创建各自的sessionStorage*。
3. `localStorage` 与 `sessionStorage` 类似，区别在于页面关闭时数据不会清空，而有时我们需要对缓存设置过期时间，本文便是讲解如何实现带缓存的`localStorage`

## localStorage

支持以下方法：

1. `localStorage.setItem('myCat', 'Tom');`
2. `let cat = localStorage.getItem('myCat');`
3. `localStorage.removeItem('myCat');`
4. `localStorage.removeItem('myCat');`

## 实现

### 命名空间

正常情况下，存储数据到`localStorage`是直接存储的，但是数据较多的情况会显得较为混乱，这里可以使用设置一个命名空间的方式将需要缓存的数据放到一个key对应的value下面去。

```js
getStroage() {
    return JSON.parse(window.localStorage.getItem(namespace) || '{}')
}
```

取不到就返回空对象

### 设置缓存

```js
/**
     * 设置缓存
     * @param {*} key
     * @param {*} value
     * @param {*} expire 过期时间，单位为分钟
     */
  setItem(key, value, expire) {
    let storage = this.getStroage()
    storage[key] = {
      value: value,
      expire: expire == null ? null : Date.now() + expire * 1000
    }
    window.localStorage.setItem(namespace, JSON.stringify(storage))
  }
```

首先取命名空间对应的缓存，然后赋值新的键值对，值当中除了设置的value外，还赋值一个超时时间，取当前时间加上超时时间。

### 取值

```js
getItem(key) {
    const val = this.getStroage()[key]
    if (val == null) return null
    const { value, expire } = val
    if (expire == null || expire >= Date.now()) {
      return value
    }
    this.clearItem(key)
    return null
  }
```

首先取命名空间对应的缓存，然后判断超时时间，如果超时时间不存在或者大于当前时间说明还没有过期，返回对应的value， 否则返回null。

### 清除特定缓存

```js
clearItem(key) {
    let storage = this.getStroage()
    delete storage[key]
    window.localStorage.setItem(namespace, JSON.stringify(storage))
  }
```

仍然是先取命名空间对应的缓存，然后删除对应的值，再把剩余的数据缓存起来。

### 完成实现

```js
/**
 * storage封装
 * @author wangheng
 */
const namespace = `app`
class Storage {
  /**
     * 设置缓存
     * @param {*} key
     * @param {*} value
     * @param {*} expire 过期时间，单位为分钟
     */
  setItem(key, value, expire) {
    let storage = this.getStroage()
    storage[key] = {
      value: value,
      expire: expire == null ? null : Date.now() + expire * 1000
    }
    window.localStorage.setItem(namespace, JSON.stringify(storage))
  }
  getItem(key) {
    const val = this.getStroage()[key]
    if (val == null) return null
    const { value, expire } = val
    if (expire == null || expire >= Date.now()) {
      return value
    }
    this.clearItem(key)
    return null
  }
  getStroage() {
    return JSON.parse(window.localStorage.getItem(namespace) || '{}')
  }
  clearItem(key) {
    let storage = this.getStroage()
    delete storage[key]
    window.localStorage.setItem(namespace, JSON.stringify(storage))
  }
  clearAll() {
    window.localStorage.clear()
  }
}
export default new Storage()
```

## 总结

以上就实现了带缓存的localStorage,此外还实现了命名空间的封装，若项目有信息加密需求也可以实现加解密功能。
