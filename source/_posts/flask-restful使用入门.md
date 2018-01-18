title: flask-restful使用入门
author: raiseyang
date: 2017-11-12 13:29:31
tags:
---
# flask-restful使用入门

标签（空格分隔）： python

---
## 安装
```
pip install flask-restful
```

## Hello World!
```
from flask import Flask
from flask_restful import Resource, Api

app = Flask(__name__) # 初始化Flash框架,获取其app
api = Api(app) # flask_restful的Api

class HelloWorld(Resource): # 继承Resource类
    def get(self):
        return {'hello': 'world'}

api.add_resource(HelloWorld, '/') # 过滤url(将所有uri指向HelloWorld)

if __name__ == '__main__':
    app.run(debug=True) # debug模式运行Flask
```
直接运行上述代码之后,在控制台上可以看到如下信息:
```
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 598-221-953
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```
直接访问该本地地址将打印出`{'hello': 'world'}`
```
D:\>curl http://127.0.0.1:5000/
{
    "hello": "world"
}
```

<!-- more -->
## GET,POST,PUT等协议支持
```
from flask import Flask, request
from flask_restful import Resource, Api

app = Flask(__name__)
api = Api(app)

todos = {}

class TodoSimple(Resource):
    def get(self, todo_id): # GET请求
        return {todo_id: todos[todo_id]}

    def put(self, todo_id):# PUT请求
        todos[todo_id] = request.form['data'] # 读取data字段
        return {todo_id: todos[todo_id]}

api.add_resource(TodoSimple, '/<string:todo_id>') # rest风格uri:将过滤`/1`资源

if __name__ == '__main__':
    app.run(debug=True)
```
我们可以这样使用它:
```
D:\>curl http://localhost:5000/todo1 -d "data=I'm raise" -X PUT
{
    "todo1": "I'm raise"
}

D:\>curl http://localhost:5000/todo1
{
    "todo1": "I'm raise"
}
```
> Flask中使用`request.headers`类字典对象来获取请求头信息，使用`request.data`获取请求数据，如果发送类型是`application/json`，则可以使用`request.get_json()`来获取JSON数据。



参考:
[flask-restful官方文档](https://flask-restful.readthedocs.io/en/latest/)
[使用Flask实现RESTful API（译）](http://www.jianshu.com/p/f3624eebff80)
[Flask中文文档0.10](http://docs.jinkan.org/docs/flask/)
[Flask文档0.12](http://flask.pocoo.org/docs/0.12/)