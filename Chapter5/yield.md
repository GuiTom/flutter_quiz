# 第2节：yield

yield`语句只能用于生成器的函数。 生成器的功能以自然的方式生成数据项（如计算，从外部接收，预定义值等）。 当下一个数据项准备就绪时，`yield`语句将此项发送到数据序列，该数据序列本质上是函数的生成结果。 数据序列可以是同步的或异步的。 在Dart语言中，同步数据序列表示`Iterable`的实例。 异步数据序列表示`Stream`的实例。

1.同步数据生成器: 返回一个 [`Iterable`](https://api.dart.dev/stable/dart-core/Iterable-class.html) 对象.

~~~
Iterable<int> genIterates (int max) sync* {
  var i = 0;
  while (i < max) {
    yield i;
    i++;
  }
}
Future<void> main() async {
  var l = genIterates(10);
  print(l);//输出: (0, 1, 2, 3, 4, 5, 6, 7, 8, 9)
}
~~~

2.异步数据生成器，返回一个Steam对象

~~~
Stream<int> runToMax (int n) async* {
  int i = 0;
  while (i < n) {
    yield i;
    i++;
    await Future.delayed (Duration (seconds: 1));
  }
}
Future<void> main() async {
  Stream<int> stream = runToMax(5);
  await for (var n in stream) {
    print(n); //每隔一秒打印一个，共打印出 0 1 2 3 4
  }
}
~~~

3.。yield*后面的表达式必须表示另一个子序列。yield*所做的是将子序列的所有元素插入到当前正在构造的序列中，就好像我们为每个元素都有一个单独的yield

~~~
Iterable<int> genIterates (int max) sync* {
  var i = 0;
  while (i < max) {
    yield i;
    i++;
  }
}
Iterable<int> countDownFrom (int n) sync* {
  if (n > 0) {
    yield n;
    yield* genIterates (n - 1);
  }
}
Future<void> main() async {
var l = countDownFrom(5);
	print(l);//打印：(5, 0, 1, 2, 3)
}

~~~

