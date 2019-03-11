> **数组更新的检测**

**变异方法**：会改变被这些方法调用的原始数组，Vue重写了数组方法push()、pop()、shift()、unshift()、splice()、sort()、
reverse()，这些方法会触发视图更新。
**非变异方法**：这些不会改变原始数组，总会返回一个新数组，例如filter()、concat()和slice()。当使用这些方法的时候，可以用新数组代替旧数组：
```
example1.items = example1.items.filter(function (item) {
  return item.message.match(/Foo/)
})
```
**不能检测的变动**：
当你利用索引直接设置一个项时，例如：`vm.items[indexOfItem] = newValue`
当你修改数组的长度时，例如：`vm.items.length = newLength`
例如：
```
var vm = new Vue({
  data: {
    items: ['a', 'b', 'c']
  }
})
vm.items[1] = 'x' // 不是响应性的
vm.items.length = 2 // 不是响应性的
```
下面两种方式可以实现相同的效果，同时将触发状态更新：
```
// Vue.set
Vue.set(vm.items, indexOfItem, newValue)
```
```
// Array.prototype.splice
vm.items.splice(indexOfItem, 1, newValue)
```
也可以使用 `vm.$set` 实例方法，该方法是全局方法 `Vue.set` 的一个别名：
```
vm.$set(vm.items, indexOfItem, newValue)
```

为了解决修改数组长度的问题，你可以使用 `splice`：
```
vm.items.splice(newLength)
```

> **对象的更改**

对于已经创建的实例，Vue 不能动态添加根级别的响应式属性。但是，可以使用 `Vue.set(object, key, value)` 方法向嵌套对象添加响应式属性。例如，对于：

```
var vm = new Vue({
  data: {
    userProfile: {
      name: 'Anika'
    }
  }
})
```

你可以添加一个新的 age 属性到嵌套的 userProfile 对象：
```
Vue.set(vm.userProfile, 'age', 27)
```
你还可以使用 `vm.$set` 实例方法，它只是全局 `Vue.set` 的别名：
```
vm.$set(vm.userProfile, 'age', 27)
```

有时你可能需要为已有对象赋予多个新属性，比如使用 `Object.assign()` 或 `_.extend()`。在这种情况下，你应该用两个对象的属性创建一个新的对象。所以，如果你想添加新的响应式属性，不要像这样：
```
Object.assign(vm.userProfile, {
  age: 27,
  favoriteColor: 'Vue Green'
})
```
你应该这样做：

```
vm.userProfile = Object.assign({}, vm.userProfile, {
  age: 27,
  favoriteColor: 'Vue Green'
})
```
