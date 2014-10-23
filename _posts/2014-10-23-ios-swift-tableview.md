---
layout: post
title: swift学习：TableView
description: TableView是IOS一个非常常见的组件，这里学习一下如何使用它
categories: ios
---

1.添加一个TableView

在xcode中打开Main.storyboard文件，从Object Library选择（control+drag+click）TableView拖进去，可以定位，调整它的大小

2.设置TableView的delegate和datasource

在interface builder里很容易做到。按住control，然后点击拖拽tableview到storyboard’s hierarchy得“View Controller”，选择‘data source’。 ‘delegate’同样如此

3、修改ViewController的类定义

把
```
class ViewController: UIViewController {
to this:
```
修改为
```
class ViewController: UIViewController, UITableViewDataSource, UITableViewDelegate {
```

command+click 这2个protocol，在它们的顶部可以看到必须要实现的方法，这里，我们必须要实现的方法有2个：

```
func tableView(tableView: UITableView!, numberOfRowsInSection section: Int) -> Int
func tableView(tableView: UITableView!, cellForRowAtIndexPath indexPath: NSIndexPath!) -> UITableViewCell!

```

例如，

```
func tableView(tableView: UITableView, numberOfRowsInSection section:    Int) -> Int {
   return 10
}
func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
   let cell: UITableViewCell = UITableViewCell(style: UITableViewCellStyle.Subtitle, reuseIdentifier: "MyTestCell")
 
   cell.textLabel?.text = "Row #\(indexPath.row)"
   cell.detailTextLabel?.text = "Subtitle #\(indexPath.row)"
 
   return cell
}
```
第一个是确定section里有多少行，在上面这个例子里，我们硬编码返回10，但是通常来说，它会是一个数组的长度

第二个方法非常关键。这里，我们创建了一个UITableViewCell的实例cell，采用Subtitle cell样式。然后，给cell的text赋值。

detailTextLabel只有在Subtitle cell样式里才会有

