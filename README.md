// Contents for server.js
const serverJsContent = `const express = require('express');
const http = require('http');
const socketIo = require('socket.io');

const app = express();
const server = http.createServer(app);
const io = socketIo(server);

app.get('/', (req, res) => {
  res.sendFile(__dirname + '/index.html');
});

io.on('connection', (socket) => {
  socket.on('join chat', (username) => {
    console.log(\`\${username} has joined the chat\`);
    socket.broadcast.emit('chat message', \`\${username} has joined the chat\`);
  });

  socket.on('disconnect', () => {
    console.log('User disconnected');
    io.emit('chat message', 'A user has left the chat');
  });

  socket.on('chat message', (msg) => {
    io.emit('chat message', msg);
  });
});

const PORT = process.env.PORT || 3000;
server.listen(PORT, () => console.log(\`Server running on port \${PORT}\`));`;

// Contents for index.html
const indexHtmlContent = `<!DOCTYPE html>
<html>
<head>
  <title>Chat App</title>
  <style>
    /* Basic CSS for our chat app */
    ul { list-style-type: none; margin: 0; padding: 0; }
    li { padding: 8px; margin-bottom: 2px; background-color: #f3f3f3; }
  </style>
</head>
<body>
  <ul id="messages"></ul>
  <form id="form" action="">
    <input id="input" autocomplete="off" /><button>Send</button>
  </form>

  <script src="/socket.io/socket.io.js"></script>
  <script>
    var socket = io();

    var form = document.getElementById('form');
    var input = document.getElementById('input');
    
    form.addEventListener('submit', function(e) {
      e.preventDefault();
      if (input.value) {
        socket.emit('chat message', input.value);
        input.value = '';
      }
    });
    
    socket.on('chat message', function(msg) {
      var item = document.createElement('li');
      item.textContent = msg;
      document.getElementById('messages').appendChild(item);
      window.scrollTo(0, document.body.scrollHeight);
    });
  </script>
</body>
</html>`;

// Contents for package.json
const packageJsonContent = `{
  "name": "chat-app",
  "version": "1.0.0",
  "description": "A simple chat application",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "express": "^4.17.1",
    "socket.io": "^4.0.0"
  }
}`;

// Proceed to zip these files
return {
  files: [
    { path: "server.js", content: serverJsContent },
    { path: "index.html", content: indexHtmlContent },
    { path: "package.json", content: packageJsonContent }
  ]
};
