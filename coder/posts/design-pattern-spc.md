+++ 
draft = false
date = 2022-03-21T13:00:27+08:00
title = "[筆記]設計模式-原則"
description= "學習設計模式之原則以及種類"
authors= ["jackson.tmm"]
tags= ["learning-note","programming"]
categories= ["OOP","degin-pattern"]
+++

## Design Patterns(設計模式)

> 設計模式(Design Pattern) 是對軟體設計中普遍存在（反覆出現）的各種問題，所提出的解決方案。
>
> 設計模式是描述在各種不同的情況下，如何解決問題的一種方案。
>
> OOP設計模式通常以類別或者物件來描述關係和相互的作用(不涉及完成應用的特定object)。
>
> 設計模式能使不穩定依賴於相對穩定、具體依賴於相對抽象，避免會引起麻煩的緊耦合。

---

| 原則(Principe) | 定義(Def) |
| :------------: | :------: |
|單一職責原則(Single Responsibility Principle) | 一個類只負責一個功能領域中的相應的原則|
|開閉原則(Open-Closed Principle) | 對擴展開發(可以繼承擴展)，對修改關閉(不能修改BaseClass)|
|里氏替換原則(Liskov Substitution Principe) | 所有引用於Base Class 的地方 都能夠透明的使用其子類的對象(以子類來替換父類)|
|依賴倒轉原則(Dependencie Inversion Principe) |細節應依賴於抽象(Abstract),抽象(Abstract)不應該依賴與細節。(也就是細節由子類來實現，父類負責定義)|
|接口隔離原則(Interface Segregation Principle) | 使用多個專用的接口(Interface),而不是使用單一的總接口.(能避免Client實現一些沒必要的功能) |
|合成複用原則(Composition Reuse Principle) | 盡量使用Object Composition(在要使用的Object的地方把用到的Object注入),而不是繼承能達到目的.(減少依賴)|
|迪米特法則(Law of Demeter) | 一個軟件實體應盡可能地與其他實體發生互相作用.(就是不用跟不是朋友(注入)的Object 直接通訊)|
---
### 創建式(6個)
| 模式(Patterns) | 簡單描述(Simple description) |
| :------------: | :-------------------------:
|簡單工廠模式(Simple Factory Pattern) |使用一個對象建立不同的對象,Client 傳入1個String 返回一個對象|
|工廠模式(Factory Pattern) | 解決SFP的問題(單一原則),改為一個工廠生產一個對象 |
|抽象工廠模式(Abstract Factory Pattern) | 把產品分類，同類型的都由一個工廠生產 |
|單例模式(Singleton Pattern) |一個系統只提供提個實例對象(Static) |
|原形模式(Prototype Pattern) | 提供方法(Clone)方法,透過複製自己(注意`Deep/Shallow Clone`)，產生相同的對象 |
|建立者模式(Builder Pattern) | 對象的建立有一個特定建立的過程`不同的build方法`(如角色的創建)，適用與創建過程相似的Product|

### 結構型模式(7個)
| 模式(Patterns) |簡單描述(Simple description)|
| :------------: | :------------------------:|
|適配器模式(Adapter Pattern) | 透過Adapter 去適配不兼容的API/接口(如名字不符合目前應用等)|
|橋接模式(Bridge Pattern) | 取代多層繼承帶來的新增具體類數量的問題,把繼承分開多個獨立維度對象,獨立變化,每個維度在透過橋接(注入的方法)進行結合 |
|組合模式(Composite Pattern) | 主要處理的對象為`Tree 結構的對象` Node跟Left的處理方法不一樣,這個模式可以讓Client一致對待Leaf和Node | 
|裝飾模式(Decorator Pattern) | 主要是解決對象複用問題,透過裝飾類對基本的對象進行一次或者多次處理裝飾,使用關聯取代繼承,給對象新增額外的職責 |
|外觀模式(Facade Pattern) | 主要解決的時候給Client 與 一個或者多個Sub-system 提供一個統一的入口,減低Client與Sub-system的溝通與系統的複雜程度 |
|享元模式(FlayWeight Pattern) | 主要解決的問題分享有相同`內部不變的狀態`的對象,無需重新新增新的而且具有相同的對象,而會隨著環境改變的狀態,不會存在對象裡,所以不會影響到對象的內部 |
|代理模式(Proxy Pattern) | 主要解決的問題為Client與對象之間提供一個中介(代理)進行不同的操作。(5種常用的代理模式) |
> 常用的代理模式
 * 遠程代理(`Remote Proxy`) -> `Ambassador(大使)` ->作為不同記憶體位置對象代理的者

  * > 為一個位於不同記憶體空間的對象提供一個本地的代理對象
    >
    > 不同的記憶體空間可以是同一台主機，也可以是另一台主機

* 虛擬代理(`Virtual Proxy`) ->作為代理生成對象的代理者

  * > 如果需要Create 一個資源消耗較大的對象,可以先就愛你了一個相對較小的對象表(代理者)，真正要被創建的對象,在用到的時候才會被真正的建立

* 保護代理(`Protect Proxy`) ->作為控制訪問的代理者

  * > 控制對一個對象的訪問(作為代理給不同用戶不用的訪問權限),可以給不同的用戶提供不同級別的使用權

* 緩衝代理(`Cache Proxy`) ->作為提供臨時儲存空間的代理者

  * > 為某一個目標操作的結果提供臨時的儲存空間(把操作結果暫存,如有相同的操作,透過代理者提供分享),以便多個客戶端可以共享這些結果

* 智能應用代理(`Smart Reference Proxy`) ->作用對象引用的代理者

  * > 當一個對象被引用時，提供一些額外的操作(如對象被調用的次數等等)

---

*待更新...*
參考來源:  
[csdn](https://blog.csdn.net/LoveLion/article/details/17517213)