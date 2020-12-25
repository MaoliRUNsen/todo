<template>
  <div class="content">
    <!-- 状态栏 -->
    <div v-if="list.length !== 0"
          class="todo-header">
      <!-- 状态栏的左侧  -->
      <div class="todo-header-left">
        <span class="active-text">{{text}}</span>
        <span>{{listData.length}}条</span>
      </div>
      <!-- 状态栏的右侧 -->
      <div class="todo-header-right">
        <div class="todo-header-right-item"
              :class="{'active-tab':activeIndex === 0}"
              @click="tab(0)">全部</div>
        <div class="todo-header-right-item"
              :class="{'active-tab':activeIndex === 1}"
              @click="tab(1)">待办</div>
        <div class="todo-header-right-item"
              :class="{'active-tab':activeIndex === 2}"
              @click="tab(2)">已完成</div>
      </div>
    </div>
    <!-- 没有数据的状态 -->
    <div v-if="list.length === 0"
          class="default">
      <div class="default-info">
        <div class="default-info-item">点击下方+号创建一个TodoList吧</div>
      </div>
    </div>
    <!-- 内容 -->
    <div v-else
          class="todo-content">
      <!-- todo-finish -->
      <div class="todo-list"
            :class="{'todo-finish':item.checked}"
            v-for="(item,index) in listData"
            :key="index"
            @click="finish(item.id)">
        <div class="todo-list-content">
          {{item.content}}
        </div>
      </div>
    </div>
    <!-- 创建按钮 -->
    <div class="create-todo"
          @click="create">
      <span class="iconfont icon-jia"
            :class="{'create-todo-active':textShow}">&#xe63e;</span>
    </div>
    <!-- 输入框 -->
    <div v-if="textShow"
          class="create-content"
          :class="{'create-show':textShow}">
      <div class="create-content-box">
        <!-- input 输入 -->
        <div class="create-input">
          <input type="text"
                 v-model="value"
                 placeholder="请输入您要创建的todo" />
        </div>
        <!-- 发布按钮 -->
        <button class="create-button"
              @click="add">
          创建
        </button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      value: '',
      list: [],
      activeIndex: 0,
      text: '全部',
      textShow: false
    }
  },
  computed: {
    listData () {
      let list = JSON.parse(JSON.stringify(this.list))
      let newList = []

      // 点击 全部
      if (this.activeIndex === 0) {
        // eslint-disable-next-line vue/no-side-effects-in-computed-properties
        this.text = '全部'
        return list
      }
      // 点击 待办事项
      if (this.activeIndex === 1) {
        // checked = false
        list.forEach((item) => {
          if (!item.checked) {
            newList.push(item)
          }
        })
        // eslint-disable-next-line vue/no-side-effects-in-computed-properties
        this.text = '待办'
        return newList
      }
      // 点击 已完成
      if (this.activeIndex === 2) {
        // checked = ture
        list.forEach((item) => {
          if (item.checked) {
            newList.push(item)
          }
        })
        // eslint-disable-next-line vue/no-side-effects-in-computed-properties
        this.text = '已完成'
        return newList
      }
      return []
    }
  },
  methods: {
    // 打开输入框
    create () {
      if (this.textShow) {
        this.close()
      } else {
        this.open()
      }
    },
    // 打开动画
    open () {
      this.textShow = true
    },
    // 关闭动画
    close () {
      this.textShow = false
    },
    // 发布
    add () {
      console.log(this.value)
      if (this.value === '') {
        alert('当前内容为空')
        return
      }
      this.list.unshift({
        // 对时间排序
        id: 'id' + new Date().getTime(),
        content: this.value,
        checked: false
      })
      this.value = ''
      this.close()
    },
    // 点击列表触发
    finish (id) {
      let index = this.list.findIndex((item) => item.id === id)
      console.log('我被点击了', this.list[index])
      this.list[index].checked = !this.list[index].checked
    },
    // 点击事件，当前状态进行同步
    tab (index) {
      this.activeIndex = index
    }
  }
}
</script>

<style>
/* 导入图标 */
@import '../../static/icon.css';

.todo-header {
  position: fixed;
  top: 0;
  left: 0;
  display: flex;
  /* flex布局 */
  align-items: center;
  font-size: 12px;
  width: 100%;
  height: 45px;
  /* 居中 */
  /* 设置边宽 */
  background: #ffffff;
}

.todo-header-left {
  width: 100%;
}

.active-text {
  font-size: 14px;
  color: #279abf;
  padding-right: 10px;
}

.todo-header-right {
  flex-shrink: 0;
  display: flex;
}

.todo-header-right-item {
  padding: 0 5px;
}

.active-tab {
  color: #279abf;
}

.todo-content {
  position: relative;
  padding-top: 50px;
  padding-bottom: 100px;
}

.todo-list {
  position: relative;
  display: flex;
  align-items: center;
  padding: 15px;
  margin: 15px;
  font-size: 14px;
  border-radius: 10px;
  background: #cfebfd;
  overflow: hidden;
}

.todo-list:after {
  position: absolute;
  /* 点击事件，需要对content设置为'' */
  content: '';
  top: 0;
  bottom: 0;
  left: 0;
  width: 5px;
  background: #91d1e8;
}

.checkbox {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  background: #ffffff;
}

.todo-finish .checkbox {
  position: relative;
  background: #eee;
  border-radius: 50%;
}

.todo-finish .todo-list-content {
  color: #999999;
  text-decoration: line-through;
}

.todo-finish.todo-list:after {
  background: #cccccc;
}

.create-todo {
  display: flex;
  justify-content: center;
  align-items: center;
  position: fixed;
  bottom: 20px;
  left: 0;
  right: 0;
  margin: 0 auto;
  width: 50px;
  height: 50px;
  border-radius: 50%;
  background-color: #deeff5;
}

.icon-jia {
  font-size: 50px;
  color: #add8e6;
}

.create-content {
  position: fixed;
  bottom: 95px;
  left: 20px;
  right: 20px;
  transition: all 0.3s;
  opacity: 0;
  transform: scale(0) translateY(200%);
}

.create-show {
  opacity: 1;
  transform: scale(1) translateY(0);
}

.create-content-box {
  display: flex;
  align-items: center;
  padding: 0 15px;
  padding-right: 0;
  border-radius: 50px;
  background: #deeff5;
}

.create-input {
  width: 100%;
  height: 100%;
  padding-right: 15px;
  color: #add8e6;
}

.create-button {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-shrink: 0;
  width: 80px;
  height: 50px;
  border-radius: 50px;
  font-size: 20px;
  background: white;
  color: black;

}

.default {
  padding-top: 100px;
}

.default-info {
  text-align: center;
  font-size: 14px;
  color: #cccccc;
}

.icon-jia {
  transition: transform 0.3s;
}

.create-todo-active {
  transform: rotate(135deg);
}
</style>
