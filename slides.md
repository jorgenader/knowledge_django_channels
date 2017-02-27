# Django Channels

Jörgen Ader

- Twitter: [@JorgenAder](https://twitter.com/JorgenAder)
- GitHub: [github.com/metsavaht](https://github.com/metsavaht)
- Project: [django/channels](https://github.com/django/channels)
- Example Project: [metsavaht/no-design-slack-clone](https://github.com/metsavaht/no-design-slack-clone)
- Slides: [github.com/metsavaht/knowledge_django_channels](https://github.com/metsavaht/knowledge_django_channels)

Note:

    Hello! This is a small intro to Django channels.

***

#### Standard Django

![The scene](./img/django-wsgi.png)

<small>* original from blog.heroku.com</small>

***

## Channels

- ASGI(Asynchronous Server Gateway Interface)
  - Support multiple common protocol styles
  - Channels and Messages
- Daphne
- Channel layer
- Consumers

Note:

    Django's standard views revolves around requests and responses.
    Channels adds event driven

---

### What does Channels provide?

- Easy HTTP long polling
- Full user session and auth for WebSockets
- Built-in primitives for mass triggering of events
- Optional low-level HTTP control on a per-URL basis
- Extendability to other protocols or event sources

***

#### How?

![The scene](./img/django-asgi.png)

<small>* original from blog.heroku.com</small>

Note:
    
    It separates Django into two process types:

    - One that handles HTTP and WebSockets
    - One that runs views, websocket handlers and background tasks (consumers)
    
    Communication via ASGI protocol
    
    - Daphne
      - asgiref and different backends
        - memory
        - redis
        - POSIX IPC

***

#### Setting up Channels

```
pip install django channels asgi_redi
```

---

#### settings.py

```
CHANNEL_LAYERS = {
   "default": {
       "BACKEND": "asgi_redis.RedisChannelLayer",
       "CONFIG": {
           "hosts": 'redis://localhost:6379',
       },
       "ROUTING": "myapp.routing.channel_routing",
   },
}
```

---

#### routing.py

```
channel_routing = [
   # Wire up websocket channels to our consumers:
   route("websocket.connect", ws_connect, path=r"^/echo/"),
   route("websocket.receive", ws_receive),
   route("websocket.disconnect", ws_disconnect),
]
```

---

#### consumers.py

```
def ws_connect(message):
   message.reply_channel.send({'accept': True})
   Group('friends').add(message.reply_channel)

def ws_disconnect(message):
   message.reply_channel.send({'accept': True})
   Group('friends').add(message.reply_channel)
   
def ws_receive(message):
    Group('friends').add({'text': message[text]})
```

---

#### Running

```
web: daphne example.asgi:channel_layer
worker: python manage.py runworker
```

***

## Thank you

- Testing Project: [metsavaht/no-design-slack-clone](https://github.com/metsavaht/no-design-slack-clone)
- Example Project: [andrewgodwin/channels-examples](https://github.com/andrewgodwin/channels-examples)
- Slides: [metsavaht/knowledge_django_channels](https://github.com/metsavaht/knowledge_django_channels)
- ASGI [Spec](http://channels.readthedocs.io/en/latest/asgi.html)
