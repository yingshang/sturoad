## models的meta
```
1,unique_together
unique_together这个选项用于：当你需要通过两个字段保持唯一性时使用。比如假设你希望，一个Person的FirstName和LastName两者的组合必须是唯一的，那么需要这样设置：
unique_together = (("first_name", "last_name"),)
一个ManyToManyField不能包含在unique_together中。如果你需要验证关联到ManyToManyField字段的唯一验证，尝试使用signal(信号)或者明确指定through属性。

2,verbose_name
verbose_name的意思很简单，就是给你的模型类起一个更可读的名字一般定义为中文，我们：
verbose_name = "学校"

3,verbose_name_plural
这个选项是指定，模型的复数形式是什么，比如：
verbose_name_plural = "学校"
如果不指定Django会自动在模型名称后加一个’s’。

4,abstract
这个属性是定义当前的模型是不是一个抽象类。所谓抽象类是不会对应数据库表的。一般我们用它来归纳一些公共属性字段，然后继承它的子类可以继承这些字段。

Options.abstract
如果abstract = True 这个model就是一个抽象类

5,app_label
这个选项只在一种情况下使用，就是你的模型不在默认的应用程序包下的models.py文件中，这时候需要指定你这个模型是哪个应用程序的。

Options.app_label
如果一个model定义在默认的models.py，例如如果你的app的models在myapp.models子模块下，你必须定义app_label让Django知道它属于哪一个app
app_label = 'myapp'

6,db_table
db_table是指定自定义数据库表名的。Django有一套默认的按照一定规则生成数据模型对应的数据库表名。
Options.db_table
定义该model在数据库中的表名称
　　db_table = 'Students'
如果你想使用自定义的表名，可以通过以下该属性
　　table_name = 'my_owner_table'

7,db_teblespace
Options.db_teblespace
定义这个model所使用的数据库表空间。如果在项目的settin中定义那么它会使用这个值

8,get_last_by
Options.get_latest_by
在model中指定一个DateField或者DateTimeField。这个设置让你在使用model的Manager上的lastest方法时，默认使用指定字段来排序

9,managed
Options.managed
默认值为True，这意味着Django可以使用syncdb和reset命令来创建或移除对应的数据库。默认值为True,如果你不希望这么做，可以把manage的值设置为False

10,order_with_repect_to
这个选项一般用于多对多的关系中，它指向一个关联对象，就是说关联对象找到这个对象后它是经过排序的。指定这个属性后你会得到一个get_xxx_order()和set_xxx_order()的方法，通过它们你可以设置或者回去排序的对象

 
11,ordering
这个字段是告诉Django模型对象返回的记录结果集是按照哪个字段排序的。这是一个字符串的元组或列表，没有一个字符串都是一个字段和用一个可选的表明降序的'-'构成。当字段名前面没有'-'时，将默认使用升序排列。使用'?'将会随机排列

ordering=['order_date'] # 按订单升序排列
ordering=("id",) #按照订单id升序排列
ordering=['-order_date'] # 按订单降序排列，-表示降序
ordering=['?order_date'] # 随机排序，？表示随机
ordering=['-pub_date','author'] # 以pub_date为降序，在以author升序排列
 
12,permissions
permissions主要是为了在Django Admin管理模块下使用的，如果你设置了这个属性可以让指定的方法权限描述更清晰可读。Django自动为每个设置了admin的对象创建添加，删除和修改的权限。
permissions = (('can_deliver_pizzas','Can deliver pizzas'))

 
13,proxy
这是为了实现代理模型使用的，如果proxy = True,表示model是其父的代理 model 
```