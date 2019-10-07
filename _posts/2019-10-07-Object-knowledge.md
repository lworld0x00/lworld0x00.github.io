[TOC]

### 类和类的关系

#### Composition(复合) 表示has-a

![image-20190129200952268](/img/image-20190129200952268.png)

上图所表示的`composition`关系为： `queue`这个类中有`deque`类。Ps. `deque`功能比较强大，两端进出。

具体代码如下所示

```cpp
template <class T>
    class queue{
        ...
        protected:
       		 deque<T> c;
        public:
        //以下完全利用c的操作函数完成
        	 bool empty() const { return c.empty(); }
        	 size_type size() const { return c.size(); }
        	 reference front() { return c.front(); }
        	 reference back() { return c.back(); }
        
        //
        	 void push(const value_type& x) { c.push_back(x); }
           void pop() { c.pop_front(); }
    };
```

适用环境：

已经存在一个功能强大的类，当新类需要使用其中的某些方法的时候，则可以使用 `composition`。 这种设计模式被称之为 `Adapter`。



从内存的角度看 `composition`的关系

![image-20190129205147049](/img/image-20190129205147049.png)



##### 复合关系下的构造和析构



![image-20190129205259721](/img/image-20190129205259721.png)

构造函数由内到外

`Container`的构造函数首先要调用 `Component`的`default`的构造函数，然后再执行自己

```cpp
Container::Container(...): Component() { ... };
```



析构由外而内

`Container` 的析构函数首先执行自己，然后才调用`Component`的析构函数

```cpp
Container::~Container(..) {.. ~Component() };
```

#### 委托关系 delegation---> composition by reference

代码如下所示

```cpp
class StringRep;
class String{
    public:
    	String();
    	String(const char* s);
    	String(const String& s);
    	String &operator= (const String& s);
    	~String();
    ....
    private:
    	StringRep* rep; //不是真实的拥有StringRep，只是拥有指针
};

```

和`composition`相比，两个类的生命周不相同— 需要的时候才创建。

```cpp

Handle/Body (plmpl)
class StringRep{
    friend class String;
    StringRep(const char* s);
    ~StringRep();
    int count;
    char* rep;
};

String::String(){...}

```

![image-20190129212434464](/img/image-20190129212434464.png)



上图展示了两个类的关系，这种设计模式称之为 `Handle/Body`，同时也称之为编译防火墙。我们只用编译右边，不需要编译左边。这就是说，类调用的接口不变。



Copy-On-Write



#### 继承，表示is-a

##### 继承关系下的构造和析构函数

![image-20190129213842486](/img/image-20190129213842486.png)



`base class`的`dtor`必须是`virtual`

构造由内而外

`Derived`的构造函数首先调用`base`的`default`构造函数，然后才执行自己。

```cpp
Derived:: Derived(...): Base() {...};
```



析构由外而内

`Derived`的析构函数首先执行自己，然后才调用`Base`的析构函数。

```cpp
Derived::~Derived(...) {... ~Base()};
```



