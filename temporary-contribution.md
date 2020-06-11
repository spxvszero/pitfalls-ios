# Temporary Contribution


### 有关 UITableHeaderView 展开收缩动画的坑

#### 环境参数：

```
*
```

#### 问题分析：

iOS `UITableView` 控件中的 `TableHeaderView`，在实现展开文字和收缩文字动画时候可能会出现以下异常的情况。

1. 无法撑开 `UITableViewCell` ，即使改变了 `TableHeaderView` 的高度
2. 带 `UITableViewSectionView` 的情况下实现动画，会出现 `UITableViewSectionView` 与 `UITableViewCell` 分离的情况
3. 当 `TableHeaderView`的背景为透明的时候，展开的文字会很明显出现在下方 `UITableViewCell` 上面，然后动画才继续直到结束

#### 解决方法：

1. 这种情况是因为单纯改变 `TableHeaderView` 的高度并不会通知 `UITableView` 更新布局，需要在 `AnimationBlock` 中重新将 `TableHeaderView` 赋值给 `UITableView` 才能不断刷新布局。
2. 这种情况是因为使用了 `[UITableView beginUpdates]` 和 `[UITableView endUpdates]` 方法，这个方法会通知 `UITableViewCell` 跟随 `UITableView` 实现动画效果，但是带来的问题就是`UITableViewSectionView` 和 `UITableViewCell` 的分离，所以不用此方法比较合理
3. 该问题是因为 `TableHeaderView` 允许超出边缘的视图，但是修改为 `maskToBounds` 为 `YES` 之后发现并没有改进，进一步的解决办法是在 `TableheaderView` 上层再次添加一个底部空白的 `UIView`，然后修改此 `UIView` 的 `maskToBounds` 属性，即可解决



### Swift 方法中传参无法修改的问题

#### 环境参数：

```
Xcode 11.1 Swift 5
```

#### 问题分析：

有如下方法：

```
struct Person {
	var name:String
	var age:Int
}
func growUp(person:Person)-->Person{
	person.age = person.age + 1
	return person
}
```
编译器报如下错误 

Cannot assign through subscript: 'ver' is a 'let' constant

在 Swift 3 之前，只需要修改入参为：

```
func growUp(var person:Person)-->Person{
	person.age = person.age + 1
	return person
}

```

即可解决，但是这种方法在 Swift 5 下会报如下错误

'var' as a parameter attribute is not allowed

#### 解决方法：

解决办法有两种，一种是通过构建新的拷贝对象，然后改变值返回这个新的对象。

```
func growUp(person:Person)-->Person{
	var mutePerson = person
	mutePerson = person.age + 1
	return mutePerson
}
```

此种方式也可以通过重新构造一个新的结构体再一一赋值来实现。

另一种方法就是通过关键词 inout 来强制要求传参为指针。

```
func growUp(person:inout Person)-->Person{
	person = person.age + 1
	return person
}
```

在 Objective-C 中，传参一般是指针，而在 Swift 中这类值属于值类型，并不容易发现这类错误，而且 Objective-C 中编译器是不会对此类问题做约束的，但是 Swift 中却会引起编译器的报错。