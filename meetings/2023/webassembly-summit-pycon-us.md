# WebAssembly Summit at PyCon US 2023 - Salt Lake City

Attendees and organization: https://discuss.python.org/t/creating-an-attendee-list-for-the-webassembly-summit-at-pycon-us-2023-on-thursday-april-20th/23509

## Brett: Update on Emscripten

[snarky.ca blog post](https://snarky.ca/webassembly-and-its-platform-targets/)

### Emscripten
- History: predated WASM
- Browser
- compile -> WebAssembly
- JS
- CPython
- Multiple backends
- Trying to move toward WASI or POSIX API

### WASI
- server stuff (edge compute)
- embedded
- "POSIX" for WebAssembly
- Get security
- Runtime provides a sandbox

### Bytecode Alliance
- industry consortium
- WASI main focus

*Note: some discussions will apply only WASI or Emscripten*

Implications on packages

Most attendees interested in Frontend side. A few for WASI server stuff. A handful for both.

### Q: How do we talk about WebAssembly to the rest of the community?
- Glossary of things
- Verbs and Nouns (Trey were confusing)
- Leverage the web communities (Jannis)
- Which platform to choose? If you are interested in ...

## WebAssembly - CPython (Brett)

Tier 3 now: no SLA; core devs can't rip out
Steering Council approval; working buildbot; coredev support
- 2 platforms
- Microsoft donated buildbot
[PEP 11](https://peps.python.org/pep-0011/) explains tier support
Tier 2: SLA guarantee about things keeping working
- Main branch within 24 hours must fix broken
- Steering Council approval
- Buildbot stable; runs every commit or trigger by core dev
- 2 Core Devs
Tier 1: Main will never be broken

Currently at Tier 3.

### WASI CPython (Brett)
- Trying to get to Tier 2: Goal end of year
- Written dev container for WASI (talk to Brett at sprint)
- Buildbot
- Kushal / Eric core dev support
- Toolchain: ensure the core dev can easily do a build
- Documentation ([CPython/tools/wasm/README.md](https://github.com/python/cpython/tree/main/Tools/wasm))
- 
#### WASI Standard
- Preview 1 standard
- Preview 2 standard under review
- Working toward to 1.0
    - [go WASI support](https://github.com/golang/go/issues/58141) explicit on `preview1`/`preview2` support, not a concern for Python (up to the wasm runtime, not Python to support)
- Next to add async
- WASI SDK always ship with stable clang

### Emscripten
- Currently Tier 3
- Brett not expert here.
- Need someone to own a buildbot
- Toolchain is more complex
- Hood contact for Emscripten builds
- PyScript team/Anaconda can run the buildbot
- [Buildbot Documentation Links](https://www.python.org/dev/buildbot/)
- Later then there can be a PEP to determine how to do packaging
- Quanstack ghosted Emscripten discussion (PyScript maybe to reach out at Europython)

## After lunch session

- Question: Can/should the binding between Python and Javascript (in pyodide) be maintained outside Pyodide? Moved into Python itself?
    - Hood: We've wanted to extract it into its own module.  It is pretty stable and mostly feature complete.  It could go in the standard library if there is interest.
- Dynamic loading and WASI
    - Bytecode Alliance: Historically focused on C/C++/Rust.  Last few months they have started paying more attention to dynamic languages.
    - Open question is how to deal with extension modules in a way that works for all languages (not just Python)
    - Can we have WASI support for dlopen() and dlsym()?
    - Discussion is very new, so no conclusions yet
- What is the ABI situation for WASI?
    - Is there risk that Python interpreter built one way won't work with extension modules built a different way
    - WASI will specify features using "worlds", and we will specify the worlds that Python needs (and extensions will need that minimum level of build)
    - WASI does not yet specify an exact signature for a C call, which is needed for ABI
- PyPI now has a topic for marking compatibility with WebAssembly (all), or specifically WASI or Emscripten
- Question: What is VSCode WASI support for things like NumPy
    - Modules have to all be statically linked into the python module
    - NumPy has been done, Pandas is partially done

### State of Emscripten Packaging
- Wheels are built using a separate tool (similar to crossenv, but different)
- Need to trick packages to believe they are building for the target platform
- Tagging wheel with emscripten-wasm32
- Where should the wheels go?
    - Can't put them on PyPI now
- Currently serving them from jsdelivr
- Need to decide what the ABI is for "standard" wheels.  Need a manylinux-like spec for this.
- Working on getting cibuildwheel to build emscripten wheels
- Currently wheels are tagged with the emscripten version
- Emscripten will break ABI from time to time (switching time counter to 64-bit example)
- ABI breaks also happen when Emscripten unwinds hacks as new WASM features are enabled
- (discussion of how dynamic loading works in emscripten.  could not transcribe all of this)
- challenge to define "emscripten wheels" without making them "pyodide wheels" because there are various flag options that are really pyodide specific and not generic to emscripten
- Option: if we say that Python 3.X will require Emscripten Y, then do we have a problem if we need to update Emscripten?
    - Plan to standardize wheels after a large upcoming patch to Emscripten, after which point we should be able to link the Emscripten version to the Python minor version
- We know how to write auditwheel for emscripten wheels
- Concern that "emscripten" is not a platform like "macos".  There is example in the room of of two different emscripten builds that technically would not be compatible.
- Antonio: Most important thing is to make it possible for people to have webassembly builds in their CI.  We can update the specs later once that story settles.
    - cibuildwheel should have emscripten support merged in a few weeks [PR](https://github.com/pypa/cibuildwheel/pull/1456)
- Proposal:
    - Treat pyodide version as the platform tag, rather than emscripten (emscripten version is implied by pyodide version)
    - Will require writing a PEP to define this
    - Question: How does pip determine what are compatible tags?
        - No need for platform specific logic.  Only want exact match of tag
- Pyodide is too many things?
    - Foreign function interface - getting stable, could be decoupled.  Tied to python version, not emscripten version.
    - Distribution (set of prebuilt packages): Could be separated out to work more like conda-forge
- ACTION: Hood will write the PEP once some moving parts settle down (in a few months)
    - Going to get some buy in from other core packages using cibuildwheel

- Question: What's going on with emscripten-forge?
    - Big focus on JupyterLite use cases right now
    - Some conda-forge maintainers concerned about the load on the volunteers to bring all the Emscripten stuff into conda-forge project
    - Continuing for now async from conda-forge
    - no interest in WASI (also, WASI can't handle dynamic loading right now anyway, so moot for now)
- Topic for later: Packaging in the browser is fundamentally different.  Should we think about this differently?
- Could we standardize a static build (app packaging) step
- Who is going to host these artifacts (no one wants to sign up to run a CDN)
- Brett: Don't need to rush all these solutions.  We can take time to get this right.

### Pyodide demo

(Hood demoed pyodide and cibuildwheel support)

### Future discussions/communication

https://discuss.python.org/c/webassembly/28


## Links

Example GH actions for building pyodide wheels
  - https://github.com/sourmash-bio/sourmash/blob/c952507f151a09cc1ed701f3a509bc50f7184c8b/.github/workflows/build_wheel.yml#L58-L84
  - https://github.com/sourmash-bio/sourmash/blob/555563dd6abe1bd52a4cdd17c596fe79f4675d72/.github/workflows/build_wheel.yml#L91
