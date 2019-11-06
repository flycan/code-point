## redis 锁

redis里面的锁，其实就是设置一个string类型的值

```php

// 锁的键名
$lockKey = 'lockkey';

// 锁的值
$lockVal = time();

$lock = false;

// 设置锁
while(!$lock) {
    $lock = $redis->set($lockKey, $lockVal, ['nx', 'ex' => $ttl]);
}

// 业务操作
.
.
.

// 执行完成后 释放锁
if ($redis->get($lockKey) == $lockVal) {
    // 这里存在一个坑，del的时候，有可能已经过期，锁被其他客户端获取了
    $redis->del('lockKey');
}

// 正确的释放锁的姿势

$releaseLockScript = "if redis.call('get', KEYS[1]) == ARGV[1] then return redis.call('del', KEYS[1]) else return 0 end";

$redis->eval($releaseLockScript, [$lockKey, $lockVal], 1);
```

这里存在的一个问题是，节点A设置锁的过期时间是10秒，10秒后A还没有执行完，节点B就会获取锁

解放方法：
    额外开一个守护线程，给快要过期的锁增加过期时间

比方说，A获得锁，通知守护线程10过期时间，守护线程9秒的时候就去更新锁的过期时间，再添加10秒。若A5秒就完成任务，完成后通知守护线程，不需要为锁续命了
