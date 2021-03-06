+++ 
draft = false
date = 2022-03-19T13:17:18+08:00
title = "簡易聊天室"
description = "簡單使用go語言實作聊天室"
tags = [
	"golang",
	"tcp",
	"websocket"
]
categories = [
	"backend",
	"networking"
]

series = ["go-backend"]
+++

# 簡易聊天室
實作的主要目的是熟悉go語言的並發機制

### TCP Server 實作

User Structure 用於記錄已連接到TCP Server是信息
```go
type User struct {
	ID          int
	Addr        string
	EnterAt     time.Time
	MessageChan chan string //individual user channel
}
```
Message Structure 用於傳送信息且跳過/無視發送者自己
```go
type Message struct {
	Owner int
	Msg   string
}
```

3個goroutine(concurrency)  
`enterChannel = make(chan *User)` 用於給全部user發送新用戶進入聊天室.  
`leavingChannel = make(chan *User)` 作用與 `enterChannel`一樣,但用於用戶離開聊天室之信息  
`messageChannel = make(chan Message, 8)` 用於用戶與用戶之間的通訊 

#### main function
* 在localhost:8080建立一個TCP Server 
* 建立一個goroutine 用於廣播
* 使用無限迴圈用於等待與接收連接TCP的用戶
```go
func main() {
	//open a TCP Server
	listen, err := net.Listen("tcp", ":2022")
	if err != nil {
		panic(listen)
	}
	log.Println("Server is on....")

	go broadcast()
	for {
		conn, err := listen.Accept() //waiting and accepting connection
		if err != nil {
			log.Println(err)
			continue
		}
		go handleConnection(conn) //passing the connection to the function
	}
}
```

#### handleConnection function
* Step 1: 建立使用者資訊
* Step 2: 建立一個基於用戶並用於通訊的goroutine 
* Step 3: 發送歡迎message給新用戶
* Step 4: 保存用戶資訊至全域記錄器
* Step 5: 處理用戶輸入與輸出
* Step 6: 發送離開message給全部用戶
```go

func handleConnection(conn net.Conn) {
	//handling incoming connection
	defer conn.Close() //close the connection

	//1.create user information
	newUser := &User{
		ID:          GenerateID(), //using generator to generate a user id
		Addr:        conn.RemoteAddr().String(),
		EnterAt:     time.Now(),
		MessageChan: make(chan string, 8), //create a buffer channel
	}
	//2.create a new goroutine for communication
	go sendMessage(conn, newUser.MessageChan)

	//3.Sending welcome message to the new user
	newUser.MessageChan <- "Welcome to demo chat room"

	//4.Move new user to global recorder
	enterChannel <- newUser //send to broadcast func

	userActive := make(chan struct{}) //we not care about the message that send to the channel

	go func() {
		disConnectionDur := 1 * time.Minute
		//create a timer
		//NOTE : ignore the error
		time := time.NewTimer(disConnectionDur) //end up after 5 minute and sending to its(channel)
		for {
			select {
			case <-time.C:
				conn.Close()
			case <-userActive:
				time.Reset(disConnectionDur)
			}
		}
		//it needs to check whether user active

	}()

	//5.handling user input
	in := bufio.NewScanner(conn)
	for in.Scan() {
		messageChannel <- Message{
			Owner: newUser.ID,
			Msg:   strconv.Itoa(newUser.ID) + ":" + in.Text(),
		} //send and broadcast to all user

		//send active message to channel
		userActive <- struct{}{} //it will reset the timer
	}
	//6.user is leaving the chat room
	leavingChannel <- newUser
	messageChannel <- Message{
		Owner: newUser.ID,
		Msg:   "user:`" + strconv.Itoa(newUser.ID) + "`has left.",
	}
}
```
這個功能是用於剔除沒在使用或者已離開但沒斷線之用戶，以減少佔用資源
```go
  userActive := make(chan struct{}) //we not care about the message that send to the channel
  go func() {
    disConnectionDur := 1 * time.Minute
    //create a timer
    //NOTE : ignore the error
    time := time.NewTimer(disConnectionDur) //end up after 5 minute and sending to its(channel)
    for {
      select {
      case <-time.C:
        conn.Close()
      case <-userActive:
        time.Reset(disConnectionDur)
      }
    }
```
receiveMessage 用於接收message
```go
func receiveMessage(conn net.Conn, ch <-chan string) {
	//Be careful that this function will run on a new routine,if the channel is not being closed
	//this routine won't exist.
	for msg := range ch { //not break if channel is not closed
		fmt.Fprintln(conn, msg)
	}
}
```
#### 廣播 function
* 保持所有user 到全域記錄器
* 監察所以channel

```go
func broadcast() {
	//need to record user info
	//just a simple storage
	users := make(map[*User]struct{})

	for {
		select {
		case user := <-enterChannel:
			//getting user from enterChannel
			users[user] = struct{}{} //just a simple storage
		case user := <-leavingChannel:
			//need to remove from simple storage
			//and close the user channel,avoid goroutine  leaking problem
			delete(users, user)
			close(user.MessageChan) // closed and sendMessage exited
		case msg := <-messageChannel:
			//send to all user from the map
			for user := range users {
				if user.ID == msg.Owner {
					continue
				}
				user.MessageChan <- msg.Msg
			}
		}
	}

}
```
### 客戶端實作
#### main function
```go
func main() {
	//connect to tcp server
	conn, err := net.Dial("tcp", ":2022")
	if err != nil {
		panic(err)
	}

	done := make(chan struct{})

	//massages from connection
	go func() {
		io.Copy(os.Stdout, conn) // NOTE: ignoring errors
		done <- struct{}{} // signal the main goroutine
	}()

	//input from stdin to connection
	copyMessage(conn, os.Stdin)
	conn.Close()
	<-done //stuck here if there is not any messages received

}
```
```go
func copyMessage(dst io.Writer, src io.Reader) {
	if _, err := io.Copy(dst, src); err != nil {
		log.Fatalln(err)
	}
}
```
