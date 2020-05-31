# 对于大流量的网站，采用什么样的方法来解决访问量问题
* 前端优化（CND加速、建立独立图片服务器），采用http2.0多路复用
* 服务端优化（页面静态化、并发处理[异步|多线程]、队列处理）
* 数据库优化（数据库缓存[Memcachaed|Redis]、读写分离、分库分表、分区）
* Web服务器优化（负载均衡、反向代理）

# MySQL数据库作发布系统的存储，一天五万条以上的增量，预计运维三年,怎么优化？
* 设计良好的数据库结构，允许部分数据冗余，尽量避免join查询，提高效率；
* 选择合适的表字段数据类型和存储引擎，适当的添加索引；
* mysql库主从读写分离；
* 找规律分表，减少单表中的数据量提高查询速度；
* 添加缓存机制，比如memcached，redis等；
* 不经常改动的页面，生成静态页面；
* 书写高效率的SQL。比如 SELECT * FROM TABEL 改为 SELECT field_1, field_2, field_3 FROM TABLE。

# 编译性语言、常驻进程程序与传统PHP差别
编译性语言、常驻进程程序与传统PHP差别
* 受fastcgi程序限制
* 解释性语言每次执行都需要重新编译（可通过opcache优化）
* 常驻进程编程时应考虑连接池管理，变量生命周期，线程资源竞争加锁，资源回收等

# PHP大文件处理
* 针对行尾数据，php_exec执行shell命令，采用tail获取文件指定数据
* fseek，通过文件指针获取指定位置数据
* yield，通过迭代器获取文件数据

## shell

```
function getLastLines($file,$line=1){
	$file = escapeshellarg($file); // 对命令行参数进行安全转义
	$line = `tail -n $line $file`;
	return $line;
}
```

## fseek

  ```
<?php
$fp = fopen( './test1.log', 'r' );
$line = 5;
$pos = -2;
$ch = '';
$content = '';
while( $line > 0 ){
  while( $ch != "\n" ){
    fseek( $fp, $pos, SEEK_END );
    $ch = fgetc( $fp );
    $pos--;
  }
  $ch = '';
  $content .= fgets( $fp );
  $line--;
}
echo $content;
exit;
  ```

## yield

```
<?php
function read_file($path) {
  if ($handle = fopen($path, 'r')) {
    while (! feof($handle)) {
      yield trim(fgets($handle));
    }
    fclose($handle);
  }
}
// 使用
$glob = read_file('/var/www/hello.txt');
while ($glob->valid()) {
   
  // 当前行文本
  $line = $glob->current();
   
  // 逐行处理数据
  // $line
 
  // 指向下一个，不能少
  $glob->next();
}
```

# 抢购场景

## HTML静态页面 通过CDN解决
## 后台接口：建议异步
### 高并发，服务器压力极大，不建议MySql
MySQL数据库自带的锁机制很好的解决问题，但是，在大规模并发的场景中，不推荐使用MySQL

### 秒杀抢购中的存在的作弊及对应的解决方案
1. 同一账号一次发送多个请求（针对账号做一个拦截）
2. 多个账号一次发送多个请求（针对ip进行拦截）
3. 多个账号通过不同ip发请求（采用数据挖掘）
4. 12306写一个中转软件，通过人来识别验证码（数据挖掘，分析）

### 高并发的数据安全
1. 超发原因：
  100个商品只剩一个，结果进来100个请求全部读到1，悲剧了

2. 悲观锁思路：
  悲观锁，也就是在修改数据的时候，采用锁定状态，排斥外部请求的修改。遇到加锁的状态，就必须等待。
    但是高并发场景可能导致有些线程永远也没有办法得到锁，会死在那里

3. FIFO思路
  高并发的场景下，因为请求很多，很可能一瞬间将队列内存“撑爆”，然后系统又陷入到了异常状态。或者设计一个极大的内存队列，也是一种方案，但是，系统处理完一个队列内请求的速度根本无法和疯狂涌入队列中的数目相比。

4. 乐观锁
  乐观锁，是相对于“悲观锁”采用更为宽松的加锁机制，大都是采用带版本号（Version）更新。实现就是，这个数据所有请求都有资格去修改，但会获得一个该数据的版本号，只有版本号符合的才能更新成功，其他的返回抢购失败。