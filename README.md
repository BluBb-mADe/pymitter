# pymitter

Python port of the extended Node.js EventEmitter 2 approach of
https://github.com/asyncly/EventEmitter2 providing namespaces, wildcards and TTL (and priorities in this fork).

#### Features

- Namespaces with wildcards
- Times to listen (TTL)
- Optional priorities
- Usage via decorators or callbacks
- Lightweight implementation, good performance


#### Installation

~~*pymitter* is a registered [PyPI module](https://pypi.python.org/pypi/pymitter), so the installation
with *pip* is quite easy~~

This fork is not on PyPI!

## Examples

#### Basic usage

```python
from __future__ import print_function
from pymitter import EventEmitter

ee = EventEmitter()

# decorator usage
@ee.on("myevent")
def handler1(arg):
   print("handler1 called with", arg)

# callback usage
def handler2(arg):
    print("handler2 called with", arg)
ee.on("myotherevent", handler2)

# emit
ee.emit("myevent", "foo")
# -> "handler1 called with foo"

ee.emit("myotherevent", "bar")
# -> "handler2 called with bar"
```


#### TTL

```python
from __future__ import print_function
from pymitter import EventEmitter

ee = EventEmitter()

@ee.once("myevent")
def handler1():
    print("handler1 called")

@ee.on("myevent", ttl=10)
def handler2():
    print("handler2 called")
    

ee.emit("myevent")
# -> "handler1 called"
# -> "handler2 called"

ee.emit("myevent")
# -> "handler2 called"

```


#### Priorities

```python
from __future__ import print_function
from pymitter import EventEmitter, Priority

ee = EventEmitter()

@ee.on("myevent")
def handler1():
    print("handler1 called")

@ee.on("myevent", prio=Priority.low)
def handler2():
    print("handler2 called")

# The priority class is just used as an int enum (2=high)
@ee.on("myevent", prio=2)
def handler3():
    print("handler3 called")


ee.emit("myevent")
# -> "handler3 called"
# -> "handler1 called"
# -> "handler2 called"

```


#### Wildcards

```python
from __future__ import print_function
from pymitter import EventEmitter

ee = EventEmitter(wildcards=True)

@ee.on("myevent.foo")
def handler1():
    print("handler1 called")

@ee.on("myevent.bar")
def handler2():
    print("handler2 called")

@ee.on("myevent.*")
def hander3():
    print("handler3 called")


ee.emit("myevent.foo")
# -> "handler1 called"
# -> "handler3 called"

ee.emit("myevent.bar")
# -> "handler2 called"
# -> "handler3 called"

ee.emit("myevent.*")
# -> "handler1 called"
# -> "handler2 called"
# -> "handler3 called"
```


## API

##### ``EventEmitter(wildcard=False, delimiter=".", new_listener=False, max_listeners=-1)``
EventEmitter constructor. **Note**: always use *kwargs* for configuration. When *wildcard* is
*True*, wildcards are used as shown in [this example](#wildcards). *delimiter* is used to seperate
namespaces within events. If *new_listener* is *True*, the *"new_listener"* event is emitted every
time a new listener is registered. Functions listening to this event are passed
``(func, event=None)``. *max_listeners* defines the maximum number of listeners per event. Negative
values mean infinity. *prio* overrides the order in which the callbacks will be called.
*prio* can be a "Priority" enum or a number. (lower is sooner.)

- ##### ``on(event, func=None, ttl=-1, prio=Priority.normal)``
	Registers a function to an event. When *func* is *None*, decorator usage is assumed. *ttl*
	defines the times to listen. Negative values mean infinity. Returns the function.

- ##### ``once(event, func=None, prio=Priority.normal)``
	Registers a function to an event with ``ttl = 1``. When *func* is *None*, decorator usage is
	assumed. Returns the function.

- ##### ``on_any(func=None, prio=Priority.normal)``
	Registers a function that is called every time an event is emitted. When *func* is *None*,
	decorator usage is assumed. Returns the function.

- ##### ``off(event, func=None)``
	Removes a function that is registered to an event. When *func* is *None*, decorator usage is
	assumed. Returns the function.

- ##### ``off_any(func=None)``
	Removes a function that was registered via ``on_any()``. When *func* is *None*, decorator usage
	is assumed. Returns the function.

- ##### ``off_all()``
	Removes all functions of all events.

- ##### ``listeners(event)``
	Returns all functions that are registered to an event. Wildcards are not applied.

- ##### ``listeners_any()``
	Returns all functions that were registered using ``on_any()``.

- ##### ``listeners_all()``
	Returns all registered functions.

- ##### ``emit(event, *args, **kwargs)``
	Emits an event. All functions of events that match *event* are invoked with *args* and *kwargs*
	in the exact order of their registeration. Wildcards might be applied.

<br>

##### ``Priority.*``
This is used as an enum to remain compatible.
```python
class Priority(object):
    realtime = 0
    veryhigh = 1
    high     = 2
    normal   = 3
    low      = 4
    verylow  = 5
    idle     = 6
```


## Development

- Original source hosted at [GitHub](https://github.com/riga/pymitter)
- ~~Python module hostet at [PyPI](https://pypi.python.org/pypi/pymitter)~~ (This is a fork)


## License

The MIT License (MIT)

Copyright (c) 2014 Marcel Rieger

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.


## Authors
Original:
Marcel R. ([riga](https://github.com/riga))
