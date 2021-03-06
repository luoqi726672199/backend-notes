# Redis 

<div align="center"> <img src="logo.png" width="50%"/> </div><br>



## Category

* [1. What is Redis](#1-what-is-redis)
* [2. Why we use Redis](#2-why-we-use-redis)
  + [2.1 Redis 使用场景](#21-redis-----)
* [3. Redis Quickstart](#3-redis-quickstart)
  + [3.1 Installation](#31-installation)
  + [3.2 Preparation](#32-preparation)
  + [3.3 String](#33-string)
    - [3.3.1 常用命令](#331-----)
    - [3.3.2 典型场景](#332-----)
  + [3.4 Hash](#34-hash)
  + [3.5 List](#35-list)
  + [3.6 Set](#36-set)
    - [3.6.1 常用命令](#361-----)
    - [3.6.2 经典场景](#362-----)
  + [3.7 Zset](#37-zset)
    - [3.7.1 常用命令](#371-----)
* [4. SpringBoot 整合 redis](#4-springboot----redis)
  + [4.1 Quickstart](#41-quickstart)





## 1. What is Redis

`Redis` 是一种基于 `key-value` 的 `NoSQL` 数据库，没有数据表的概念

> Q：
>
> 关系型数据库与非关系型数据库之间有什么关系？
>
> A：
>
> 区别：
>
> 1. 关系型数据库：数据之间有关系；非关系型数据库：数据与数据之间没有关系
> 2. 关系型数据库：数据存储在硬盘的文件上；非关系型数据库：数据存储在内存中
>
> 关联：
>
> 两者相辅相成



## 2. Why we use Redis

1. 速度快

   `Redis` 所有数据都是放在内存中的（避免了大量与操作系统 IO 打交道）

2. 基于 `key-value` 的数据结构服务器

3. 丰富的功能：

   1. 键过期：实现缓存
   2. 发布订阅：消息系统

4. 持久化

   将数据保存在内存中是不安全的，当发生断电或者宕机，数据极有可能丢失

   `Redis` 提供了 `RDB` 和 `AOF` 两种持久化方式，将内存的数据保存在硬盘中

5. 高可用和分布式



### 2.1 Redis 使用场景

1. 缓存

   对于一些经常用却不怎么发生改变的数据，使用缓存机制可以极大加快对数据的访问速度且降低后端数据源的压力

   对于客户端发来的数据，先找缓存：

   有：直接读取

   没有：向数据库查询，读取并存到缓存中

2. 排行榜系统

3. 计数器应用

4. 社交网络

5. 消息队列

<div align="center"> <img src="image-20200503171756162.png" width="80%"/> </div><br>



## 3. Redis Quickstart

### 3.1 Installation

:warning:注意

官网不提供 `Windows` 版本

<div align="center"> <img src="image-20200503172707953.png" width="90%"/> </div><br>

当前使用版本：`Redis 2.8.9`



### 3.2 Preparation

**查看所有键**

<div align="center"> <img src="image-20200503180249686.png" width="50%"/> </div><br>

**键总数**

<div align="center"> <img src="image-20200503180348759.png" width="30%"/> </div><br>

:warning:注意

`dbsize` 的时间复杂度是 `O(1)` ，而 `keys *` 的时间复杂度是 `O(n)`，当 `Redis` 保存了大量的键时，线上环境禁止使用



**键是否存在**

<div align="center"> <img src="image-20200503180919638.png" width="40%"/> </div><br>



**删除键**


<div align="center"> <img src="image-20200503181038487.png" width="40%"/> </div><br>

删除成功：1

删除失败：2



**键过期**

<div align="center"> <img src="image-20200503181245321.png" width="50%"/> </div><br>

`ttl` 查看键剩余过期时间

大于0：剩余过期时间

-1：未设置过期时间

-2：键不存在 / 键已过期



**键的数据结构类型**

<div align="center"> <img src="image-20200503181718752.png" width="80%"/> </div><br>

在 `Redis` 中有 5 种外部的数据类型，分别为：

1. String
2. List
3. Hash
4. Set
5. Zset

其中每种都有自己的底层内部编码实现（不止一种）

这样做的好处：

1. 提供接口供开发者调用，一旦开发出更优秀的源码，无需改动外部的数据结构和命令
2. 多种内部编码实现在不同的应用场景下发挥更优势的实现



<div align="center"> <img src="redis-data-structure-types.jpeg" width="100%"/> </div><br>

### 3.3 String
<div align="center"> <img src="string.jpg" width="80%"/> </div><br>


#### 3.3.1 常用命令

`get` 和 `set`

<div align="center"> <img src="image-20200503184301360.png" width="50%"/> </div><br>

批量设置值（`get` 和 `set` 增强版）

<div align="center"> <img src="image-20200503184512248.png" width="60%"/> </div><br>

:bulb:Tips

学会批量操作，有助于提高业务效率，但是注意每次批量操作的命令数不是无节制的，如果数量过多可能造成 `Redis` 阻塞或者网络拥塞



#### 3.3.2 典型场景

**缓存**

<div align="center"> <img src="image-20200503204043055.png" width="80%"/> </div><br>

缓存通常能起到加快读取速度以及降低后端服务器压力的作用



**计数**

许多应用会利用 `Redis` 作为计数的基础工具



### 3.4 Hash

<div align="center"> <img src="hash.jpg" width="80%"/> </div><br>

**常用命令**

常见的 `set` 和 `get`

<div align="center"> <img src="image-20200503204722141.png" width="60%"/> </div><br>

查看 `key` 下的所有 `field`

<div align="center"> <img src="image-20200503205210446.png" width="60%"/> </div><br>

使用 `hdel` 删除某个或多个 `field`

用 `hkeys` 查看所有的 `field`（不要给名字误导了）

:warning:注意

是 `hdel` 而不是 `del`

`del` 会删除整个 `key` （所有的 `field`）

<div align="center"> <img src="image-20200503212215487.png" width="70%"/> </div><br>

### 3.5 List

<div align="center"> <img src="list.png" width="80%"/> </div><br>

**特点**

1. 有序（`index` 从 0 开始）

2. 可存重复元素

   

**常用方法**

**添加**

分为左插入和右插，很好理解

<div align="center"> <img src="image-20200503223318811.png" width="60%"/> </div><br>

**查找**

:warning:注意

与许多编程语言不同，这里的 `start` 和 `end` 是左闭右闭区间


<div align="center"> <img src="image-20200503223721084.png" width="60%"/> </div><br>



### 3.6 Set

集合内保存的都是 `String` 类型，有两个特点：

1. 不允许重复元素
2. 无序（不能通过索引查找值）

#### 3.6.1 常用命令

**集合内操作**

添加


<div align="center"> <img src="image-20200504203708116.png" width="60%"/> </div><br>

移除

`rem` 是 `remove` 的缩写

<div align="center"> <img src="image-20200504203828297.png" width="50%"/> </div><br>



统计个数


<div align="center"> <img src="image-20200504204033075.png" width="50%"/> </div><br>

`scard` 的时间复杂度是 `O(1)` ，它不会遍历所有元素，而是直接用 `Redis` 的内部变量



判断元素是否在集合中

<div align="center"> <img src="image-20200504204222577.png" width="50%"/> </div><br>



生成随机元素
<div align="center"> <img src="image-20200504204459292.png" width="60%"/> </div><br>

**集合外操作**

求交集

<div align="center"> <img src="image-20200504204847252.png" width="70%"/> </div><br>

求并集
<div align="center"> <img src="image-20200504204933847.png" width="60%"/> </div><br>



求差集

:warning:注意

差集有顺序先后之分

<div align="center"> <img src="image-20200504210155285.png" width="60%"/> </div><br>

#### 3.6.2 经典场景

集合类型的经典使用场景是标签 `tag` 。例如一个电商网站会对不同标签的用户做不同类型的推荐，为网站带来更多的利益



### 3.7 Zset

<div align="center"> <img src="zset.png" width="60%"/> </div><br>

特点：

1. 不允许重复元素
2. 元素可以通过 `score` 排序



:warning:注意

元素不可以重复但是 `score` 可以重复

#### 3.7.1 常用命令

**集合内**

添加成员（分数需给出）

<div align="center"> <img src="image-20200504211141482.png" width="100%"/> </div><br>

计算集合总数以及某个元素的分数

<div align="center"> <img src="image-20200504211326808.png" width="60%"/> </div><br>

计算成员排名

既然有分数，那么肯定得有排名（正序反序）

:warning:注意

排名从 0 开始


<div align="center"> <img src="image-20200504211511504.png" width="60%"/> </div><br>



## 4. SpringBoot 整合 redis

### 4.1 Quickstart

使用 `Spring data redis` 整合 `redis`

引入相关依赖

**pom.xml**

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-redis</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <dependency>
        <groupId>org.projectlombok</groupId>
        <artifactId>lombok</artifactId>
        <optional>true</optional>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
        <exclusions>
            <exclusion>
                <groupId>org.junit.vintage</groupId>
                <artifactId>junit-vintage-engine</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
</dependencies>
```



这里的 `user` 必须实现序列化接口

**User.java**

```java
@Data
public class User implements Serializable {
    private long id;
    private String username;
}
```





**UserController.java**

```java
@RequestMapping("user")
@RestController
public class UserController {

    private RedisTemplate redisTemplate;

    @Autowired
    public void setRedisTemplate(RedisTemplate redisTemplate) {
        this.redisTemplate = redisTemplate;
    }

    // create
    @PostMapping("set")
    public void set(@RequestBody User user) {
        redisTemplate.opsForValue().set("user", user);
    }

    // read one
    @GetMapping("get/{key}")
    public User get(@PathVariable String key) {
        return (User) redisTemplate.opsForValue().get(key);
    }

    // delete
    @DeleteMapping("delete/{key}")
    public boolean delete(@PathVariable String key) {
        redisTemplate.delete(key);
        return !redisTemplate.hasKey(key);
    }
}
```

添加数据

<div align="center"> <img src="image-20200615235717716.png" width="100%"/> </div><br>

根据 `key` 获取 `object` （我们设置的 `key` 是 `user`）


<div align="center"> <img src="image-20200615235923700.png" width="100%"/> </div><br>

删除数据，返回 `true` 代表删除成功
<div align="center"> <img src="image-20200616000108254.png" width="100%"/> </div><br>


