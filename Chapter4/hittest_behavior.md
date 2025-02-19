####  histTestBehavor有三个属性：

1. behavior 为 deferToChild 时，hitTestSelf 返回 false，当前组件是否能通过命中测试完全取决于 hitTestChildren 的返回值。也就是说只要有一个子节点通过命中测试，则当前组件便会通过命中测试。
2. behavior 为 opaque 时，hitTestSelf 返回 true，hitTarget 值始终为 true，当前组件通过命中测试。
3. behavior 为 translucent 时，hitTestSelf 返回 false，hitTarget 值此时取决于 hitTestChildren 的返回值，但是无论 hitTarget 值是什么，当前节点都会被添加到 HitTestResult 中。

注意，behavior 为 opaque 和 translucent 时当前组件都会通过命中测试，它们的区别是 hitTest() 的返回值（hitTarget ）可能不同，所以它们的区别就看 hitTest() 的返回值会影响什么，这个我们已经在上面详细介绍过了，下面我们通过一个实例来理解一下。



~~~
//在命中测试过程中 Listener 组件如何表现。
enum HitTestBehavior {
  // 组件是否通过命中测试取决于子组件是否通过命中测试
  deferToChild,
  // 组件必然会通过命中测试，同时其 hitTest 返回值始终为 true
  opaque,
  // 组件必然会通过命中测试，但其 hitTest 返回值可能为 true 也可能为 false
  translucent,
}

~~~

### 例子 

当给 `Container`设置颜色的时候，点击有响应。而去掉颜色，则点击无响应。若去掉颜色而加上behavior: HitTestBehavior.opaque,则点击也有相应。

~~~
//无响应
GestureDetector(
                  onTap: () {
                    print("onTap");
                  },
                  child: Container(
                    //color: Colors.blue,打开
                    width: 40,
                    height: 40,
                  ),
                ),
~~~





### 参考:

1.[Flutter : 关于 HitTestBehavior_flutter hittestbehavior-CSDN博客](https://blog.csdn.net/u013066292/article/details/1172840

[85)

2.[8.3 Flutter事件机制 | 《Flutter实战·第二版》 (flutterchina.club)](https://book.flutterchina.club/chapter8/hittest.html#_8-3-5-hittestbehavior)
