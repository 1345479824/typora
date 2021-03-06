## 锁

- 死锁和案例
- 全局锁、表锁、行锁、乐观锁、悲观锁、排它锁
- 锁优化

## 事务

- 事务特性
- 脏读、幻读、不可重复读
- 事务隔离
- 并发事务
- 事务实现原理

## 日志

- 错误日志
- 查询日志
- 慢日志
- redo log(重做日志)
- binlog(归档日志)
- undo log(回滚日志)

## 命令和内置函数

- 服务器操作
- 服务器运行状态
- 执行统计
- 用户授权
- 库表设置
- 删除的N种方式

## 通用模块

- 查询内部执行过程
- 查询缓存
- 表空间
- 表收缩
- 前置索引
- 回表查询
- 内存表VS临时表
- 数据类型间的区别
- 删除表的几种方式和性能对比
- 枚举
- count(1)/count(*)/count(字段)性能对比
- 视图
- 删除数据恢复和原理分析
- 数据库引擎：Innodb和myisam
- 数据结构

## 索引

- 模糊查询与索引
- 唯一索引和普通索引性能对比
- 全局索引
- 聚集索引VS非聚集索引
- 最左匹配原则
- 前缀索引
- 存储算法
- 优化器如何选择索引
- 不使用索引原因列举

## 高性能

- 慢查询排查
- 读写分离
- 主备无延迟
- 多实例配置
- 水平分区
- 性能分析

## 开放性问题

- 超大表优化
- 线上操作隐患
- 故障排查
- 主从延迟分析
- 误删防范
- 高性能服务器设计

## 索引优化口诀

全值匹配我最爱，最左前缀要遵守；
带头大哥不能死，中间兄弟不能断；
索引列上少计算，范围之后全失效；
Like百分写最右，覆盖索引不写星；
不等空值还有or，索引失效要少用；
哇擦引号不可丢，SQL高级也不难！