# mongodb数据库

### 一、mongodb的介绍

- 非关系型数据库，是文档结构，json（字典加列表），存储大型数据
- 对sql注入语句攻击免疫。最简单的数据库。
- mongodb中有多个库，库中有多个集合，每个集合中有多条json数据。

### 二、mongodb数据库操作

- 输入mongo进入mongo数据库，端口：27017。
- show dbs——查看数据库，db——查看当前所在的位置。use 数据库——切换数据库，不存在就创建。
- 注意：mongodb命令全部是小写，区分大小写，但是MySQL，redis，规范是大写，不区分大小写。
- 如果创建的库没有数据，则这个数据库的名称是不会显示的。
- db.createCollection('student')——对当前数据库，创建集合，添加数据。db.dropDatabase()——删除当前数据库
- show collections——查看所有的集合，db.student.drop()——删除student所有的集合
- db.student.insert({name:'一个',age:18})——插入一个不存在的集合时，会自动创建集合。
- db.student.find()——查找student集合中的所插入的数据，还有insertOne，或者insertMany
- db.student.find().pretty()——内容进行分条展示。在find()括号里面加入特定的查找条件，进行特定查找。
- db.student.find({age:{$gt:18}})——查找年龄大于18岁的数据，db.student.find({age:{$gte:18}})——查找年龄大于等于18岁的数据
- $gt ——大于，$gte——大于等于， $lt ——小于，$lte——小于等于， $ne——不等于， $eq——等于
- 多个条件要用逗号隔开，db.student.find({$or[{},{},{}]})——在大括号中填入自己写的东西。
- 全文档替换——db.student.update({},{})——前面是查找出来的，后面是修改的内容，把这一条数据全部改为后面的内容。
- db.student.update({_id:7},{$set:{age:38})——只修改age字段的值。,但是只修改第一个满足条件的数据。
- db.student.update({_id:7},{$set:{age:38},{multi:true})——更新集合中所有满足条件的数据
- db.student.remove({})——默认删除所有满足条件的数据。不加数据就是全部删除，但是不会真正释放空间，还要用db.repairDatabase()释放空间
- db.student.deleteOne({})——删除一条数据，db.student.deleteMany({})——多条数据。

### 三、python操作mongodb

```python
import pymongo

class MyMongodb:
    def __init__(self,database,collection):
        '''
        :param database: 指定要链接的数据据
        :param collection: 指定集合
        '''
        self.client = pymongo.MongoClient()
        self.db = self.client[database]
        self.col = self.db[collection]

    def istype(self,data,istype1):

        '''

        :param data:传入onlyone
        :param istype1: 传入bool
        :return: none
        '''
        if not isinstance(data,istype1):
            raise TypeError


    def insert(self,data,onlyone = True):
        '''

        :param data: 要插入的数据
        :param onlyone: 是否只插入一条
        :return: none
        '''
        # if not isinstance(onlyone,bool):
        #     raise TypeError
        self.istype(onlyone,bool)
        # if onlyone:
        #     self.col.insert_one(data)
        # else:
        #     self.col.insert_many(data)

        self.col.insert_one(data) if onlyone else self.col.insert_many(data)

    def update(self,data,new_data,onlyone =True):

        '''

        :param data: 传入要修改的数据
        :param new_data: 新的数据
        :param onlyone: 判断是否改一条
        :return: none
        '''

        self.istype(onlyone,bool)

        self.col.update_one(data,{'$set':new_data}) if onlyone else self.col.update_many(data,{'$set':new_data})

    def find(self,query = None,onlyone = True):
        '''

        :param query:默认是查询全部
        :param onlyone: 判断是否只查询一条
        :return: none
        '''
        self.istype(onlyone, bool)
        res = self.col.find_one(query) if onlyone else list(self.col.find(query))

        return res

    def delete(self,data,onlyone = True):
        '''

        :param data:要删除的数据
        :param onlyone: 是否指删除一条
        :return: none
        '''
        self.istype(onlyone, bool)
        self.col.delete_one(data) if onlyone else self.col.delete_many(data)

if __name__=='__main__':
    student = MyMongodb('yige','student')
    student.insert({'name':'sige','age':85})
    # student_list = list(student.find(onlyone=false))
    # print(student_list)
    # student.insert([{'name': 'liangge', 'age': 15},{'name': 'sange', 'age': 16}],onlyone=False)
    student_list = student.find(onlyone=False)
    print(student_list)
```

