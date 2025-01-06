# TERRIBLE-DEBUG-LOG

日常踩坑记录！！！



## mysql

-   mysql中`order by` ` limit` 分页数据重复或丢失问题说明

​		原因在于mysql优化器在遇到order by limit语句时，做了个查询优化。如果多条记录的 ORDER BY 列中有相同的值，服务器可以自由地按任何顺序返回这些记录，并可能根据整体执行计划的不同而采取不同的方式。换句话说，相对于未排序列，这些记录的排序顺序是不确定的，这就会导致两次查询后limit的值是不确定的，这就会出现类似第一页和第二页数据出现重复数据问题（有重复就会有遗漏）

解决方案：ORDER BY一个不重复的字段，例如ORDER BY id，确保在出现重复的列数据时能够有确定的排序。



-   带 LIMIT 和不带 LIMIT 的 ORDER BY 查询可能以不同的顺序返回记录


## dubbo
- Dubbo-Fail to decode request due to:......
  Service not found:......

  排查原因：
    1、检查参数是否实现了java.io.Serializable，
    2、调用的服务提供者依赖包里可能没有对应的类，
    3、检查两边接口包的版本是否一致，
    4、检查两边的dubbo所引用的jar是否一致

## VUE2
- vue的`<keep-alive>`内置组件不起作用，导致tab切换时页面的生命周期完成执行，页面数据刷新

  排查原因：
  由于目前 keep-alive 和 router-view 是强耦合的，而且查看文档和源码不难发现 keep-alive 的 include (opens new window)默认是优先匹配组件的 name ，所以在编写路由 router 和路由对应的 view component 的时候一定要确保 两者的 name 是完全一致的。(切记 name 命名时候尽量保证唯一性 切记不要和某些组件的命名重复了，不然会递归引用最后内存溢出等问题)，页面（组件）的name和路由的name名字要一样

## Element-UI
- el-table页面在切换到其他页面再切回来时，会自动将滚动条置顶

  原因：会触发activated，导致表格重新布局，将滚动条置顶
  解决方案：
  ```js
  data(){
    return {
      tableScrollTop:0,
    }
  },
  updated() {
    const {handleTableScroll} = this;
    this.$nextTick(() => {
      // 添加事件前需要先移除之前添加的的事件 防止多次触发事件
      // TODO 可尝试再mounted中绑定事件  但似乎触发了dom更新后会导致事件失效（可尝试使用nextTick和setTimeout结合的方式看是否能解决此问题）
      this.$refs.singleTable.$el.querySelector('div.el-table__body-wrapper')?.removeEventListener("scroll", handleTableScroll);
      this.$refs.singleTable.$el.querySelector('div.el-table__body-wrapper')?.addEventListener("scroll", handleTableScroll);
    })
  },
  activated() {
    const {tableScrollTop} = this
    this.$nextTick(() => {
      setTimeout(() => {
        this.$refs.singleTable.$el.querySelector('div.el-table__body-wrapper').scrollTop = tableScrollTop;
      }, 60)
    });
  },
  ```
