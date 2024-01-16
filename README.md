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
