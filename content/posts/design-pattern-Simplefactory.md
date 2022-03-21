+++ 
draft = false
date = 2022-03-21T13:05:53+08:00
title = "[筆記]設計模式之創建型模式-簡單工廠模式(Simple Factory Pattern)"
description= "配合例子了解並學習簡單工廠的理念"
authors= ["jackson.tmm"]
tags= ["learning-note","programming"]
categories= ["OOP","degin-pattern"]
+++

{{< notice question >}}
**什麼是簡單工廠模式(Simple Factory Pattern)**
> 提供一種建立類的方式同時隱藏內部logic的方式  
> 注意: 簡單工廠不在23個設計模式之內
**定義**
> 建立一個接口,讓子類自己決定實現哪一個Factory,其重點在於工廠，透過工廠的Static method 進行Product(繼承於同一Abstract)的Object 
{{</ notice>}}

{{< notice warning >}}
**缺點:**  
* 不符合Open-Close Principe(新增產品必須修改工廠)
{{< /notice >}}


{{< notice example >}}
**注意:以下程式單純用於講解當前設計模式,並不能運行**  

透過傳入參數到Factory 類進行不同類別的生成，但是如果要生成更多的類別,就必要要修改Factory類,**違反Open-Close 原則**
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
{{< /notice >}}

--- 
參考來源:  
[csdn](https://blog.csdn.net/LoveLion/article/details/17517213)