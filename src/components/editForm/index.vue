<!--
 * @Author: NagatoYuki
 * @Date: 2019/5/10
-->
<template>
  <div class="edit-form">
    <el-form
      v-bind="formProps"
      auto-complete="on">

      <el-row>
        <el-col
          v-for="(item, index) in items"
          :key="item.props.prop || index"
          v-bind="colProps"
          class="edit-form-item">

          <!-- 如果传入的组件值是数组的话 -->
          <el-form-item
            v-bind="item.props"
            v-if="!item.isHidden && Array.isArray(item.child)">

            <component
              v-for="(childItem, childIndex) in isNotSelectItems(item.child)"
              :is="childItem.componentName"
              v-bind="childItem.props"
              v-model="formProps.model[childItem.model]"
              v-on="childItem.event"
              :key="childIndex">
              {{ childItem.componentName === 'el-button' ? childItem.text : null }}
            </component>

            <component
              v-for="(childItem, childIndex) in isSelectItems(item.child)"
              :is="childItem.componentName"
              v-bind="childItem.props"
              v-model="formProps.model[childItem.model]"
              v-on="childItem.event"
              :key="childIndex">
              <el-option
                v-for="selectItem in childItem.options.list"
                :key="childItem.options.value ? selectItem[childItem.options.value] : selectItem.value"
                :label="childItem.options.label ? selectItem[childItem.options.label] : selectItem.label"
                :value="childItem.options.value ? selectItem[childItem.options.value] : selectItem.value">
              </el-option>
            </component>

          </el-form-item>


          <!-- 如果传入的组件值非数组的话 -->
          <el-form-item
            v-bind="item.props"
            v-if="!item.isHidden && !Array.isArray(item.child)">
            <component
              :is="item.child.componentName"
              v-bind="item.child.props"
              v-model="formProps.model[item.child.model]"
              v-on="item.child.event"
              v-if="item.child.componentName === 'el-select'">
              <el-option
                v-for="selectItem in item.child.options.list"
                :key="item.child.options.value ? selectItem[item.child.options.value] : selectItem.value"
                :label="item.child.options.label ? selectItem[item.child.options.label] : selectItem.label"
                :value="item.child.options.value ? selectItem[item.child.options.value] : selectItem.value">
              </el-option>
            </component>

            <component
              v-else
              :is="item.child.componentName"
              v-bind="item.child.props"
              v-model="formProps.model[item.child.model]"
              v-on="item.child.event">
            </component>

          </el-form-item>
        </el-col>

      </el-row>

    </el-form>

  </div>
</template>

<script>
  export default {
    name: "index",
    props: {
      formProps: {
        type: Object,
        require: false,
        default: () => {
          return {}
        }
      },
      colProps: {
        type: Object,
        require: false,
        default: () => {
          return {}
        }
      },
      items: {  // 表单控件数组
        type: Array,
        require: true,
        default: () => {
          return []
        }
      }
    },
    mounted () {
    },
    methods: {
      handleEvent (event, fun, type) {  // 事件回调函数
        this.$emit(`handle${type}Event`, fun, event)
      },
      isSelectItems (arr) {
        return arr.filter(item => {
          return item.componentName === 'el-select'
        })
      },
      isNotSelectItems (arr) {
        return arr.filter(item => {
          return item.componentName !== 'el-select'
        })
      },
    }
  }
</script>

<style lang="scss">
  .edit-form {
    .edit-form-item{
      .el-form-item{
        width: 100%;
        display: flex;
        justify-content: space-between;
        label{
          width: 30%;
        }
        .el-form-item__content{
          width: 50%;
          .el-select{
            width: 100%;
          }
        }
      }
    }
  }
</style>
