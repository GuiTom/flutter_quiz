# 第1节：线程模型

### 如何处理耗时的操作? 不同语言有不同的处理方式

- 多线程。比如 Java、C++，就是开启一个新的线程，将耗时操作放在新的线程里面处理，再通过线程间通信的方式，将拿到的数据传给主线程处理。

- 单线程+事件循环。比如 JavaScript、Dart 都是主要基于单线程加事件循环来完成耗时操作的处理

- dart的多线程对应多isolate(isolate的显示调用和隐式调用)

##### 隐式调用

~~~ 
int expensiveOperation(int value) {
  // 执行一些计算密集型的任务
  return value * value;
}

Future<void> main() async {
  // 在后台Isolate中执行expensiveOperation函数
  int result = await compute(expensiveOperation, 10);

  // 处理结果
  print('Result: $result');
}
~~~

##### 显式调用

~~~
import 'dart:isolate';

void task1(SendPort sendPort) {
  // 在这里执行任务1
  int result = 0;
  for (int i = 0; i < 5; i++) {
    result += i;
  }

  // 将结果发送回主Isolate
  sendPort.send(result);
}

void task2(SendPort sendPort) {
  // 在这里执行任务2
  int result = 0;
  for (int i = 0; i < 10; i++) {
    result -= i;
  }

  // 将结果发送回主Isolate
  sendPort.send(result);
}

Future<void> main() async {
  ReceivePort receivePort1 = ReceivePort();
  ReceivePort receivePort2 = ReceivePort();

  // 创建 Isolate 并分别将任务1和任务2分配给它们
  Isolate isolate1 = await Isolate.spawn(task1, receivePort1.sendPort);
  Isolate isolate2 = await Isolate.spawn(task2, receivePort2.sendPort);

  // 等待 Isolate 返回结果
  int result1 = await receivePort1.first;
  int result2 = await receivePort2.first;

  print('Result from Isolate 1: $result1');//Result from Isolate 1: 10
  print('Result from Isolate 2: $result2');//Result from Isolate 2: -45

  // 关闭 Isolate
  isolate1.kill();
  isolate2.kill();
}
~~~

