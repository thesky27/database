# 一、python操作mongodb数据库

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

- self.col.insert_one(data) if onlyone else self.col.insert_many(data)——注意插入多条数据时要用列表。
- self.col.update_one(data,{'$set':new_data}) if onlyone else self.col.update_many(data,{'$set':new_data})——更新数据的时候不能全部更新，所有只需传入要更改的字典数据即可。
- res = self.col.find_one(query) if onlyone else list(self.col.find(query))——打印出数据前要先转为列表，但是只查出一个的话就不需要转列表，并且原本的返回值是字典类型的数据
          

# 二、python操作mysql数据库

```python
'''
  python 操作mysql
'''
import pymysql

db_config = {
        'host': '127.0.0.1',
        'port': 3306,
        'user': 'admin',
        'password': 'qwe123',
        'db': 'yige' ,
        'charset': 'utf8'
}

conn = pymysql.connect(**db_config)
cur = conn.cursor()   #获取游标对象
try:
    sql = 'select * from `student`'
    cur.execute(sql)
    print(cur.fetchone())
    print(cur.fetchall())
except Exception as e:
    conn.rollback()
    print(f'异常{e}，已取消操作')
else:
    conn.commit()
    print('执行成功')
finally:
    cur.close()
    conn.close()
```

- 连接MySQL，IP，端口，用户，密码，数据库，编码方式
- 获取游标对象，执行语句，记得要加分号。
- 在MySQL中开启了事务，如果没有提交就不会进行持久化
- 获取查询到的单条数据——cur.fetchone()，cur.fetchmany(2)——获取两条数据，返回的是元组

# 三、python操作redis

```python
'''
python操作redis
'''
import redis

redis_config = {
    'host':'127.0.0.1',
    'port':6379,
    'db':0,
    'decode_responses':True #默认返回的是字节类型的数据，我们应该返回的是字符串的数据
}
db = redis.StrictRedis(**redis_config)
db.set('num','1234')
db.lpush('li',1)
db.lrange('li',0,-1)

db.set('test',12,ex=60)
db.keys()

```



# 四、登陆操作——redis加MySQL

```python
class User:
    def __init__(self,id,name,age,sex,class_id):
        self.id = id
        self.name =name
        self.age = age
        self.sex =sex
        self.class_id =class_id
    def eat(self):
        print(f'{self.name}在吃饭呢')

if __name__=='__main__':
    student_id = input('请输入学号：')
    name = input('请输入姓名：')
    import pymysql
    import redis
    import random

    redis_config = {
        'host': '127.0.0.1',
        'port': 6379,
        'db': 0,
        'decode_responses': True
    }
    db = redis.StrictRedis(**redis_config)
    db.set(f'{student_id}:verfication',str(random.randint(1000,9999)),ex=60)   #生成验证码
    verficatiin = input("请输入验证码")
    if verficatiin==db.get(f'{student_id}:verfication'):
        print('验证码错误')
        raise Exception
    host_ig = {
        'host': '127.0.0.1',
        'port': 3306,
        'user': 'admin',
        'password': 'qwe123',
        'db': 'yige',
        'charset': 'stf-8',

    }
    conn = pymysql.connect(**host_ig)
    cur = conn.cursor()
    try:
        sql = f'select * FROM `student` where `id`={student_id} and `name`="{name}";'
        cur.execute(sql)
        res = cur.fetchone()
        if res:
            now_student =  User(**res)
        else:
            print('账号密码错误')
            raise Exception
    except Exception as e:
        conn.rollback()
        print("出现异常")
    else:
        conn.commit()
    finally:
        cur.close()
        conn.close()

    if now_student:
        print(f'当前登录的用户是：{now_student.name}')
        now_student.eat()

```

