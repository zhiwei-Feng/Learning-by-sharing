# MyISAM vs InnoDB

&#x20;主要在以下几个方面有区别

1. 是否支持外键
2. 是否支持事务, 重要区别
3. 是否是聚集索引的索引存储结构
4. 是否支持行级锁
5. 是否保存表的行数\是否支持全文索引
6. 使用场景上, MyISAM适合读(性能更好), InnoDB适合改

> InnoDB在做SELECT的时候，要维护的东西比MYISAM引擎多很多，影响查询速度有：
>
> &#x20;
>
> 1）数据块，InnoDB要缓存，MyISAM只缓存索引块，  这中间还有换进换出的减少；
>
> 2）InnoDB寻址要映射到块，再到行，MyISAM记录的直接是文件的OFFSET，定位比InnoDB要快
>
> 3）InnoDB还需要维护MVCC一致； 虽然你的场景没有，但他还是需要去维护



**reference**

1. ****[**https://segmentfault.com/a/1190000019263351**](https://segmentfault.com/a/1190000019263351)****
2. ****[**https://www.cnblogs.com/chingho/p/14798021.html**](https://www.cnblogs.com/chingho/p/14798021.html)****



