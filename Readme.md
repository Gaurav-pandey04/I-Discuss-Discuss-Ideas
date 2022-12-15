# I-Discuss: Discuss Idea

## Objective:
To build a real time chat web app. The technology required to build app is vanilla JS, Node.js and socket.io. 

https://i-discuss-chat-app.netlify.app/

## Overview:
This project contains three folders namely 


1.	Assets
    •	Ping.mp3
    •	Icon.png
2.	Frontend
    •	Index.html
    •	Style.css
    •	Client.js 
3.	nodeServer
    •	node_modules
    •	index.js
    •	package.json
    •	package-lock.json


Assets is responsible to hold image and sound of notification files. Frontend is responsible for main user experience and UI. nodeServer is responsible to connect all the user through socket.io in real-time and manage them accordingly.

## Explanation:
### nodeServer:
### index.js:
PORT is the default port for the server and process.env.PORT is the default port for environment variables and if it is not present then run on localhost:8000 .
```
const PORT = process.env.PORT || 8000
```

This initializes the server at 8000 port with socket.io module and set cors mode so that network can be established properly without any restriction.

```
const io = require("socket.io")(PORT,{
    cors:{
        mode:'cors'
    }
});
```

Contains all users as an object.
```
const users={};
```

This block describes when connection get established check for the three events, they are new-user-joined, send, disconnect. 
Further it describes if new user joins then add that user to users object with its socket.id and assign it a name entered by user after that broadcast user-joined event with name as a argument.
If send event occurs then broadcast receive event with message and name as an argument.
If disconnect event occurs then broadcast left event with users object as an argument and after that delete that user from users object.
```
io.on('connection', socket => {
    socket.on('new-user-joined', name => {
        users[socket.id] = name;
        socket.broadcast.emit('user-joined', name)
    });

    socket.on('send', message => {
        socket.broadcast.emit('receive', {message: message, name: users[socket.id]})
    });

    socket.on('disconnect', message =>{
        socket.broadcast.emit('left', users[socket.id]);
        delete users[socket.id];
    });

});
```

That’s it was all setup for server and now let’s move on to frontend part.

### Frontend: 
### Index.html:
Included nodeServer running on port link to index.html so that index.html can access the nodeServer and can manage the users accordingly.
```
<script defer src="http://localhost:8000/socket.io/socket.io.js" ></script>
```
Included JavaScript fille client.js which has access to index.html and it act as bridge to nodeServer and index.html. i.e., it informs nodeServer what event is happening in index.html and give, take response accordingly.
```
<script defer src="./client.js"></script>
```
Included CSS file to index.html to enhance UI.
```
<link rel="stylesheet" href="style.css" />
```

From here body starts, nav element contains logo and main-heading of the App. Image is at ‘../Assets/icon.png ‘
```
<nav>
      <img src="../Assets/icon.png" alt="Logo" id="logo" />
      <h1 id="main-heading">I-Discuss: Discuss Ideas</h1>
    </nav>
```

Section part contains the mat chat-box where chats should be appeared. The ‘chat-box’ id is given to that section.
```
 <section id="chat-box">
      
 </section>
```

This section contains input-box where message should be typed with send button. And here index.html overs.
```
<section class="send">
      <form action="#" id="send-container">
        <input type="text" name="messageInp" id="messageInp" placeholder="Type Your Message Here">
        <button class="btn" type="submit">send</button>
      </form>
    </section>
```
    
### Style.css: 
This file contains all the CSS properties of index.html.

### Client.js: 
This links the server to the UI.
Create a constant socket which fetch the data from ‘http://localhost:8000’ 
Note: At ‘http:localhost:8000’ nodeServer is running.
```
const socket = io('http://localhost:8000');
```

Gets the following elements from the DOM.
```
const form = document.getElementById('send-container');
const messageInput = document.getElementById('messageInp')
const messageContainer = document.getElementById('chat-box')
```

Audio to play audio when new user join, new message arrives and user lefts.
```
var audio = new Audio('../Assets/ting.mp3');
```

Append function is used to append message to the chat-box in dom. It takes two argument message and position.


MessageElement creates the new div in which message is to be appended. innerText method is used to insert the message in DOM. After that add message and position as a class to the div made. After that it append to messageContainer which contains chat-box id.


After that it checks the position of element if it is left then play audio and currentTime = 0 is used to play audio when audio is still playing.
```
const append = (message, position)=>{
    const messageElement = document.createElement('div');
    messageElement.innerText = message;
    messageElement.classList.add('message');
    messageElement.classList.add(position);
    messageContainer.append(messageElement);
    if(position =='left'){
        audio.currentTime = 0; 
        audio.play();
    }
}
```

Prompt the user to type name to join the chat and fires the new-user-joined event with name of user.
```
const name = prompt("Enter your name to join");
socket.emit('new-user-joined', name);
```

This fires user-joined event to show to other user, new user has joined.
```
socket.on('user-joined', name =>{
    append(`${name} joined the chat`, 'left')
})
```

These fires receive event which show message when message is received by other user.
```
socket.on('receive', data =>{
    append(`${data.name}: ${data.message}`, 'left')
})
```

These fires when user left and show to all user that any user has left.
```
socket.on('left', name =>{
    append(`${name} left the chat`, 'left')
})
```

If from i.e., the message is send then these send message to the server. And fires the send event.
```
form.addEventListener('submit', (e) => {
    e.preventDefault();
    const message = messageInput.value;
    append(`You: ${message}`, 'right');
    socket.emit('send', message);
    messageInput.value = ''
})
```

That’s it chats app is ready to use.
## Conclusion:
Socket.io is great way to make real-time chat application which send message to the user if server has update and user can also send message to the server if user has update. Which make it super powerful.


