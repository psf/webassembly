# EuroPython 2024 Web Assembly Summit

Held in Prague. Event website here: https://ep2024.europython.eu/session/webassembly-summit 

We had around 15 sign up, and several more turn up on the day.

As with last year, the morning consisted of presentations, with a discussion
scheduled for the afternoon.

## Presentations

The talks presented in the morning were:

* Patrick Armino: presenting Strawberry rocks (cancelled).
* Chris Laffra: PySheets and/or profiling with PyScript.
* Hood Chatham: State of the Pyodide Union.
* Josh Lowe: Migrating Edublocks to PyScript.
* Antonio Cuni: SPy summary.
* Nicholas Tollervey: State of PyScript Union.


These were presented in a "seminar fashion" where people were encouraged to
respectfully interrupt, question, debate and otherwise constructively engage
during the presentation of the subject matter.

## Afternoon discussion

The following topics were discussed (taken from notes provided by Chris Laffra):

* WASM specific:
    * WASI -> Arjun Ramesh and Ben Titzer's WALI (Web Assembly Linux Interface): niche, light-weight and secure "docker" for WASM. [Proposal paper](https://www.semanticscholar.org/paper/Stop-Hiding-The-Sharp-Knives%3A-The-WebAssembly-Linux-Ramesh-Huang/2544ea6d1cb37723b3988b7874981048357a614d)
* PyScript related:
    * UI toolkits:
        * [Invent](https://inventframework.org/), `pyscript.web` API ([docs](https://docs.pyscript.net/2024.8.2/user-guide/dom/#pyscriptweb)), [puepi](https://puepy.dev/))
        * [Pygame Zero](https://pygame-zero.readthedocs.io/en/stable/index.html) - can we run it in PyScript? Performance is the challenge.
            * **TODO**: we should create an FFI-aware kernel to run games (like PGZero).
            * Check out [Streamlit](https://streamlit.io/).
        * PyScript [LTK](https://github.com/pyscript/ltk) - client side rendering of UIs on top of jQuery and PyScript.
        * Integration with JS frameworks:
            * [HTMXXX](https://htmxxx.fly.dev/)
            * Jinja templates to JS?
* Pyodide focused:
    * Reducing the footprint, compression - do like Pyrun (remove stuff, compress).
    * Reducing startup time:
        * We need to improve packages to download all dependencies at once, rather than one wheel at a time. [Previous online context/discussion](https://github.com/pyodide/pyodide/issues/2045#issuecomment-1423248534).
        * We should parallelize startup and package downloads.
        * Cache entire file-system as a snapshot with all packages pre-installed (an idea from Nicholas).
        * Related: memory snapshots.
        * WASM-fs will improve things, rather than the current JS version.
    * Nightly packages are now a thing.
