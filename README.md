Dumping here things I've learnt hacking on [newm](https://github.com/newm-next/newm-next) wayland compositor.

# Jupyter

I'm starting newm inside a Jupyter notebook, so I can connect to it and interact with Python state live: [NEWM.ipynb]()  A bit crazy ;-), but I learnt a lot about Jupyter internals, moving parts, and how to put them together in weird ways, which is maybe useful to others...

WHY did I go for jupyter?
   - [x] Supports disconnect/reconnect
   - [x] Records outputs while disconnected, in a standard format.
   - Fancy rich outputs.

[HANDOFF.ipynb]() is good intro to Jupyter architecture in terms of what happens during long-running cell execution and disconnect/reconnect.

See [KERNELS.ipynb] for how to enumerate servers / "sessions" / kernels, how they relate, kernel lifecycle + code to draw mermaid diagrams of all these.

Ideal workflow involves starting notebook *before* I have a graphical environment, and connecting from browser — which itself is running under newm!  And, I want messages from startup to be captured in the notebook.

1. start kernel without browser
   * Put app top-level in (N)otebook ? vs. [(E)mbed kernel](EMBED.md)

     😐 In retrospect, I have to admit the whole notebook adventure was a _large_ distraction, and I should have started from embed + console + log file...

2. need server "session" linked to the kernel
   * Can a session "adopt" (E) kernel? AFAICT no, Running (N)otebook under server is simpler.
   * `jupyter server` (part of `jupyter lab` etc) vs. jupyverse

3. want early execution outputs to be recorded in notebook (file and/or Yjs)
   * `jupyter execute --inplace` writes to a file but only when finished & exits.
   * Start notebook under `jpterm --server=...`!  (`--experimental` ?)
   * Long-running cell output is hardest...  Yjs `--collaborative` + WIP server-side execution!  (OR just keep jpterm running.)
   * Use [widget](https://ipywidgets.readthedocs.io/en/latest/examples/Output%20Widget.html#interacting-with-output-widgets-from-background-threads) to stream log instead?  Keeps updating after tab close & reopen!

4. open notebook in browser
   * JupyterLab frontend vs. "Classic" notebook frontend.  lab leads developement. (any server differences?)
   * auth `?token=`
   * open notebook with existing kernel.
     * What happens when you change kernel on existing notebook?

TODO write 'BACKGROUND.ipynb' about bg execution & threads & output.

# TODO architecture of pywm <-> wlr C layer

# TODO newm specifics
