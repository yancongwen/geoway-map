<template>
  <div class="tree">
    <div class="title">图层列表</div>
    <div class="project" v-for="(node,index) in nodeList" :key="index">
      <input type="checkbox" v-if="checkable" v-model=node.checked @change="changeParentNodeChecked">
      <label @click="checkSublayer(node)" :title="node.name" style="display: inline-block;height: 25px;line-height: 25px;">
        <img src="/static/images/folder.png" v-if="node.type==='folder'&&node.collapsed==true">
        <img src="/static/images/opened_folder.png" v-if="node.type==='folder'&&node.collapsed==false">
        <img src="/static/images/file.png" v-if="node.type==='file'">
        <span>{{node.name}}</span>
      </label>
      <div v-if="node.items!==undefined" class="subproject" v-show="node.collapsed==false">
        <div v-for="(item,index) in node.items" :key="index" :title="item.name" :name="item.name" :id="item.id" class="subproject-item hover" :class="item.id == activeId ? 'active':''">
          <input type="checkbox" v-if="checkable" v-model=item.checked @change="changeChildNodeChecked">
          <label :for="index" :title="item.name" style="display: inline-block;height: 25px;line-height: 25px;">
            <img src="/static/images/file.png">
            <span :name="item.name">{{item.name}}</span>
          </label>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: ['nodeList', 'activeId', 'checkable'],
  methods: {
    /* 目录的展开与折叠 */
    checkSublayer: function (node) {
      node.collapsed = !node.collapsed
    },
    // 父节点选中改选事件
    changeParentNodeChecked: function (e) {
      // 若父节点选中，则子节点全部选中
      this.nodeList.forEach(node => {
        if (node.items !== undefined) {
          node.items.forEach(item => {
            item.checked = node.checked
          })
        }
      })
      this.$emit('on-select-change', this.nodeList)
    },
    // 子节点选中改变事件
    changeChildNodeChecked: function (e) {
      // 若子节点全部选中，则选中父节点
      this.nodeList.forEach(node => {
        if (node.items !== undefined) {
          node.checked = node.items.every(item => {
            return item.checked
          })
        }
      })
      this.$emit('on-select-change', this.nodeList)
    }
  }
}
</script>

<style scoped>
.tree {
  width: 100%;
}
.title {
  height: 30px;
  line-height: 30px;
  padding: 0 10px;
  font-weight: 500;
  color: #ffffff;
  background-color: rgb(40,92,149);
}
.project {
  margin: 5px 25px 5px 10px;
  overflow: hidden;
}
.project .subproject-item.active {
  background-color: #bedbfd;
  height: 30px;
  line-height: 30px;
  font-size: 12px;
}
.project a {
  color: #6b6b6b;
}
.project label {
  white-space: nowrap;
  cursor: pointer;
}
.project img {
  position: relative;
  top: 3px;
  width: 16px;
}
.subproject-item {
  padding-left: 20px;
}
.hover {
  margin: 0;
  font-size: 12px;
}
.hover.sub3 {
  padding-left: 30px;
}
.hover:hover {
  background-color: #bedbfd;
}
</style>
