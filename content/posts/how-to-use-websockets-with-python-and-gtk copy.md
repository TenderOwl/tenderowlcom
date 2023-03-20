---
title: "How to use WebSockets with Python and GTK+"
date: 2020-12-18T11:26:21+03:00
draft: false
description: "Tutorial shares the experience on how to use  WebSockets with Python and GTK"
tags: python, gtk+, gtk-3, websockets
---


Hey there! I'm here to present you an amazing trip to the native apps with WebSockets!

<!--more-->
 In this current case we will be using these technologies:
- Python
- GTK+
- Libsoup
- Websocket messaging

If you're wondering how to build a native GTK+ chat app, let's dive with me!

## Theory

[WebSockets](https://en.wikipedia.org/wiki/WebSocket) is a protocol designed to provide a consistent connection between the client and a server where both can send data to each other. It's like a wire over the Internet or a tube, distinct of regular HTTP, where the client only can send data to the server and it gets a response immediately.

Benefits of this approach are quite obvious: the server can push the data to the client when it comes to server from another client or generated somewhere on the backend. And the client doesn't need to refresh the page or do something to fetch new data, it just comes to the client by itself.

More information about WebSockets you can find on the [websocket.org](http://websocket.org/aboutwebsocket.html).

What I want to show you in this article it is how to connect to a WebSocket server and send or receive data from it in a native GTK+ application.

I will code my app in elementary OS using [GNOME Builder](https://wiki.gnome.org/Apps/Builder), but the same logic should work anywhere where [GTK+](https://gtk.org) and [libsoup](https://libsoup.org) are available and all code could be easily adapted even for macOS.

## UI

The app logic is quite simple yet representative. The UI will consist of two screens (`Gtk.Grid`) placed in `Gtk.Stack`. The first screen contains widgets required to connect to the server such as `Gtk.Entry` for server address, button to initiate a connection and a spinner to show the state.

![First page](https://dev-to-uploads.s3.amazonaws.com/i/6peeanzovpi8tbpp38rn.png)

The second screen is more complex. Purpose of this is to enter the message, send it to the server and show the server's response in `Gtk.TextView`. Thus we need `Gtk.Grid` to place widgets. `Gtk.Entry` and `Gtk.Button` the same way we did it on the first page. And to display log we need `Gtk.TextView` placed within `Gtk.ScrolledWindow` in case there will be much more logs than the window can contain and the scrolling will be as much useful feature as it could :)

![Second page](https://dev-to-uploads.s3.amazonaws.com/i/b9we4xc3cwi202qr5c86.png)


### Connection page

Let's start building! Create a new project in Builder, as the language, we will use Python 3.6+.

![New project](https://dev-to-uploads.s3.amazonaws.com/i/6s87by1ljgku3jsp9h5p.png)

IDE will generate a base app structure with all we need, and all we need is just two files: `window.ui` and `window.py`.

Firstly, we need a correct UI for our app, so open `window.ui`. file and replace the "hello world" `Label` with `Gtk.Stack`. We will need two pages. 

![Stack properties](https://dev-to-uploads.s3.amazonaws.com/i/h289zyc16lxbjwx4o840.png)

Name the first screen "connection", and place onto it `Gtk.Grid` with 3 rows and columns. In the central cell place `Gtk.Box` with 4 rows:
1. `Gtk.Label` with text "Connection"
2. `Gtk.Entry`. Set its id to "host_entry"
3. `Gtk.Button` with id "connect_btn"
4. `Gtk.Spinner`. Call it whatever you like, mine is just a "spinner" :)

That's enough for the "Connection" page.

### Chat page

Moving further. The second screen is a bit more complex. Again, we need `Gtk.Grid` to put all the widgets we need. But for now, it will be a grid with just 2 rows and 2 columns.

First row with two widgets in its own cells:
- `Gtk.Entry` with id "message_entry"
- `Gtk.Button` with id "send_btn"

On the second row place `Gtk.ScrolledWindow`, no id required because we don't need to call its method. Inside this ScrolledWindow put a new `Gtk.TextView` widget. Set its id to "log_view".

To make it looks more convenient, set _width_ = 2 in the container's props inside attribute editor for ScrolledWindow.

![ScrolledWindow packing](https://dev-to-uploads.s3.amazonaws.com/i/oiu7wgjgguqg4g0dn4ue.png)

## Logic

Ok, out UI is ready enough to build some logic for it, finally :)

Open `window.py` and add class vars to connect with widgets from the `.ui` file.

```python3 {linenos=table}
# Stack with pages
screens = Gtk.Template.Child()

# Connection page widgets
host_entry = Gtk.Template.Child()
connect_btn = Gtk.Template.Child()
disconnect_btn = Gtk.Template.Child()
spinner = Gtk.Template.Child()

# Chat page widgets
send_btn = Gtk.Template.Child()
message_entry = Gtk.Template.Child()
log_view = Gtk.Template.Child()

# Declare some class-vars
self.session = None
self.connection = None
```

Well, now we are able to call methods of these widgets. But also, we have to handle its signals. Let's connect "connect_btn":

```python {linenos=table}
def __init__(self, **kwargs):
    # Some app logic

    self.connect_btn.connect('clicked', self.on_connect_clicked)

def on_connect_clicked(self, widget):
    # Check if there is a text inside the host_entry
    # and grab focus if its empty
    if not self.host_entry.get_text():
        self.host_entry.grab_focus()
        return

    # Show user we are trying to connect
    self.spinner.start()

    # Init libsoup Session, create message and start connection
    self.session = Soup.Session()
    msg = Soup.Message.new("GET", self.host_entry.get_text())
    self.session.websocket_connect_async(msg, None, None, None, self.on_connection)
```

The code above is quite straightforward:
- Get the server address
- Create new [`Session`](https://lazka.github.io/pgi-docs/#Soup-2.4/classes/Session.html)
- Init the connection with [`websocket_connect_async`](https://lazka.github.io/pgi-docs/#Soup-2.4/classes/Session.html#Soup.Session.websocket_connect_async)

As a callback for connection function, we put `on_connection` method, which takes the result of this operation and finish the process. 

```python {linenos=table}
def on_connection(self, session, result):
    try:
        # Finish connection process
        self.connection = session.websocket_connect_finish(result)
        
        # If we here, the connection went correctly
        # Connect `message` handler to the connection
        self.connection.connect('message', self.on_message)
        
        # Show to user all is OK and switch to the Chat page
        self.screens.set_visible_child_name('chat')
        self.disconnect_btn.set_visible(True)
    
    except Exception as e:
        # In case something goes wrong just print an exception
        print(e)
        self.session = None
    
    finally:
        # Don't forget to show a user that process is finished.
        self.spinner.stop()
```

We are almost done! All we need to do more is to handle click event from the "send_btn" and display messages in the "log_view".

```python {linenos=table}
def on_message(self, connection, msg_type, message):
    msg = f'<b>RECEIVED:</b> {message.get_data().decode()}\n'
    self.buffer.insert_markup(self.buffer.get_start_iter(),
                              msg,
                              len(msg))

```

Could it be more simple than that? :wink: Just to display a message in a more readable way we add some Pango markup and use [`insert_markup`](https://lazka.github.io/pgi-docs/#Gtk-3.0/classes/TextBuffer.html#Gtk.TextBuffer.insert_markup) method of `Gtk.TextBuffer` to... insert markup!

Last, but not least required thing is a handler for "send_btn" signal.

```python {linenos=table}
def on_send_clicked(self, widget):
    # Get the message from the Entry
    msg = self.message_entry.get_text()

    # If there is no message - grab focus.
    if not msg:
        self.message_entry.grab_focus()
        return

    # Send a text to the server.
    self.connection.send_text(msg)
```

For our simple case, all we need is just the [`send_text`](https://lazka.github.io/pgi-docs/#Soup-2.4/classes/WebsocketConnection.html#Soup.WebsocketConnection.send_text) method. But libsoup could do a lot of different stuff, such as [`send_binary`](https://lazka.github.io/pgi-docs/#Soup-2.4/classes/WebsocketConnection.html#Soup.WebsocketConnection.send_binary) or emit a signal if something [goes wrong](https://lazka.github.io/pgi-docs/#Soup-2.4/classes/WebsocketConnection.html#Soup.WebsocketConnection.signals.error).

![Spacebeam](https://dev-to-uploads.s3.amazonaws.com/i/6th5fr8cow67bmb8nh3z.gif)

## Conclusion

Simple yet descriptive example of how to start building your GTK+ app with WebSockets inside. Maybe you will create a new chat app? 

Stay tuned if you want to read more! Or go to the docs from the Links section. 

Anyway, good luck!


## Links
Source code:
 https://github.com/amka/pygobject-gtk-websocket-app

Docs:
- https://websocket.org/
- https://lazka.github.io/pgi-docs/#Gtk-3.0
- https://lazka.github.io/pgi-docs/#Soup-2.4
