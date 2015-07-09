# 使用索引

当数据量增大时，生产系统上的Mongodb会变得越来越慢，最好的做法就是在项目早期就考虑通过index来提升查询效率。

## 为什么用索引

默认情况下，在数据库里面保存的数据是没有任何逻辑顺序的，常规而言一次查询需要对数据库所有的记录进行读取比对，这样是很低效的。

如果能够按照某个字段的顺序保存的数据，则可以查询得更快一些，但是这种方法无法适应对多个字段查询的场景，因为不可能按照每种排序方式保存不同的副本。

为了达到这样的目的，有一个更简单的方法，与保存多个副本不同，我们可以生成指向每个文档的指针，再把这些指针按照一定的规则保存在一个独立的文件中，这文件就叫做索引(index)。

举个栗子，实际文档如下保存

    {diskLocation: 1, username: 'John', country: 'UK', age: 24, gender: 'male'}
    {diskLocation: 2, username: 'Ash', country: 'India', age: 20, gender: 'male'}
    {diskLocation: 3, username: 'Jane', country: 'USA', age: 21, gender: 'female'}
    {diskLocation: 4, username: 'Carl', country: 'Japan', age: 30, gender: 'male'}
    {diskLocation: 5, username: 'Nadini', country: 'Sri Lanka', age: 20, gender: 'male'}

我们可以针对age和country建立不同的索引

年龄升序排列的索引

    {age: 20, diskLocation: 2}
    {age: 20, diskLocation: 5}
    {age: 21, diskLocation: 3}
    {age: 24, diskLocation: 1}
    {age: 30, diskLocation: 4}

国家字母排序的索引

    {country: 'UK', diskLocation: 1}
    {country: 'Japan', diskLocation: 4}
    {country: 'India', diskLocation: 2}
    {country: 'Sri Lanka', diskLocation: 5}
    {country: 'USA', diskLocation: 3}

通过上述数据库和索引，就很容易根据年龄和国家来进行检索。

## Mongodb和索引

Mongodb的索引策略比较类似上述内容，在MongoDB里，可以为单一的字段建立索引，也可以为多个字段建立一个联合索引(compound index)。更复杂地，可以为数组和内嵌文档建立索引。

下面是在mongo shell里面为posts这个库建立索引的方法

    db.posts.ensureIndex({category: 1});  //普通单个索引
    db.posts.ensureIndex({'owner.email': 1});  //内嵌索引，nested field index
    db.posts.ensureIndex({comments: 1});  // 数组索引，comments is an array

需要说明的是，ensureIndex语句可以执行多遍，他只是在没有这个索引的时候才会尝试去创建。




