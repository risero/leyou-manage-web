<template>
  <div>
    <!-- pa: 边距，px：横边距、py：纵边距、pl：左边距、pr：右边距... -->
    <v-layout class="px-4 pb-2">
      <v-flex xs2>
          <v-btn color="info">新增品牌</v-btn>
      </v-flex>
      <v-spacer/><!-- 自动填补间隔 -->
      <v-flex xs4>
          <!-- append-icon: 追加搜索图标 -->
          <v-text-field hide-details append-icon="search" v-model="key"
            label="搜索">
          </v-text-field>
      </v-flex>
    </v-layout>

    <v-data-table
      :headers="headers"
      :items="brands"
      :pagination.sync="pagination"
      :total-items="totalBrands"
      :loading="loading"
      class="elevation-1"
    >
      <template slot="items" slot-scope="props">
        <td class="text-xs-center">{{ props.item.id }}</td> <!-- 遍历的属性 -->
        <td class="text-xs-center">{{ props.item.name }}</td>
        <td class="text-xs-center"><img :src="props.item.image" alt=""/></td>
        <td class="text-xs-center">{{ props.item.letter }}</td>
        <td class="text-xs-center">
          <v-btn flat icon color="info">
            <v-icon>edit</v-icon>
          </v-btn>
          <v-btn flat icon color="error">
            <v-icon>delete</v-icon>
          </v-btn>
        </td>
      </template>
    </v-data-table>
  </div>
</template>

<script>
  export default {
    name: "MyBrand",
    data() {
      return {
        headers: [
          {
            text: '品牌编号',
            value: 'id', //value是关联的属性/字段
            align: 'center',
            sortable: 'true', //是否使用排序
          },
          {text: '品牌名称', value: 'name', align: 'center', sortable: 'false'},
          {text: '品牌Logo', value: 'image', align: 'center', sortable: 'false'},
          {text: '品牌首字母', value: 'letter', align: 'center', sortable: 'true',},
          {text: '编辑', align: 'center', sortable: "false"}
        ],
        brands: [],
        pagination: {},
        totalBrands: 0,
        loading: false,
        key: "" // 搜索条件
      }
    },
    created() { // v使用created方法，vue实例一创建就执行
      this.brands = [
        {id: 1, name: 'OPPO', image: '1.jpg', letter: 'O'},
        {id: 2, name: '华为', image: '1.jpg', letter: 'H'},
        {id: 3, name: '魅族', image: '1.jpg', letter: 'M'}
      ];
      this.totalBrands = 15;

      // 去后台查询
      this.loadBrand();
    },
    watch:{ // 监控
      key(){ // 对搜索文本进行监控
        this.pagination.page = 1;
        this.loadBrand() // 搜索的文本改变就发送请求去查询
      },
      pagination:{ // 对分页进行监控
        deep: true, //开启深度监控
        handler(){
          this.loadBrand() // 分页以改变就发起请求去查询
        }
      }
    },
    methods: {
      loadBrand(){
        this.loading = true // 加载进度条
        this.$http.get('/item/brand/page', {
          params: {
            // 获取发起请求的数据
            key: this.key, // 搜索条件
            page: this.pagination.page, // 当前页
            rows: this.pagination.rowsPerPage, // 每页数据
            sortBy: this.pagination.sortBy, // 排序字段
            desc: this.pagination.descending, // 是否排序
            //totalItems: this.pagination.totalItems // 总数
          }
        }).then(resp => {
          this.brands = resp.data.items;
          this.totalBrands = resp.data.total;
          this.loading = false // 数据加载完成后，取消进度条
        })
      },
    }
  }
</script>

<style scoped>

</style>
