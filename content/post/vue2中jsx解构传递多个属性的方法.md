---
title: "Vue2中jsx解构传递多个属性的方法"
date: 2022-05-25T10:38:34+08:00
categories:
  - Vue
---

```js
render() {
  const inputAttrs = {
    type: 'email',
    placeholder: 'Enter your email'
  }

  return <input {...{ attrs: inputAttrs }} />
}
````
**注意：** 必须使用 ·`attrs`作为前缀，否则属性传递失败