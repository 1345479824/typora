<https://blog.51cto.com/itzhoujun/2365607> 原博客地址

事务ID在begin的时候是不会生成的。在第一次执行增删改查语句的时候才会生成。或者开启事务时，写成“start transaction with consistent”也会生成事务ID。