---
title: "[Note]Creational-Simple Factory"
date: 2022-03-27T16:42:45+08:00
draft: false
toc: false
tags:
  - creational
  - learning
categories:
  - programming
  - design pattern
author: jackson.tmm
---

## 什麼是Simple Factory(簡單工廠)呢?
> 簡單而言就是: 由一個工廠來生產全部產品
> 定義:建立一個接口,讓子類自己決定實現哪一個Factory,重點在於工廠，透過工廠的Static method 進行生成的Object

#### 簡單例子
*注: 以及程式單純用於解釋，並不能實際執行*  
```
透過Factory 類的Static 方法調用方法，傳入參數生成相關的Product
```
```c++
class Product{
 public:
 	virtual void someMethod() = 0
}

class ProductA : public Product{
 public:
 	void someMethod(){
         //TODO For ProductA
     }
}

class ProductB : Public Product{
 public:
 	void someMethod(){
         //TODO for ProductB
     }
}

class Factory{
 public:
 	static Product* getProduct(string type){
         if(type == "A"){
             return new ProductA();
         }
         else if(type == "B"){
             return new ProductB();
         }
         return null;
     }
}

int main(){
 Product* A = Factory::getProduct("A"); //get ProductA instance
 Product* B = Factory::getProduct("B"); //get ProductB instance
}
```
---
### 優點
* Client可以自行決定生產哪個Product
* Client免除直接建立Product的責任，只僅僅*消費*產品
* Client無需關心具體的類名與相關設置參數

### 缺點
* Factory 的職責太重。所有的Product 都由一個Object 生產，一旦不能正常工作，便會影響整個系統的運作
* 如有不同的Product會增加系統中的class的數目，增加了系統複雜度以及理解難度
* 擴展十分困難，添加需要修改Factory的邏輯。如果生存的Product較多時，可能會使其邏輯過於複雜，不利於系統的擴展與維護


### 違背原則
* *違背了Open-Close Principe*
  * 如果有新的Product 需要生成，就必修要修改Factory類

---
參考資料:  
[史上最全设计模式导学目录（完整版）](https://blog.csdn.net/LoveLion/article/details/17517213)