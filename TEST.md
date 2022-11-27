

# WidgetApp Challenge - Tim Robards

### Table of contents:
1. [WidgetApp User Guide](#widgetapp-user-guide)
    - [What’s new in WidgetApp](#what’s-new-in-widgetapp)
	    - [Introducing multiple-user support](#introducing-multiple-user-support)
	    - [An all-new Streaming API](#an-all-new-streaming-api)
    - [Approach used](#approach-used)
2. [WidgetApp Administrator Guide](#widgetapp-administrator-guide)
    - [What’s new in the WidgetApp Administrator Guide](#what’s-new-in-the-widgetapp-administrator-guide)
	    - [New Streaming API](#new-streaming-api)
	    - [Streaming API Server Installation](#streaming-api-server-installation)
	    - [Streaming API Server Configuration](#streaming-api-server-configuration)
	    - [Streaming API Server Management](#streaming-api-server-management)
    - [Approach used](#approach-used-2)
3. [WidgetApp API Guide](#widgetapp-api-guide)
    - [What’s new for WidgetApp Developers](#what’s-new-for-widgetapp-developers)
	    - [Introducing the Streaming API](#introducing-the-streaming-api)
	    - [Authentication](#authentication)
	    - [Connecting to the Streaming API](#connecting-to-the-streaming-api)
	    - [Working with the Streaming API](#working-with-the-streaming-api)
    - [Approach used](#approach-used-3)

# WidgetApp User Guide

### What’s new in WidgetApp

*Version [number]*

An overview of the latest features and enhancements in this release.

#### Introducing multiple-user support

WidgetApp now allows multiple users to work collaboratively on design projects. Work with other users to collaborate on projects in real-time. See [How to share a design project](http://www.example.com) and [Introducing Collaboration mode](http://www.example.com) (video) to learn more.

#### An all-new Streaming API

The WidgetApp Streaming API is now available for developers. To learn more, see the [WidgetApp API guide](http://www.example.com).

----------

### Approach used:

For this section, I decided to emphasize the new project collaboration feature, as this is the primary feature affecting both the product UI and UX for users. Additionally, I found it relevant to mention the Streaming API. Despite being technical, I felt it was helpful to reference in the user guide.

Most of the notes leaned toward behind-the-scenes changes, and I’d recommend a few additions, such as linking to a basic tutorial showing how collaboration works in the UI. For the user guide, a series of screenshots would help users visualize the changes. Ideally, a video or GIF image demonstrating users working collaboratively would significantly enhance the communication of these changes.

----------

# WidgetApp Administrator Guide

### What’s new in the WidgetApp Administrator Guide

*Version [number]*  

#### New Streaming API

Streaming API is a feature that allows authorized clients to collaborate on projects. Simultaneous client connections are run over Websockets, allowing events to process in real time.

#### Streaming API Server Installation

The Streaming API runs on WebSockets-enabled TCP servers. The WebSocket protocol allows for the fluid data exchange between the browser and server via a persistent connection. See [Streaming API server build](http://www.example.com) for the complete installation process.

#### Streaming API Server Configuration

WebSockets servers are configured to listen for incoming socket connections (referred to as ‘handshakes’) using a standard TCP socket, for example:

```widgetapp.com:8080```

Connection requests are received via a `get` request sent to the server. The request will resemble a standard `http` request, for example:

 ```
GET HTTP/1.1
Host: widgetapp.com:8080
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: [ key ]
Sec-WebSocket-Version: [ protocol version ]
```

**Note**: If any header returns an incorrect value, the server should reply with a `400` (or “Bad Request”) response and terminate the socket connection.

If the request is successful, the server should send back a response indicating that the protocol will be changing from `http` to WebSocket (`wss`), for example:

``` 
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: [ accepted response string]
```
Once the response has been sent, the WebSocket connection is active, and data may be swapped between the client and server.

#### Streaming API Server Management

In this release, note that the WidgetApp frontend (WFE) handles all existing API requests as per previous versions, but hands over to the new Streaming frontend (SFE) for authorized connections that use the new Streaming API. SFE then manages the session as per WFE, but with Streaming API capabilities.

A key consideration for server management is that both SFE and Streaming API processes will contribute to the overall server load. To account for this, the `_sfe` and `_stream` processes must be monitored for CPU resource usage.

#### To manage server load:

A service restart is required if service utilization for the `_sfe` or `_stream` process exceeds 50%.

 To restart `_sfe` instances, run the following command: 

`widgetapp_admin -process _sfe -n <num_instances> -start`

**Note**: SFE restarts will terminate `_stream` processes automatically for any existing connections.

 A single SFE process is the default on startup, with an additional process started in parallel for every 10 Streaming API clients.

----------

### Approach used:

My approach for this section was to determine which notes were relevant for admin and technical support operations. I categorized the guide additions into subsections (Server Installation, Configuration & Management) to communicate the changes sequentially and improve clarity for support teams.

I recommend adding further information about the server build process, as WebSockets installations will likely be standardized, and some insight at this point would add clarity. I elaborated upon the available server configuration notes, providing more background into the changes, as I worked on the basis that this was a new configuration for the admin team.

----------

# WidgetApp API Guide

### What’s new for WidgetApp Developers

*Version [number]*

#### Introducing the Streaming API

Streaming API is a new feature that allows users to collaborate on design projects. To learn more, see [Introducing collaboration mode](http://www.example.com).

The Streaming API is organized around WebSockets. With WebSockets, we are able to create a two-way communication line which is used to architect the collaboration mode feature for real-time operation.

Our API is built upon a bi-directional protocol (WSS) with no predefined message patterns such as "request" or "response". The client or the server can send a message to the other party over a single TCP connection.

#### Authentication

The Streaming API uses the OAuth2.0 protocol for authentication. In this release, the authentication process remains unchanged from previous versions, and are managed through the existing WidgetApp frontend (WFE). In accordance with OAuth protocol, Streaming API defines the flow roles as follows:

-   **Resource Owner**: The user who authorizes access to their account. Typically, this is the end-user.
    
-   **Client**: The application requesting access to the user’s account. It must first be authorized by the user, and the API must validate the authorization.
    
-   **Resource Server**: The Streaming API resource server hosts the protected user accounts.
    
-   **Authorization Server**: The authorization server verifies the user’s identity before issuing access tokens to the client. **Note**: The Streaming API is authorized only for approved user groups.
    

#### Connecting to the Streaming API

To connect to the Streaming API, you can create a new WebSocket using the WSS protocol in the URL, for example:

`let socket = new WebSocket("wss://www.widgetapp.com/_stream");`

**Note**: Ensure you use `wss:` to enforce an encrypted connection.

#### Working with the Streaming API

When connected to the API, event listeners should be created for the following events:  
 
-   **Open**: detects an active connection
    
-   **Message**: detects data received
    
-   **Error**: detects a WebSocket error
    
-   **Close**: detects a closed connection
    
When using SFE, ensure that your configuration listens to **port 200** to receive stream messages from the `widgetApp_useraction_` APIs. Such as `widgetApp_useraction_refresh()`,  `widgetApp_useraction_edit()`, etc.  
  
**Note**: Most user actions (search, scrolling, etc.) will work by calling **ajax** endpoints.

----------

### Approach used:

For this section, I included any information related to the external use of the API. I introduced WebSockets to provide insight for developers who may be more familiar with methods such as REST. To improve readability, I then categorized the new changes under subsections (Authentication, Connecting to the API & Working with the API).

I recommend adding further information about how to connect to the endpoint and work with the API, in the form of tutorials to link to for common scenarios. I would also recommend including information about how user groups are determined and approved for the use of the API.
