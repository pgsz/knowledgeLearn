# deep 样式穿透



## scoped 属性

- Vue 会为组件 template 中定义的 HTML 元素中添加一个 data-v-{hash值} 的属性选择器
- 在 style 标签中写的 CSS 样式， Vue 都会在样式选择器的最后段添加 data-v-{hash值} 的属性标签
- 在使用第三方 UI 库时，只会为根元素添加 data-v-{hash值} 属性，而子元素不会添加

HTML 标签添加一个属性选择器，可以根据这个属性选择器来保证各个组件的样式不被相互污染，从而实现样式隔离模块化效果



## deep

编译阶段：当 Vue 编译到带有 ::v-deep 、/deep/ 或 >>> 的 CSS 时，会将这些特殊选择器转换为普通的 CSS 选择器；意味这些选择器不再受 scoped 属性的限制。