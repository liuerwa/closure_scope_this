<center>Vue</center>

一、MVVM开发模式

MVVM分为Model、View、ViewModel。

1. Model代表数据模型，数据和业务逻辑都在Model层定义
2. View代表UI视图，负责数据展示
3. ViewModel负责监听Model中数据变化并控制视图的更新，处理用户交互操作

Model与View并无实际关联，两者是通过ViewModel进行联系，Model与ViewModel有直接关联，当Model中的数据发生改变，

