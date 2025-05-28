same terminal:
https://ipython.readthedocs.io/en/latest/interactive/reference.html#embedding
```
>>> import IPython
>>> IPython.embed()
```
real ZMQ-listening kernel:

https://ipykernel.readthedocs.io/en/stable/api/ipykernel.html#ipykernel.embed.embed_kernel
```
>>> import ipykernel.embed
>>> ipykernel.embed.embed_kernel()
```
[qt]console --existing

----

[embed ZMQ kernel in background without side effects](https://github.com/ipython/ipython/issues/8097)

[Calling IPython.embed_kernel() outside the main thread fails silently](https://github.com/ipython/ipython/issues/4032)

https://github.com/albertz/background-zmq-ipython

[Infinite loop when using threadpool in notebook](https://github.com/ipython/ipykernel/issues/1269)

[Make outputs go to correct cell when generated in threads/asyncio](https://github.com/ipython/ipykernel/pull/1186)
landed in 2024 => [Need public opt-out API for output routing from threads #1289](https://github.com/ipython/ipykernel/issues/1289) 😆

----

- https://ipython.org/ipython-doc/3/config/eventloops.html

- Does embed in non-main thread work?
