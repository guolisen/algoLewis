[TOC]



# SOLID

## 单一责任原则（Single Responsibility Principle）

## 开放封闭原则（Open Close Principle）

某个设计在遇到需求变化下，不需要修改该原有代码，而只需要添加、继承、实现新接口，新实例即可

## 里氏原则 (Liskov Substitution Principle)

## 接口分离原则

## 依赖反转原则（Dependency inversion principle）

## 迪米特法则

有限知识，调用者，不应该知道被动用者过多的细节

Bad：

```
Car->getFuelTank()->addOil();
```

Good:

```
Car->addOil();
```





# UML 符号

"+" Public

"-" Private

"#" Protected

# OOD

## 封装

## 继承

"is a"

强耦合，父类任何修改，都可能对子类造成未知影响，完全不可控。

子类很难单一职责，父类里面很多功能子类可能不需要

## 多态

