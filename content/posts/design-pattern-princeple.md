---
title: "[Note]Design Pattern Princeple"
date: 2022-03-27T16:18:55+08:00
draft: false
Description: 學習設計模式是什麼以及有什麼原則
tags:
  - learning
  - note
categories: 
  - programming
  - design pattern
author: jackson.tmm
---

## 什麼是Design Pattern(設計模式)呢?
> 設計模式(Design Pattern) 是對軟體設計中普遍存在（反覆出現）的各種問題，所提出的解決方案。用於描述在各種不同的情況下，**如何解決問題的一種方案**

> OOP設計模式通常以類別或者物件來描述關係和相互的作用(不涉及完成應用的特定object)。
> 設計模式能使不穩定依賴於相對穩定、具體依賴於相對抽象，**避免會引起麻煩的緊耦合**。

## Design Pattern 有什麼原則呢?
|原則(Princeple)|定義(Def)|
|:---------------|:---|
|**單一職責原則(Single Responsibility Principle)**|一個類只負責一個功能領域中的相應的原則|
|**開閉原則(Open-Closed Principle)**|對擴展開發(可以繼承擴展)，對修改關閉(不能修改BaseClass)|
|**里氏替換原則(Liskov Substitution Principe)**|所有引用於Base Class 的地方 都能夠透明的使用其子類的對象(以子類來替換父類)|
|**依賴倒轉原則(Dependencie Inversion Principe**|細節應依賴於抽象(Abstract),抽象(Abstract)不應該依賴與細節。(也就是細節由子類來實現，父類負責定義)|
|**接口隔離原則(Interface Segregation Principle)**|使用多個專用的接口(Interface),而不是使用單一的總接口.(能避免Client實現一些沒必要的功能)|
|**合成複用原則(Composition Reuse Principle)**|盡量使用Object Composition(在要使用的Object的地方把用到的Object注入),而不是繼承能達到目的.(減少依賴)|
|**迪米特法則(Law of Demeter)**|一個軟件實體應盡可能地與其他實體發生互相作用.(就是不用跟不是朋友(注入)的Object 直接通訊)|
## Design Pattern 分成哪幾種呢?
主要分成3大類，一共23個
* 創建型模式(6個)
* 結構型模式(7個)
* 行為型模式(11個)

#### 創建型模式(6個)
1. [簡單工廠模式(Simple Factory Pattern)](/posts/2022/03/notecreational-simple-factory)
2. [工廠模式(Factory Pattern)](/posts/2022/03/notecreational-factory//)
3. 抽象工廠模式(Abstract Factory Pattern)
4. 單例模式(Singleton Pattern)
5. 原形模式(Prototype Pattern)
6. 建立者模式(Builder Pattern)
  
#### 結構型模式(7個)
1. 適配器模式(Adapter Pattern)
2. 橋接模式(Bridge Pattern)
3. 組合模式(Composite Pattern)
4. 裝飾模式(Decorator Pattern)
5. 外觀模式(Facade Pattern)
6. 享元模式(FlayWeight Pattern)
7. 代理模式(Proxy Pattern)

#### 行為型模式(11個)
1. 職責鏈模式(Chain of Responsibility Pattern)
2. 命令模式(Command Pattern)
3. 解釋器模式(Interpreter Pattern)
4. 迭代器模式(Iterator Pattern)
5. 中介者模式(Mediator Pattern)
6. 備忘錄模式(Memento Pattern)
7. 觀察者模式(Observer Pattern)
8. 狀態模式(State Pattern)
9. 策略模式(Strategy Pattern)
10. 板塊模式(Template Method Pattern)
11. 訪問者模式(Visitor Pattern)
---

參考資料:  
[史上最全设计模式导学目录（完整版）](https://blog.csdn.net/LoveLion/article/details/17517213)