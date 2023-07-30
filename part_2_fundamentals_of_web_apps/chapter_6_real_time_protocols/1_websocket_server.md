## 6.1 Websocket Server

WebSocket servers provide a powerful tool for building real-time, interactive web applications. While they operate alongside traditional web servers, they serve a distinctly different role and enable new kinds of user experiences.

They do so while maintaining the fundamental principles of server-side web development, like the request-response cycle of HTTP and the short-lived nature of individual application instances.


1. **The Need of WebSocket Servers and Their Role**:


   WebSockets provide a protocol for real-time, bidirectional communication between a client (typically a web browser) and a server. This is in contrast to the HTTP protocol, which is unidirectional - the client sends a request and the server sends back a response. WebSockets, on the other hand, keep the connection open, allowing data to be sent back and forth as needed. This is crucial for applications that require real-time updates, such as chat apps, live sports updates, and collaborative editing tools.

```mermaid
sequenceDiagram
  participant C as Client
  participant WS as WebSocket Server
  participant L as Laravel Application
  C->>WS: Establish WebSocket connection
  C->>L: Send HTTP request
  L->>L: Process request
  L-->>C: Send HTTP response
  L->>WS: Dispatch event
  WS-->>C: Broadcast message
```

2. **WebSocket Server Process**:

    
   A WebSocket server can either be a standalone process running on the same operating system as the web server (like the Laravel Websockets package) or it could be a separate service running elsewhere (like Pusher). The WebSocket server's role is to manage these long-lived WebSocket connections, handling incoming messages and broadcasting messages to clients.

```mermaid
graph TB
  subgraph OS1["OS"]
    WS1["WebSocket Server"]
    HS1["HTTP Web Server"]
  end
```

```mermaid
graph TB
  subgraph OS1["OS"]
    HS1["HTTP Web Server"]
  end
  subgraph OS2["OS"]
    WS1["WebSocket Server"]
  end
```

3. **Laravel Applications and WebSocket Connections**:

   
   While a WebSocket connection between a client and the WebSocket server is long-lived, a Laravel application still adheres to its typical lifecycle: it receives an HTTP request, sends back an HTTP response, and then terminates. The presence of a WebSocket server doesn't change this fundamental aspect of the Laravel application.

```mermaid
sequenceDiagram
  participant C as Client
  participant WS as WebSocket Server
  participant L as Laravel Application
  C->>WS: Establish WebSocket connection
  C->>L: Send HTTP request
  L->>L: Process request
  L-->>C: Send HTTP response
  Note over L: Laravel application terminates
```

4. **Laravel Application Communication with WebSocket Server**:

   
   In a typical scenario, the Laravel application doesn't communicate directly with the WebSocket server, but rather communicates through events. When something happens in a Laravel application that should be broadcast to clients (for example, a new message is added in a chat app), the application dispatches an event. This event is heard by the WebSocket server, which then broadcasts the relevant data to all connected clients. All of this is done while maintaining the short-lived nature of the Laravel application instance.

```mermaid
sequenceDiagram
  participant C as Client
  participant WS as WebSocket Server
  participant L as Laravel Application
  participant E as Event
  L->>E: Dispatch event
  Note over L: Laravel application instance terminates
  E-->>WS: Event heard by WebSocket Server
  WS-->>C: Broadcast relevant data to clients
```

5. **WebSocket Servers and Clients**:


   In most scenarios, it's the WebSocket server that is actively pushing messages to the clients. The clients typically interact with the server via standard HTTP requests to access and modify resources. For example, in a chat application, a new message is sent to the server via an HTTP request. The server then broadcasts this message to other connected clients using the WebSocket connection.

```mermaid
sequenceDiagram
  participant C as Client
  participant WS as WebSocket Server
  participant HS as HTTP Web Server
  C->>HS: Send HTTP request
  HS-->>C: Send HTTP response
  HS->>WS: Broadcast message to clients
  WS-->>C: Receive WebSocket message
```

6. **Clients Sending Messages to WebSocket Server**:


   There are scenarios where the client might also send messages to the WebSocket server. A notable example is Google Docs. 

   As users type and make changes to a document, those changes are sent to the WebSocket server over the WebSocket connection. The WebSocket server then broadcasts these changes to all other clients connected to the same document. Each client application would have the logic to handle these updates and apply them to the document in real-time. This allows all users to see changes as they happen.

   There's still server-side application logic involved, but it's primarily for handling more permanent changes like saving the final version of the document, handling authentication, managing users, and similar tasks. These operations would typically be performed via traditional HTTP requests to the server, and are separate from the real-time, collaborative aspect of the application facilitated by the WebSocket connection. 

```mermaid
sequenceDiagram
  participant C1 as Client 1
  participant C2 as Client 2
  participant WS as WebSocket Server
  participant HS as HTTP Web Server
  C1->>WS: Send changes over WebSocket
  WS-->>C2: Broadcast changes
  C2->>C2: Apply changes in real-time
  C1->>HS: Send HTTP request for permanent changes
  HS-->>C1: Respond with HTTP response
```
