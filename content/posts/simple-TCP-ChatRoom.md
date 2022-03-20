+++ 
draft = false
date = 2022-03-19T13:08:48+08:00
title = "simple-TCP-Chatroom"
description = "simple tcp chat room implementation using golang"
slug = ""
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

# simpleChatRoom
Playing/Learning goroutine by making a simple char room with TCP

### TCP Server implementation

User Structure is used to record user info that connected to the TCP Server
```go
type User struct {
	ID          int
	Addr        string
	EnterAt     time.Time
	MessageChan chan string //individual user channel
}
```
Message Structure is used to ignore the user who sends the message to others
```go
type Message struct {
	Owner int
	Msg   string
}
```

Three gorouitne(concurrency)  
`enterChannel = make(chan *User)` is used to send the message to all user when a user comes in.  
`leavingChannel = make(chan *User)` is same as `enterChannel`,but for users leaving the chat room.   
`messageChannel = make(chan Message, 8)` is used to communicate among users.  

#### main function
* Creating a TCP Server base on localhost:2020
* Creating a routine for broadcast
* Using an infinite loop listens to any connection and accepting
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
* Step 1: Create user info
* Step 2: Create a new goroutine for communication
* Step 3: Sending welcome message to the new user
* Step 4: Moving user info to global recorder
* Step 5: Handling user input(I/O Scanner)
* Step 6: Sending leaving message to all users
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
this functionality is used to kick the a user out of the chat room if the user is no longer active
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
receiveMessage is used to recevie messages 
```go
func receiveMessage(conn net.Conn, ch <-chan string) {
	//Be careful that this function will run on a new routine,if the channel is not being closed
	//this routine won't exist.
	for msg := range ch { //not break if channel is not closed
		fmt.Fprintln(conn, msg)
	}
}
```
#### broadcast function
* Storing all chat room user to the map(temporary)
* Listing to all available channel

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
### Client implementation
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
