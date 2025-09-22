# vueSeamlessScroll手动滚动



```js
npm i vue-seamless-scroll
```



```js
import vueSeamlessScroll from 'vue-seamless-scroll'
```



```	vue
<vueSeamlessScroll
  ref="scrollContainer"
  class="table_body"
  :class-option="classOption"
  :data="dataArr"
  @mousewheel.native="handleScroll"
>
  <div v-for="item in dataArr" :key="item.id" class="body_item">
    <div :title="item.name">{{ item.name || '--' }}</div>
  </div>
</vueSeamlessScroll>
```



```js
const classOption = ref({
  limitMoveNum: 4
})

const serverList = ref([..._arr])
const scrollContainer = ref(null)

const handleScroll = e => {
  if (dataArr.value?.length < classOption.value.limitMoveNum) return
  e.preventDefault()
  scrollContainer.value.yPos = scrollContainer.value.yPos - e.deltaY
  if (scrollContainer.value.yPos > 0) {
    scrollContainer.value.yPos = 0
    return
  }
  if (Math.abs(scrollContainer.value.yPos) > scrollContainer.value.realBoxHeight / 2) {
    scrollContainer.value.yPos = 0
  }
}
```



```css
.table_body {
  height: 145px;
  overflow: hidden;
}
```

