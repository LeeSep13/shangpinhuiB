<!--
 * @Author: Lee && lsh133417@163.com
 * @Date: 2022-08-11 16:21:47
 * @LastEditors: Lee && lsh133417@163.com
 * @LastEditTime: 2022-08-13 09:58:16
 * @FilePath: \shangpinghui-bs\src\views\product\attr\index.vue
 * @Description:
 * Copyright (c) 2022 by Lee email: lsh133417@163.com, All Rights Reserved.
-->
<template>
  <div>
    <el-card style="margin:20px 0px">
      <CategorySelect @getCategoryId="getCategoryId" />
    </el-card>
    <el-card>
      <div v-show="isShowTable">
        <el-button type="primary" icon="el-icon-plus" :disabled="!category3Id" @click="isShowTable = false">添加属性
        </el-button>
        <!-- 表格：展示平台属性 -->
        <el-table style="width: 100%;" border :data="attrList">
          <el-table-column type="index" label="序号" width="80" align="center"></el-table-column>
          <el-table-column prop="attrName" label="属性名称" width="150"></el-table-column>
          <el-table-column prop="prop" label="属性值列表">
            <template slot-scope="{row,$index}">
              <el-tag type="success" v-for="(attrValue, index) in row.attrValueList" :key="attrValue.id"
                style="margin:0px 20px">
                {{ attrValue.valueName }}</el-tag>
            </template>
          </el-table-column>
          <el-table-column prop="prop" label="操作" width="150">
            <template slot-scope="{row,$index}">
              <el-button type="warning" icon="el-icon-edit" size="mini" @click="isShowTable = false"></el-button>
              <el-button type="danger" icon="el-icon-delete" size="mini"></el-button>
            </template>
          </el-table-column>
        </el-table>
      </div>
      <!-- 添加属性|修改属性的结构 -->
      <div v-show="!isShowTable">
        <el-form :inline="true" ref="form" label-width="80px" :model="attrInfo">
          <el-form-item label="属性名">
            <el-input placeholder="请输入属性名" v-model="attrInfo.attrName"></el-input>
          </el-form-item>
        </el-form>
        <el-button type="primary" icon="el-icon-plus">添加属性值</el-button>
        <el-button @click="isShowTable = true">取消</el-button>
        <el-table style="width:100%;margin: 20px 0px" border :data="attrInfo.attrValueList">
          <el-table-column align="center" type="index" label="序号" width="80"></el-table-column>
          <el-table-column prop="prop" label="属性值名称">
            <template slot-scope="{row,$index}">
              <el-input v-model="row.valueName" placeholder="请输入属性值名称" size="mini"></el-input>
            </template>
          </el-table-column>
          <el-table-column prop="prop" label="操作">
            <template slot-scope="{row,$index}">
              <el-button type="danger" icon="el-icon-delete"></el-button>
            </template>
          </el-table-column>
        </el-table>
        <el-button type="primary">保存</el-button>
        <el-button @click="isShowTable = true">取消</el-button>
      </div>
    </el-card>
  </div>
</template>

<script>
export default {
  name: "attr",
  components: {},
  data() {
    return {
      category1Id: '',
      category2Id: '',
      category3Id: '',
      // 接受平台属性的字段
      attrList: [],
      // 这个属性控制table表格显示与隐藏
      isShowTable: false,
      // 收集新增属性|修改属性使用的
      attrInfo: {
        "attrName": "", // 属性名
        "attrValueList": [  // 属性值，因为属性值可以有多个，所以用数组，每个属性值都是一个对象需要attrId，valueName
          {
            "attrId": 0, // 相应的属性名的id
            "valueName": ""
          }
        ],
        "categoryId": 0, // 三级分类的id
        "categoryLevel": 0, // 因为服务器也需要区分几级分类id
      }
    };
  },
  methods: {
    // 自定义事件的回调
    getCategoryId({ categoryId, level }) {
      // 区分三级分类相应的id，以及父组件进行存储
      if (level == 1) {
        this.category1Id = categoryId;
        this.category2Id = '';
        this.category3Id = '';
      } else if (level == 2) {
        this.category2Id = categoryId;
        this.category3Id = '';
      } else {
        // 代表三级分类的id有了
        this.category3Id = categoryId;
        // 发请求获取平台属性的数据
        this.getAttrList();
      }
    },
    // 获取平台属性的数据
    // 当用户确定三级分类数据的时候，可以向服务器发请求获取平台属性进行展示
    async getAttrList() {
      // 获取分类的ID
      const { category1Id, category2Id, category3Id } = this;
      let result = await this.$API.attr.reqAttrList(category1Id, category2Id, category3Id)
      if (result.code == 200) {
        this.attrList = result.data;
      }
    }
  }
};
</script>
<style scoped>
</style>
