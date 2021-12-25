# SocketRocket

![Platforms][platforms-svg]
[![License][license-svg]][license-link]

[![Podspec][podspec-svg]][podspec-link]
[![Carthage Compatible][carthage-svg]](carthage-link)

[![Build Status][build-status-svg]][build-status-link]


## Features/Design

- TLS (wss) support, including self-signed certificates.
- Seems to perform quite well.
- Supports HTTP Proxies.
- Supports IPv4/IPv6.
- Supports SSL certificate pinning.
- Sends `ping` and can process `pong` events.
- Asynchronous and non-blocking. Most of the work is done on a background thread.
- Supports iOS, macOS, tvOS.

## Installing

There are a few options. Choose one, or just figure it out:


 Run `carthage update`, and you should now have the latest version of `SocketRocket` in your `Carthage` folder.

- **Using SocketRocket as a sub-project**

  You can also include `SocketRocket` as a subproject inside of your application if you'd prefer, although we do not recommend this, as it will increase your indexing time significantly. To do so, just drag and drop the `SocketRocket.xcodeproj` file into your workspace.

## API

### `SRWebSocket`

The Web Socket.

#### Note:

`SRWebSocket` will retain itself between `-(void)open` and when it closes, errors, or fails.
This is similar to how `NSURLConnection` behaves (unlike `NSURLConnection`, `SRWebSocket` won't retain the delegate).

#### Interface

```objective-c
@interface SRWebSocket : NSObject

// Make it with this
- (instancetype)initWithURLRequest:(NSURLRequest *)request;

// Set this before opening
@property (nonatomic, weak) id <SRWebSocketDelegate> delegate;

// Open with this
- (void)open;

// Close it with this
- (void)close;

// Send a Data
- (void)sendData:(nullable NSData *)data error:(NSError **)error;

// Send a UTF8 String
- (void)sendString:(NSString *)string error:(NSError **)error;

@end
```

### `SRWebSocketDelegate`

You implement this

```objective-c
@protocol SRWebSocketDelegate <NSObject>

@optional

- (void)webSocketDidOpen:(SRWebSocket *)webSocket;

- (void)webSocket:(SRWebSocket *)webSocket didReceiveMessageWithString:(NSString *)string;
- (void)webSocket:(SRWebSocket *)webSocket didReceiveMessageWithData:(NSData *)data;

- (void)webSocket:(SRWebSocket *)webSocket didFailWithError:(NSError *)error;
- (void)webSocket:(SRWebSocket *)webSocket didCloseWithCode:(NSInteger)code reason:(nullable NSString *)reason wasClean:(BOOL)wasClean;

@end
```

## Testing

Included are setup scripts for the python testing environment.
It comes packaged with vitualenv so all the dependencies are installed in userland.

To run the short test from the command line, run:
```bash
  make test
```

To run all the tests, run:
```bash
  make test_all
```

The short tests don't include the performance tests
(the test harness is actually the bottleneck, not SocketRocket).

The first time this is run, it may take a while to install the dependencies. It will be smooth sailing after that. 

You can also run tests inside Xcode, which runs the same thing, but makes it easier to debug.

- Choose the `SocketRocketTests` target
- Make sure your running destination is either your Mac or any Simulator
- Run the test action (`⌘+U`)

### TestChat Demo Application

SocketRocket includes a demo app, TestChat.
It will "chat" with a listening websocket on port 9900.

#### TestChat Server

The sever takes a message and broadcasts it to all other connected clients.

It requires some dependencies though to run. 
We also want to reuse the virtualenv we made when we ran the tests. 
If you haven't run the tests yet, go into the SocketRocket root directory and type:

```bash
make test

```

In the same terminal session, start the chatroom server:

```bash
python TestChatServer/py/chatroom.py
```

There's also a Go implementation (with the latest weekly) where you can:

```bash
cd TestChatServer/go
go run chatroom.go
```

#### Chatting

Now, start TestChat.app (just run the target in the Xcode project).
If you had it started already you can hit the refresh button to reconnect.
It should say "Connected!" on top.

To talk with the app, open up your browser to [http://localhost:9000](http://localhost:9000) and start chatting.


