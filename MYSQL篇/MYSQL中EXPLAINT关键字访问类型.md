```
性能从高到底以此为：system，const，eq_ref，ref，fulltext，ref_or_null，index_merge，unique_subquery，index_subquery，range，index，ALL
```

除了all之外，其他的type都可以使用到索引，除了index_merge之外，其他的type只可以用到一个索引。

- 1、system

	一般查询系统表用到的访问类型，开发中不常见。

- 2、const

	一般指通过主键返回。

- 3、eq_ref

	主键或唯一索引。

- 4、ref
    通过索引能返回多条结果。

- range

	索引范围查询，常见于使用 =, <>, >, >=, <, <=, IS NULL, <=>, BETWEEN, IN()或者like等运算符的查询中。

- 7、index

	索引全表扫描，把索引从头到尾扫一遍。这里包含两种情况：  
一种是查询使用了覆盖索引，那么它只需要扫描索引就可以获得数据，这个效率要比全表扫描要快，因为索引通常比数据表小，而且还能避免二次查询。在extra中显示Using index，反之，如果在索引上进行全表扫描，没有Using index的提示。

