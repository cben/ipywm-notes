https://stackoverflow.com/questions/59457073/connecting-to-a-running-jupyter-kernel-with-a-currently-running-process
...
RuntimeError: Kernel didn't respond to kernel_info_request



I had similar symptoms but I _am_ running `jupyter console --existing` from same dir as `jupyter server`.  Adding --debug:
```
jupyter console --debug --existing
```
revealed which kernel file it's picking up:
> `[ZMQTerminalIPythonApp] Connecting to existing kernel: /home/beni/.local/share/jupyter/runtime/kernel-euporie-1b7236dd.json`

which was a remnant of my previous attempts to mix [Euporie](https://euporie.readthedocs.io/) with Jupyter, it's not currently running.  Plus `ls -l --sort=time /home/beni/.local/share/jupyter/runtime/` showed I had a bunch of other files there (e.g. kernel-c3d01cca-40de-41be-bec2-1b25eb72f611.json from regular jupyter was also dated last month, also stale).

 1. A mis-conception I had was `console --existing` connects to existing _server_.  
    It's not just that, a server may run 0 or more _kernels_, to each of which you can connect 0 or more frontends (browser, console, qtconsole etc), and `--existing` insists on reusing an already running _kernel_ — so that it'll share variables.

   !!!!!!!    TODO: no, consoles are direct parents of kernels.  Does server only handle notebooks??
   /api/sessions?token=... doesn't even list console kernels, kernel*.json files do

 2. Once you have 1 or more running kernels, you can specify one explicitly instead of letting it guess:
    ```
    > jupyter console --debug --existing 1f70dd10-f855-4b27-b3a4-e37bae896237
    ...
    Connecting to existing kernel: /home/beni/.local/share/jupyter/runtime/kernel-1f70dd10-f855-4b27-b3a4-e37bae896237.json
    ...
    In [1]: x  # Existing variable I set previously
    Out[7]: 42
    ```

 3. After shutting down everything `jupy|ipy`-related, it's helpful to clear that `~/.local/share/jupyter/runtime/` dir, now `--existing` fails immediately and more clearly: `CRITICAL | Could not find existing kernel connection file kernel*.json` — instead of getting stuck for a minute to produce that "didn't respond to kernel_info_request" error.

 4. So my core misunderstanding was that **a bare `jupyter server` starts out with 0 running kernels**!
    [BTW you can `jupyter server --debug` as well but that's too verbose and no need here, it always prints when it starts/stops kernels]

    🛫 When does a server start a kernel?  A: On demand, when you open a notebook in browser, or start `jupyter console`, `ipython qtconsole` etc.
    * A `console` "owns" the kernel it started, on exit it's `Shutting down kernel` 🛑 
    * A `qtconsole` will ask you


```
jupyter server list --jsonlist | python -c '
import sys, json, pprint, urllib.request
for j in json.load(sys.stdin):
     print(j)
     pprint.pprint(json.load(urllib.request.urlopen(j["url"].rstrip("/") + "/api/sessions?token=" + j["token"])))'
```
Lists only "sessions" running under jupyter server/notebook/lab;
It does not show console-parented kernels.
