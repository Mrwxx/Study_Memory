## 本文章由公号【开发小鸽】发布！欢迎关注！！！
<br>

**老规矩--妹妹镇楼：**
<center>
<img src="https://img-blog.csdnimg.cn/20200721223424816.JPG"   width="20%">

## 一．	流

### （一）	概述

&nbsp;  &nbsp;  &nbsp;  &nbsp;与集合相比，流提供了一种可以让我们在更高的概念级别上指定计算任务的数据视图，通过使用流，只需要说明想要完成什么任务，而不用说明如何实现它。流遵循的是“做什么而非怎么做”的原则，不需要指定该操作应该以什么顺序或者在哪个线程中执行。
<br>


### （二）	流与集合的区别

&nbsp;  &nbsp;  &nbsp;  &nbsp;1.	流并不存储元素，这些元素可能存储在底层的集合中，或者是按需生成的；

&nbsp;  &nbsp;  &nbsp;  &nbsp;2.	流的操作不会修改其数据源，而是生成一个新的流；

&nbsp;  &nbsp;  &nbsp;  &nbsp;3.	流的操作尽可能是惰性执行的，直至需要其结果时，操作才会执行。
<br>



### （三）	操作流的典型流程

&nbsp;  &nbsp;  &nbsp;  &nbsp;包含三个阶段的操作管道：

&nbsp;  &nbsp;  &nbsp;  &nbsp;1.	创建一个流；

&nbsp;  &nbsp;  &nbsp;  &nbsp;2.	指定将初始流转换为其他流的中间操作，可能包含多个步骤；

&nbsp;  &nbsp;  &nbsp;  &nbsp;3.	应用停止操作，从而产生结果，自此之后，这个流无法继续使用了。
<br>



### （四）	流的创建

#### 1.	Stream.of(T… values)
&nbsp;  &nbsp;  &nbsp;  &nbsp;产生一个元素为给定值的流，且参数是可变长的。如下所示：

```java
Stream<String> song = Stream.of(“gently”, “down”);
```
<br>



#### 2.	Stream.empty()
&nbsp;  &nbsp;  &nbsp;  &nbsp;产生一个不包含任何元素的流。

```java
Stream<String> silence = Stream.empty();
```
<br>



#### 3.	Stream.generate(Supplier<T> s)
&nbsp;  &nbsp;  &nbsp;  &nbsp;产生一个无限流，它的值是通过反复调用函数s，可以说是lambda表达式构建的。

```java
Stream<String> echos = Stream.generate(()->”echo”);
```
<br>



#### 4.	Stream.iterate(T seed, UnaryOperator<T> f)
&nbsp;  &nbsp;  &nbsp;  &nbsp;产生一个无限流，接受一个种子值seed，以及一个函数，反复地将该函数应用到之前的结果上。如要产生一个像1,2,3…这样的无限序列，就额可以通过iterate方法：

```java
Stream<BigInteger> integers = Stream.iterate(BigInteger.ZERO, n -> n.add(BigInteger.ONE));
```
<br>



#### 5.	Stream.iterate(T seed, Predicate<? super T> hasNext, UnaryOperator<T> f)
&nbsp;  &nbsp;  &nbsp;  &nbsp;比上面的中间多了一个谓词参数，用于判断迭代应该在满足什么条件下结束，只要该谓词拒绝了某个迭代生成的值，这个流就会结束。

```java
var limit = new BigInteger(“100000”);
Stream<BigInteger> integers = Stream.iterate(BigInteger.ZERO, n -> n.compareTo(limit) > 0, n->n.add(BigInteger.ONE));
```
<br>



#### 6.	Stream.ofNullable(T t)

&nbsp;  &nbsp;  &nbsp;  &nbsp;用一个对象来创建一个很短的流，如果该对象为null， 则流的长度为0；否则，流的长度为1，即只包含该对象。

```java
Stream.ofNullable(limit);
```
<br>



#### 7.	Arrays.stream(T[] array, int startInclusive, int endExclusive)
&nbsp;  &nbsp;  &nbsp;  &nbsp;产生一个流，元素由数组中指定范围内的元素构成。

```java
List<String> words = List.of(“a”, “b”);
long count = words.stream().filter(w -> w.length() > 12).count();
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;不同的类有不同的产生流的方式，这里就不一一介绍了。
<br>



### （五）	流的转换

#### 1.	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;流的转换会产生一个新的流，它的元素派生自另一个流中的元素。
<br>



#### 2.	Stream.filter(Predicate<? super T> predicate)
&nbsp;  &nbsp;  &nbsp;  &nbsp;产生一个流，它包含当前流中所有满足谓词条件的元素。

```java
List<String> words = …;
Stream<String> long = words.stream().filter(w -> w.length()>12);
```

&nbsp;  &nbsp;  &nbsp;  &nbsp;filter中的引元是Predicate<T>，即从T到Boolean的函数。
<br>



#### 3.	Stream.map(Function<? super T, ? extends R> mapper)

&nbsp;  &nbsp;  &nbsp;  &nbsp;产生一个流，包含将mapper函数应用于当前流中所有元素所产生的结果。如将所有单词都转换成小写：

```java
Stream<String> lower = words.stream().map(String::toLowerCase);
Stream<String> lower = words.stream().map(s -> s.substring(0, 1));
```
<br>



#### 4.	Stream.flatMap(Function<? super T, ? extends Stream<? extends R>> mapper)

&nbsp;  &nbsp;  &nbsp;  &nbsp;产生一个流，通过将mapper函数应用于当前流中的所有元素所产生的结果，并且将这些结果连接在一起而获得的，每个结果都是一个流，最终连接得到一个流。
<br>



#### 5.	Stream.limit(long n)
&nbsp;  &nbsp;  &nbsp;  &nbsp;产生一个流，其中包含了当前流中最初的n个元素，经常应用于裁剪无限流的尺寸：

```java
Stream<Double> randoms = Stream.generate(Math::random).limit(100);
```
<br>



#### 6.	Stream.skip(long n)
&nbsp;  &nbsp;  &nbsp;  &nbsp;产生一个流，包含当前流中除了前n个元素以外的所有元素。
<br>



#### 7.	Stream.takeWhile(Predicate<? super T> predicate)
&nbsp;  &nbsp;  &nbsp;  &nbsp;产生一个流，它的元素是当前流中所有满足谓词条件的元素，在谓词为真时获取流中的元素，然后停止。
<br>



#### 8.	Stream.dropWhile(Predicate<? super T> predicate)
&nbsp;  &nbsp;  &nbsp;  &nbsp;产生一个流，它的元素是当谓词为真时丢弃满足谓词条件的元素，并产生一个由第一个使该谓词为假的字符开始的所有元素。
<br>



#### 9.	Stream.concat(Stream<? extends T> a, Stream<? extends T> b)
&nbsp;  &nbsp;  &nbsp;  &nbsp;产生一个流，将流a和流b连接起来，注意，a一定不能是无限的，不然流b永远都无法连接上去。
<br>



#### 10.	Stream.distinct()
&nbsp;  &nbsp;  &nbsp;  &nbsp;产生一个流，将原来的流的元素按照相同顺序剔除重复元素。
<br>



#### 11.	Stream.sorted(Comparator<? super T> comparator)

&nbsp;  &nbsp;  &nbsp;  &nbsp;产生一个流，按照排序器的排序规则排序，如对字符串排序，使得最长的字符串排在最前面：

```java
Stream<String> long = words.stream().sorted(Comparator.comparing(String::length).reversed());
```
<br>



#### 12.	Stream.peek(Consumer<? super T> action)

&nbsp;  &nbsp;  &nbsp;  &nbsp;产生一个流，与当前流中的元素相同，但是在每次获取一个元素时，都会调用函数action。

```java
Object[] powers = Stream.iterate(1.0, p -> p*2).peek(e ->System.out.println(e)).limit(29).toArray();
```
<br>



### （六）	流的约简

#### 1.	概述
&nbsp;  &nbsp;  &nbsp;  &nbsp;从流数据中获取答案的方式称为约简，是一种终结操作，将流约简为可以在程序中使用的非流值。
<br>



#### 2.	Stream.max(Comparator<? super T> comparator)
&nbsp;  &nbsp;  &nbsp;  &nbsp;产生这个流的最大元素，使用比较器的排序原则，如果这个流为空，则产生一个空的Optional对象。
<br>



#### 3.	Stream.min(Comparator<? super T> comparator)
&nbsp;  &nbsp;  &nbsp;  &nbsp;产生这个流的最小元素，使用比较器的排序原则，如果这个流为空，则产生一个空的Optional对象。
<br>



#### 4.	Stream.findFirst()
&nbsp;  &nbsp;  &nbsp;  &nbsp;返回流中的第一个值。

```java
Optional<String> starts = words.filter(s->s.startsWith(“Q”)).findFirst();
```
<br>



#### 5.	Stream.findAny()
&nbsp;  &nbsp;  &nbsp;  &nbsp;返回这个流中的任意元素，通常用于并行处理流，因为可以任意地匹配。
<br>



#### 6.	Stream.anyMatch(Predicate<? super T> predicate)
&nbsp;  &nbsp;  &nbsp;  &nbsp;流中任意元素能够匹配给定的谓词时返回true。
<br>



#### 7.	Stream.allMatch(Predicate<? super T> predicate)
&nbsp;  &nbsp;  &nbsp;  &nbsp;流中所有元素都匹配谓词时返回true。
<br>



#### 8.	Stream.noneMatch(Predicate<? super T> preidicate)
&nbsp;  &nbsp;  &nbsp;  &nbsp;流中没有元素匹配谓词时返回true。











