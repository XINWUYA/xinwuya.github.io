# 腾讯深圳天美（43分钟）

## 一、自我介绍及项目介绍

（项目没有问问题，言下之意是说我在介绍里已经说了）

<font color=#FF8C00></font>

## 二、C++

1. 有3个C++ class的类，A是基类，B继承A，C继承B。

   1）先new一个C类对象的实例，然后再释放，构造和析构的顺序是什么？

   ​	<font color=#FF8C00>构造顺序：A→B→C</font>

   ​	<font color=#FF8C00>析构顺序：C→B→A</font>

   2）现在3个类都实现了一个叫do的虚函数，三个类的析构函数分别调了do函数，new的还是C的实例，析构时当它调到B类的析构函数时，它调的do函数会是ABC哪个类的do函数?

   ​	<font color=#FF8C00>B的do函数</font>

   ```c++
   #include <iostream>
   
   class A
   {
   public:
   	A() { std::cout << "A constructor." << std::endl; }
   	virtual ~A() { std::cout << "A destructor." << std::endl; doSomething(); }
   
   	virtual void doSomething(){ std::cout << "A doSomething." << std::endl; }
   };
   
   class B : public A
   {
   public:
   	B() { std::cout << "B constructor." << std::endl; }
   	virtual ~B() { std::cout << "B destructor." << std::endl; doSomething(); }
   
   	virtual void doSomething(){ std::cout << "B doSomething." << std::endl; }
   };
   
   class C : public B
   {
   public:
   	C() { std::cout << "C constructor." << std::endl; }
   	~C() { std::cout << "C destructor." << std::endl; doSomething(); }
   
   	void doSomething(){ std::cout << "C doSomething." << std::endl; }
   };
   
   int main()
   {
   	C* pTest = new C();
   	delete pTest;
   
   	return 0;
   }
   ```

   结果为：

   ![](images/interview_tianmei_shenzhen_1_01.png)

   

   3）当一个虚函数里没有写任何代码，编译器会自动生成什么样的数据代码？也就是对这个对象进行什么样的操作和处理？虚函数表会进行什么样的操作？

   ​	

   4）这个类的对象是怎么调用虚表的？

   ​	<font color=#FF8C00>对象的前4个字节存储了虚表的指针（32位系统）</font>

2. new/delete和malloc/free的区别和关联？new和malloc实现上有区别么？delete[]不写数组长度怎么知道数组的长度？

   1）区别：

   2）

3. new和malloc在写字节越界会发生非法写入异常么（不是stl容器的安全检查）

4. 内存分页机制和内存虚拟地址向物理地址的映射

5. stl常用的4个容器，vector、list、map、unordered_map四个容器的算法，常用操作的耗时，适合使用的场合，依次来说。

6. 红黑树是一棵平衡二叉树么？为什么会分红黑结点，目的是为了解决算法上什么问题，插入和删除的时候会对树的结点会做什么操作？红黑树每个结点是怎么构造的，30~40万个结点有多少层？

7. hash碰撞怎么解决？开放地址法怎么实现的？

8. 假设要存储一个数据集，key和value都是32位整型，数据量大约有30~40万，对查找不在意，在意的时存储效率，使用map和unordered_map哪个存储空间比较少？

## 三、图形学

1. 空间变换过程？裁剪坐标系的定义域，z轴的远近是正还是负？视口变换是等同于屏幕了么？

   

2. 投影变换有几种，透视投影的参数有哪些？

3. Phong模型和PBR的区别？能量守恒怎么解释？Phong模型为什么不会守恒（diffuse和specular是分开计算的，不会因为specular强，diffuse变弱）

4. D,F,G的含义,Frenel现象，PBR为什么会将金属材质和非金属材质分开？为什么纯金属只有镜面反射？

5. 说明一下深度测试？深度检测是在管线哪一步之后进行的，为什么在片元着色器之后，除了写入会影响深度测试，还有其他的么？打了透明，他说那就不开深度测试就是了，除了透明呢？Alpha测试？

6. early-z？

7. VBO,VAO,VAO存的什么信息，FBO，OpenGL做后期处理除了创建FBO，还会创建额外的Buffer？

