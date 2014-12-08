### JavaScript-oo

#### 类

类（Class）定义了一件事物的抽象特点。通常来说，类定义了事物的属性和它可以做到的（它的行为）。
举例来说，“狗”这个类会包含狗的一切基础特征，即所有“狗”都共有的特征或行为，例如它的孕育、毛皮颜色和吠叫的能力。
类可以为程序提供模版和结构。一个类的方法和属性被称为“成员”。

在javascript中class是保留字，没有class如何定义类？利用javascript中的function关键字，
类名首字母一般采用大写，下面，我来定义User类。

```
function User(userid,uname,uage,uscore){
	     this._userid=userid;
	     this._name=uname;
	     this._age=uage;
	     this._score=uscore;
	}

```
