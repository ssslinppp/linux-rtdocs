# Groovy语法

## 定义变量
```
def x="abc"
def y=1
```

## 方法调用
方法调用时，可以省略括号
```
System.out.println "Hello, world"
```

## 命名参数
```
def createName(String givenName, String familyName){
	return givenName + " " + familyName;
}
```
调用
```
createName familyName="Lee", givenName="Tom"
```

## 支持默认参数值
```
def sayHello(String name="Tom"){
	print "Hello ${name}"
}

sayHello() //此时括号不能省略
```

## 单引号和双引号
```
def name= 'world'
print "Hello ${name}"   //结果：Hello world
print 'Hello ${name}'   //结果：Hello ${name}
```

## 三引号
- 三单引号： 支持换行，不支持插值     
- 三双引号： 支持换行，不支持插值   

```
def name='world'

//三单引号
def aString='''line one
line two
line three
${name}
'''

// 三双引号
def aString = """line one
line two
line three
${name}
"""
```