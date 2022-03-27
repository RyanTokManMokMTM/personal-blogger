---
title: "[Note]Creational-Factory"
date: 2022-03-27T16:55:17+08:00
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

## 什麼是Factory(工廠)呢?
> 定義:建立一個接口,讓子類自己決定實現哪一個Factory,其**重點是繼承了Simple Factory Patterns的優點,同時解決了它的問題**

#### 簡單例子
*注: 以及程式單純用於解釋，並不能實際執行*  
```
可透過子類繼承Factory的抽象類自主決定生產什麼樣的Product。  
生產的Product在透過里氏替換原則(Lisko Subsititution princeple)替換成繼承與Logger的子類，即可換成的需要的Logger。
```
```c++
class Logger{
    public:
    	virtual void wirteLog() = 0;
}

//Implement
class DatabaseLogger: public Logger{
    pubic:
    	void wirteLog(){
            //Wirte DB log message
        }
}

class FileLogger : public Logger{
    public:
    	void wirteLog(){
            //Write File Log message
        }
}

//Abstruct Factory
class Factory{
    public:
    	virtual Logger* createLogger() = 0;
}
//不可以是Static靜態，因為子類是動態實現父類的函數(static 沒有this指標)
//Implement
class FileLoggerFactory : public Factory{
    public:
    	Logger* createLogger(){
            //init file logger 
            //TODO init etc

            //create file logger
            Logger* logger = new FileLogger();
            //TODO Create file etc

            return Logger;
        }
}

class DabatabaseFactory : public Factory{
    public:
        Logger* createLogger(){
           	//Connect to db
            //Create db logger etc...
            Logger* logger = new DatabaseLogger();
            //init this logger etc...
            return logger
        }
}

//Client to use
int main(){
    Factory *factory = new FileLoggerFactory();
    Logger *logger = factory->createLogger(); //return the Product that it is initialized
    Logger->wirteLog();
    return;

    //如果想讓系統有更多靈活性和課擴展性，可以透過xml來進行配置，不需透過修過client的代碼
    //只需更新xml中的設置，在代碼中新增新的Product 以及新增的Factory 並重新編譯便可

    //除了默認的設置方法外，還想透過傳入String的方法來自定義設計，例如連接要DB，文件的路徑等等
    //可以透過Override Abstruct class 的func來進行設置
    /*
    	class Factory{
            public:
            	virtual Logger* createLogger() = 0;
            	virtual Logger* createLogger(string config) = 0;
            	virtual Logger* createLogger(Object obj) = 0;//通過Obj裡面的成員進行設置等
		}
    */
}
```
---
### 優點
* Client無需知道Object的設置的細節，也無需知道實質Object的名稱，只需通過工廠即可
* 工廠(Factory)以及產品(Product)都是透過Polymorphism(多態)來實現，也是工廠模式的關鍵，工廠可以自主的決定要生產什麼產品，在內部進行設置產品(封裝在內部)
* 加入新的Product時無需修改Abstract class 以及 具體的工廠和產品的類，只要擴展新增即可，符合Open-Close principle
* 透過繼承抽象類使系統更容易擴展

### 缺點
* 新增產品時，要加入新的產品類以及工廠類，增加了系統的複雜度，而且需要從新編譯，增加了開銷
* 為了擴展性,加入了抽象類，Client都使用Abstract class 進行編程，增加了系統的抽象性和理解難度

---
參考資料:  
[史上最全设计模式导学目录（完整版）](https://blog.csdn.net/LoveLion/article/details/17517213)