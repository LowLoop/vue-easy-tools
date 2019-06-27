<!--
 * @Author: NagatoYuki
 * @Date: 2019/4/9
-->
<template>
  <div class="search-form">
    <el-row :gutter="gutter">
      <el-col
        v-for="(item, index) in items"
        :key="index"
        :span="item.span ? item.span : globalSpan">
        <div class="form-item">
          <div
            class="label"
            :style="item.style">
            <span>{{ item.label }}</span>
          </div>

          <component
            :is="item.componentName"
            v-bind="item.props"
            v-on="item.event"
            v-model="form[item.column]">

            <!-- 为了兼容之前的版本，所以这下面做这些复杂的判断处理 -->
            <!-- 为了兼容在此之前已使用未改动之前的本组件的其它页面 -->
            <!-- 比较简化高效的代码是不需要这么多嵌套div判断的 -->

            <div v-if="(item.child && item.child.componentName.indexOf('radio') >= 0) || item.componentName === 'el-radio-group'">
              <component
                :is="item.child ? item.child.componentName : 'el-radio'"
                v-for="childItem in getCurrentOptions(item)"
                v-bind="item.child ? item.child.prop : {}"
                :key="(item.child && item.child.options.value) ?
                      childItem[item.child.options.value] :
                      (item.options && item.options.value) ? childItem[item.options.value] : childItem['value']"
                :label="(item.child && item.child.options.value) ?
                        childItem[item.child.options.value] :
                        (item.options && item.options.value) ? childItem[item.options.value] : childItem['value']">
                {{ (item.child && item.child.options.label) ?
                childItem[item.child.options.label] :
                (item.options && item.options.label) ? childItem[item.options.label] : childItem['label'] }}
              </component>
              <!--<el-radio
                v-for="childItem in getCurrentOptions(item)"
                :key="(item.child && item.child.options.value) ?
                      childItem[item.child.options.value] :
                      (item.options && item.options.value) ? childItem[item.options.value] : childItem['value']"
                :label="(item.child && item.child.options.value) ?
                        childItem[item.child.options.value] :
                        (item.options && item.options.value) ? childItem[item.options.value] : childItem['value']">
                {{ (item.child && item.child.options.label) ?
                    childItem[item.child.options.label] :
                    (item.options && item.options.label) ? childItem[item.options.label] : childItem['label'] }}
              </el-radio>-->
            </div>

            <div v-if="(item.child && item.child.componentName.indexOf('select') >= 0) || item.componentName === 'el-select'">
              <component
                :is="item.child ? item.child.componentName : 'el-option'"
                v-for="selectItem in getCurrentOptions(item)"
                v-bind="item.child ? item.child.prop : {}"
                :style="item.elOptionStyle"
                :key="(item.child && item.child.options.value) ?
                      selectItem[item.child.options.value] :
                      (item.options && item.options.value) ? selectItem[item.options.value] : selectItem['value']"
                :label="(item.child && item.child.options.label) ?
                        selectItem[item.child.options.label] :
                        (item.options && item.options.label) ? selectItem[item.options.label] : selectItem['label']"
                :value="(item.child && item.child.options.value) ?
                        selectItem[item.child.options.value] :
                        (item.options && item.options.value) ? selectItem[item.options.value] : selectItem['value']">
              </component>
              <!--<el-option
                v-for="selectItem in getCurrentOptions(item)"
                :style="item.elOptionStyle"
                :key="(item.child && item.child.options.value) ?
                      selectItem[item.child.options.value] :
                      (item.options && item.options.value) ? selectItem[item.options.value] : selectItem['value']"
                :label="(item.child && item.child.options.label) ?
                        selectItem[item.child.options.label] :
                        (item.options && item.options.label) ? selectItem[item.options.label] : selectItem['label']"
                :value="(item.child && item.child.options.value) ?
                        selectItem[item.child.options.value] :
                        (item.options && item.options.value) ? selectItem[item.options.value] : selectItem['value']">
              </el-option>-->
            </div>

            <div v-if="(item.child && item.child.componentName.indexOf('checkbox') >= 0) || item.componentName === 'el-checkbox-group'">
              <component
                :is="item.child ? item.child.componentName : 'el-checkbox'"
                v-for="childItem in getCurrentOptions(item)"
                v-bind="item.child ? item.child.prop : {}"
                :key="(item.child && item.child.options.value) ?
                      childItem[item.child.options.value] :
                      (item.options && item.options.value) ? childItem[item.options.value] : childItem['value']"
                :label="(item.child && item.child.options.value) ?
                        childItem[item.child.options.value] :
                        (item.options && item.options.value) ? childItem[item.options.value] : childItem['value']">
                {{ (item.child && item.child.options.label) ?
                childItem[item.child.options.label] :
                (item.options && item.options.label) ? childItem[item.options.label] : childItem['label'] }}

              </component>
              <!--<el-checkbox
                v-for="childItem in getCurrentOptions(item)"
                :key="(item.child && item.child.options.value) ?
                      childItem[item.child.options.value] :
                      (item.options && item.options.value) ? childItem[item.options.value] : childItem['value']"
                :label="(item.child && item.child.options.value) ?
                        childItem[item.child.options.value] :
                        (item.options && item.options.value) ? childItem[item.options.value] : childItem['value']">
                {{ (item.child && item.child.options.label) ?
                childItem[item.child.options.label] :
                (item.options && item.options.label) ? childItem[item.options.label] : childItem['label'] }}
              </el-checkbox>-->
            </div>

          </component>

          <!--<component
            v-else
            :is="item.componentName"
            v-bind="item.props"
            v-on="item.event"
            v-model="form[item.column]">
          </component>-->

        </div>
      </el-col>
      <el-col
        v-if="layoutType === 'horizontal'"
        :span="btnSpan"
        :push="btnPush">
        <el-button
          v-for="(button, index) in btns"
          :key="index"
          :icon="button.icon"
          @click="btnClick($event, button.clickFun)"
          :type="button.type"
          :size="button.size">
          {{ button.text }}
        </el-button>

        <button-center
          v-if="permissionButton && permissionButton.havePermissionButton"
          :customData="permissionButton.customData"
          :buttonConfig="permissionButton.buttonConfig"
          :filterButtons="permissionButton.filterButtons"></button-center>
      </el-col>
    </el-row>
    <el-row v-if="layoutType === 'vertical'" type="flex" justify="center" align="center">
      <el-col
        v-for="(button, index) in btns"
        :span="button.span"
        :key="index">
        <el-button
          :icon="button.icon"
          @click="btnClick($event, button.clickFun)"
          :type="button.type"
          :size="button.size">
          {{ button.text }}
        </el-button>
      </el-col>
    </el-row>
  </div>
</template>

<script>
  import lazyTreeSelect from '@/components/lazyTreeSelect'
  import buttonCenter from '@/components/buttonCenter'
  export default {
    name: "index",
    components: {
      lazyTreeSelect,
      buttonCenter
    },
    props: {
      permissionButton: {
        type: Object,
        require: false,
        default: () => {
          return {}
        }
      },
      layoutType: {   // horizontal or vertical  这个布局属性是指表单item和按钮的相对位置
        type: String,
        require: false,
        default: 'vertical'
      },
      btns: { // 按钮组
        type: Array,
        require: false,
        default: () => {
          return [
            {
              type: '',
              size: 'mini',
              text: '搜索',
              icon: 'el-icon-search',
              span: 4
            }
          ]
        }
      },
      btnSpan: {  // 当表单布局为横向-horizontal 时，按钮区域所占宽度比例
        type: Number,
        require: false,
        default: 2
      },
      btnPush: {  // 按钮块儿 向右横移格数
        type: Number,
        require: false,
        default: 0
      },
      gutter: { // 表单布局中每个item的间隔
        type: Number,
        require: false,
        default: 20
      },
      globalSpan: { // 在没有配置每个表单item 宽度的情况下，将会采用这个配置
        type: Number,
        require: false,
        default: 6
      },
      items: {  // 表单控件数组
        type: Array,
        require: true,
        default: () => {
          return []
        }
      },
      form: { // 表单绑定的数据对象
        type: Object,
        require: true,
        default: () => {
          return {}
        }
      }
    },
    mounted () {
    },
    methods: {
      btnClick (event, fun) { // 按钮的回调函数
        this.$emit('handleBtnClick', fun)
      },
      funFactory (vm, realFun, customData) {  // 表单中各个组件触发事件的代理方法
        return ($event) => {
          if (customData) {
            return vm[realFun]($event, ...customData)
          } else {
            return vm[realFun]($event)
          }
        }
      },
      getCurrentOptions (item) {
        if (item.child && item.child.options) {
          return item.child.options.list
        } else {
          return item.options.list
        }
      }
    }
  }
</script>

<style lang="scss" scoped>
  .search-form{
    .el-row {
      margin-bottom: 20px;
      &:last-child {
        margin-bottom: 0;
      }
    }
    .el-col {
      border-radius: 4px;
    }
    .bg-purple-dark {
      background: #99a9bf;
    }
    .bg-purple {
      background: #d3dce6;
    }
    .bg-purple-light {
      background: #e5e9f2;
    }
    .grid-content {
      border-radius: 4px;
      min-height: 36px;
    }
    .row-bg {
      padding: 10px 0;
      background-color: #f9fafc;
    }
    .form-item{
      display: flex;
      margin: 7px 0;

      .label{
        display: flex;
        font-size: 14px;
        text-align: center;
        align-items: center;
        padding-right: 5px;
      }
    }
  }
</style>
