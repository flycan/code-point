PHP研发面试题目


时间 9/5 20:05 ~ 9/5 21:15

1、 一般SQL数据库中，什么情況该加索引(Index) – 优劣分别是？

【自己的答案】

```
- 表数据量大
- 查询频率高
- 需要提升查询速度
优
索引可以提升查询速度
缺
索引创建消耗资源，占用硬盘空间
```
【引用】(https://blog.csdn.net/MiracleWW/article/details/53352738)
```
1.什么样的表跟列要建立索引：
总的来说就是数据量大的，经常进行查询操作的表要建立索引

2.为什么要建立索引，即索引的优点：
①  建立索引的列可以保证行的唯一性，生成唯一的rowId
②  建立索引可以有效缩短数据的检索时间
③  建立索引可以加快表与表之间的连接
④  为用来排序或者是分组的字段添加索引可以加快分组和排序顺序

3.索引的缺点：
①  创建索引和维护索引需要时间成本，这个成本随着数据量的增加而加大
②  创建索引和维护索引需要空间成本，每一条索引都要占据数据库的物理存储空间，数据量越大，占用空间也越大（数据表占据的是数据库的数据空间）
③  会降低表的增删改的效率，因为每次增删改索引需要进行动态维护，导致时间变长
```

2、 Web安全的SQL Injection 是一个什么样的问题？在一般PHP/Mysql的软件中，应该採取什么方法解决？

【自己的答案】

```
SQL注入通常是用户登录提交过来的表单数据中包含SQL语句，这些语句在作为sql条件时，会使sql条件始终为真，从而在密码输入错误的情况下也能登录成功

相应的措施
- 始终不要相信用户的输入，对输入做关键字检查
- 在拼接SQL语句时，不要把用户输入的账号密码都放在where条件中，应先查询用户，找到后再核对密码
- 使用PDO预处理
```


3、 长条图(柱状图/Bar Chart)、圆饼图 ：(Pie Chart)、折线图(Line Chart)各有什么用途和特性？请就售后服务每月满意度报告、年度各品牌的的营业额、季度快递出货分佈，分析一下用哪种报表合适，以及为什么？

【自己的答案】

```
- 长条图
特点：可以很直观的看出各部分的数量，方便比较
用途：展示不同产品某一时期的销量或者营业额等数据

- 圆饼图 
特点 ： 可以很清晰的看出各个部分占用的比例
用途 ： 展示各种产品某一时期的销售比例

- 折线图 
特点：展示不同时期的变化走势
用途 ： 显示某一个产品在一段时间内的销售走势

售后服务每月满意度报告 使用`折线图`，该报表重点关注连续月份的满意度，是对同一对象的不同时期的统计，使用`折线图`能很清晰的反应出来

年度各品牌的的营业额 使用`长条图`，`长条图` 可以方便的看出不同产品的销量

季度快递出货分佈 使用`圆饼图`，出货分佈的核心是统计分布情况，不关心具体数值，关系分布比例，使用`圆饼图`合适
```

4、 The input is an English sentence as a string, we need a function - “function convert($input)” that can transform it as described below and return a new string.
For example:

Input: Many people spell MySQL incorrectly

Output: Ynam elpoep lleps LqSYM yltcerrocni

【自己的答案】

```php
<?php
function convert($input)
{
	// 1. 分割每一个单词
	$words = explode(' ', $input);
	
	$newWords = [];
	
	// 2. 反转每一个单词
	foreach($words as $word) {
		$newWords[] = convert_word($word);
	}

	return implode($newWords,' ');
}

function convert_word($word)
{
	$len = strlen($word);

	$mid = (int)$len/2;

	for($i=0; $i < $mid; $i++){
		
		$leftIsUpper = preg_match('/[A-Z]/', $word[$i]);
		$rightIsUpper = preg_match('/[A-Z]/', $word[$len-$i-1]);
		
		$tmp = $word[$i];			
		$word[$i] = $word[$len-$i-1];
		$word[$len-$i-1] = $tmp;

		if($leftIsUpper) {
			$word[$i] = strtoupper($word[$i]);
		}else{
			$word[$i] = strtolower($word[$i]);
		}

		if($rightIsUpper) {
			$word[$len-$i-1] = strtoupper($word[$len-$i-1]);
		}else{
			$word[$len-$i-1] = strtolower($word[$len-$i-1]);
		}
	}
	return $word;
}
```