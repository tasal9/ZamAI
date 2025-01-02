# Introduction

Neovim project ideas for [Google Summer of Code](https://developers.google.com/open-source/gsoc/) are tracked by the `gsoc` label: https://github.com/neovim/neovim/labels/gsoc

These projects may require familiarity with C, Makefiles, Lua or VimL ("Vim script").

[Neovim](https://neovim.io/) is a text editor based on Vim. One of the [project goals](https://neovim.io/charter/) is to encourage hacking and collaboration. We've done a lot of work to reduce friction for new contributors. For example, building the project from source is a single command:

    make

Documentation for Neovim developers is here: https://neovim.io/doc/user/develop.html 

The Neovim source has roots going back to 1987 which means libraries such as [libuv](https://github.com/libuv/libuv) were not around at that time. The codebase can be made easier to maintain and understand by using these libraries. Project ideas that involve working heavily with internals will in general be more difficult than project ideas that "simply" add new features. However working with older/complex parts of the code base can also provide valuable learning feedback for writing simpler and more maintainable code. There is a large range of skills that can be learned and working with the team to find a project that will help you the most is in your benefit.

# Getting started

Visit [https://neovim.io/#chat](https://neovim.io/#chat) to discuss GSoC projects with the community and our mentors. Because communication is a big part of open source development you are expected to get in touch with us before making your application.

We recommend that you start with a small coding task (choose from [complexity:low](https://github.com/neovim/neovim/issues?q=is%3Aopen+is%3Aissue+label%3Acomplexity%3Alow) issues). The goal here is to get accustomed with the codebase and to our workflow for reviewing and testing PRs, rather than directly making a large code contribution.

The following list of projects are just some ideas. We are happy to hear any suggestions that you may have.

## Making a proposal

The application period for GSOC is March 16-March 31 ([Timeline](https://developers.google.com/open-source/gsoc/timeline)). Send your proposal through the official [GSOC page](https://summerofcode.withgoogle.com/organizations/6095582066638848/). We encourage students to send a first draft early in this period, this allows us to give feedback and and ask for more information if need. See https://google.github.io/gsocguides/student/writing-a-proposal for some guidelines for writing a good proposal.

Note: this year we will likely accept 1-2 students. We expect to get more strong proposals than available slots, so we will need to turn some good proposals down.

Proposals are tracked on the issue tracker. If an issue doesn't already exist for your idea, create a new issue and mention that it's for GSoC. Then we will apply the [gsoc](https://github.com/neovim/neovim/labels/gsoc) label.

## Proposal evaluation

Proposal evaluation criteria:

http://intermine.org/internships/guidance/grading-criteria-2019/

## Tips

- Anywhere a Vim concept (such as "textlock") is mentioned, you can find what it means by using the `:help` command from within neovim (`:help textlock`).
- Ask questions and post your partial work frequently (say, once every day or 2, if possible). It's OK if work is messy; just put "[WIP]" in the pull request (PR) title.
- Take advantage of the continuous integration (CI) systems which automatically run against your pull
requests. When you send work to a PR,  the full test-suite runs on the PR while you continue to work locally.
- `:help dev-tools` contains up-to-date documentation on building, debugging, and development tips.
- Only a text editor, CMake, and a compiler are needed to develop Neovim. LSP or [ctags](https://github.com/universal-ctags/ctags) is very helpful also.
- The [contributing guidelines](https://github.com/neovim/neovim/blob/master/CONTRIBUTING.md) are intended to be
helpful, not rigid.

# GSoC Ideas 20XX

* Look at open issues with the ["gsoc" tag](https://github.com/neovim/neovim/labels/gsoc).
* Or propose an idea by creating a new issue.

# GSoC Ideas 2020

* [New Features](#new-features)
  * [Improve Lua configurability](#support-for-Lua-plugins-and-configuration)
  * [GUI Features](#gui-features)
  * [Live preview of commands](#live-preview-of-commands)
  * [Improve autoread](#improve-autoread)
  * [IDE "Vim mode"](#ide-vim-mode)

___
## Support for Lua plugins and configuration

**Desirable Skills:**

- C and related tools
- Familiarity with Lua C API is a plus.

**Description:**

Nvim is always built with a builtin Lua interpreter. A goal is to make Lua a first class language for plugins and user config, which can access editor functionality directly, without "shelling out" to VimL commands and functions.

**Expected Result:**

More functionality is directly exposed to Lua. This project idea is a bit open ended, and improvements can be made in different directions. Regardless what priorities are decided, it is expected that the project will take form as multiple smaller deliverables that will be merged separately during the duration of the project.

The project can involve calls the core editor makes to VimL, such as autocommands and mappings, where native Lua support could be added. Lua code can already use luv bindings to libuv for async io, but better integration with specific io features like rpc channels and terminal buffers could be worthwhile. Adding API functions for vim features only available via ex commands would be useful (like reading and setting :highlight definitions).  Nvim is  working on building a Lua standard library to make it convenient to write plugins and user config in Lua. This goal is broad, and discussion with the plugin developing community is encouraged, about what functionality would be most useful for the standard library.

Another direction is to add new extension points, such as letting Lua code control aspects of screen drawing. A  starting point could be to let Lua code draw a richer completion popup menu for the TUI, by processing popup menu UI events internally.


**Difficulty:** Medium

**Code license:** Apache 2.0

**Mentor:** Björn Linse ([@bfredl](http://github.com/bfredl)), Ashkan Kiani ([@norcalli](http://github.com/norcalli))

___
## GUI Features

**Desirable Skills:**

- C and related tools
- Familiar with event-loop programming model

**Description:**

Nvim GUIs are implemented as processes communicating with Nvim. Originally the UI protocol exposed most functionality as a single, terminal-like screen grid. Work has been done to allow UIs (including embeddings in GUI editors, like VSCode) to render the screen layout themselves, based on semantic updates from Nvim. Some screen elements like the cmdline, popupmenu and message area has been externalized. As a result of a 2018 [GSOC project](https://github.com/neovim/neovim/issues/8320), windows can be drawn on separate grids.

**Expected Result:**

Further improvements to the GUI protocol.

Some UIs want to render the buffer contents themselves. A solution would be a UI protocol mode, where the rendered grid is not used, rather all decorations, such as syntax highlighting, conceals, virtual text are transmitted for the current viewport. Such an UI would be able to render text without the restrictions of the builtin monospace grid. Then the UI should be able to inform nvim about usage of virtual columns and wrapping, so that vim commands such as `gj` are consistent with how text is presented to the user.

Another path is to improve the core Nvim grid model. We could allow the width and height of cells be different for each row. This would allow for heading text with different font size, and pictures rendered inside windows.

Putting forward your own ideas of UI improvements is encouraged. Read the [docs](https://github.com/neovim/neovim/blob/master/runtime/doc/ui.txt) for the implemented extensions as well as the [tracking issue](https://github.com/neovim/neovim/issues/9421) for ongoing/planned work, as a starting point.


**Difficulty:** Medium-Hard

**Code license:** Apache 2.0

**Mentor:** Björn Linse ([@bfredl](http://github.com/bfredl))

___
## Live preview of commands

**Desirable Skills:**

- C and related tools
- Familiar with event-loop programming model

**Description:**

Nvim has builtin live preview of `:%s` substitution, as a result of a [[successful collaboration with students|https://medium.com/@eric.burel/stop-using-open-source-5cb19baca44d]]. This support could be extended to more commands, such as `:global` and `:normal`.

**Expected Result:**

More commands support interactive preview. This could be done by extending the hard-coded support for substitution preview to more commands. Alternatively, a more scalable approach could be to add core functionality that makes it easier to implement live preview as plugins in VimL and/or Lua. See [[#7370|https://github.com/neovim/neovim/issues/7370]] for some ideas.


**Difficulty:** Medium-Hard.

**Code license:** Apache 2.0

**Mentor:** Björn Linse ([@bfredl](http://github.com/bfredl))

---

## Improve autoread

**Desirable Skills:** Any

**Description:** Reload file/notify user when a file being edited changes outside of `nvim`.

**Expected Result:**

Neovim has the 'autoread' setting that regularly checks if a file edited in neovim has been externally modified. It thus notifies the user to prevent overwriting the changes. Sadly the current mechanism isn't foolproof. This project intends to make this feature work as well as in other editors like Sublime text and across the Neovim-supported platforms. The interface should also be improved so that notifications show how different the edited and modified files are.
The candidate can realize some of the difficulties involved with this [proposition for linux](https://github.com/neovim/neovim/issues/1380)


**Difficulty:** Medium

**Code license:** Apache 2.0

**Mentor:** Matthieu Coudron ([@teto](http://github.com/teto))

___
## IDE "Vim mode"

**Desirable Skills:** Any

**Description:** Implement "Vim mode" in an editor/IDE (such as IntelliJ) by embedding a `nvim` instance.

**Expected Result:**

Full Nvim editing should be available in the editor/IDE, while also allowing the user to use the native editor/IDE features.

Examples:

- [VSCode integration](https://github.com/asvetliakov/vscode-neovim)
- [Sublime Text integration](https://github.com/lunixbochs/actualvim)

**Difficulty:** Medium

**Code license:** Apache 2.0

**Mentor:** TBD

---

# GSoC Ideas 2019

---

## "Multiprocessing" feature (done)

**Desirable Skills:**

* Experience with concurrency and interprocess communication
* Strong experience with C
* Familiar with VimL (Vim script) and Vim concepts (quickfix list, buffers, etc.)

**Description:**

p2p architecture for data sharing between multiple Nvim instances. Similar to Python's [multiprocessing](https://docs.python.org/3/library/multiprocessing.html) module, the idea is to to offload Nvim tasks (VimL and/or Lua) to child Nvim processes.

Here's a picture of the potential workflow:

1. parent calls `invoke_async(Foo)`
2. parent spawns new child `nvim` process
3. parent sends command name `Foo` + state to child
4. parent does other, unrelated work
5. child completes its `Foo` work
6. child sends notification (method name + state) to parent

**Difficulty:** Hard

**Code license:** Apache 2.0

**Mentor:** Justin M Keyes ([@justinmk](http://github.com/justinmk))

___

## TUI (Terminal UI) remote attachment (done)

**Desirable Skills:**

C

**Description:**

The built-in UI is called the TUI. It looks like Vim, but internally it is decoupled from the UI and "screen" layout subsystem. It was designed to be able to connect to other (remote) instances of Nvim, but this hasn't been implemented yet. [#7438](https://github.com/neovim/neovim/issues/7438)

Nvim is both a server and a client. Nvim (client) can connect to any other Nvim (server). And Nvim [GUIs](https://github.com/neovim/neovim/wiki/Related-projects#gui) can show the screen of a remote Nvim server.

But the built-in Nvim TUI cannot show the screen of a remote Nvim server. That's the goal of this project.

- It is not "live share". It's just showing the remote UI in a client TUI.
- Networking question are out of scope. It is assumed the client has an SSH tunnel or named pipe to connect to.

The Nvim API model is:

- `:help api`: general API functions
- `:help ui`: UI events
- Any client can call any API function.
- If a client calls `nvim_ui_attach`, then it is a "UI client". This simply means that Nvim will send UI events as msgpack-rpc notifications on the channel.

That's how _every_ Nvim UI works. And that's how the TUI client (this project proposal) will work.

Python "demo UI" may be helpful: https://github.com/neovim/python-gui

The simplest UI is the "fake UI" implemented in `test/functional/ui/screen.lua` from the Nvim test suite. It creates a text UI from real Nvim UI events. This allows us to write Lua tests that check the state of the UI, by simply writing the text UI in the test. In the Neoivm repo, "git grep 'screen:expect'" shows all of the places where this is used.

The `example_spec.lua` test shows a simple example. You can try it by running this shell command:

    TEST_FILE=test/functional/example_spec.lua make functionaltest

Overview of the `screen.lua` "fake UI" implementation:

- `Screen._wait()` / `Screen:sleep()` runs the event loop to consume UI events
- `Screen:_redraw()` dispatches UI events to the appropriate handlers
- For example `Screen:_handle_grid_line()` consumes a line event, and updates some tables (self._grids and self._attr_table).
    - And those tables are literally the contents of the fake UI that `Screen:expect()` tests against.

Use cases:

- Connect to any Nvim. Unlike tmux, Nvim UI client can connect to any other running Nvim, including GUIs.
- Potential for using `libnvim` as the RPC core of any Nvim API client, to eliminate the need for clients to implement their own msgpack-RPC handling.

**Expected Result:**

Implement a TUI "remote UI" client. Modify the TUI subsystem so that it can display a remote Nvim instance.
The C codebase already has msgpack support, an event-loop, and the ability to connect to sockets/named pipes/etc.

- Extend `tui/tui.c` to:
  1. connect to a channel
  2. get UI events from the channel
  3. unpack the events and call the appropriate handlers
- Extend `tui/input.c` to:
  1. send user input to the channel (i.e. call the nvim_input() API function)

Example:

    nvim --servername foo

will connect to the Nvim server at address `foo`. The `nvim` client instance sends input to the remote Nvim server, and reflects the UI of the remote Nvim server. So the `nvim` client acts like any other external (G)UI.


**Difficulty:** Medium

**Code license:** Apache 2.0

**Mentor:** Justin M Keyes ([@justinmk](http://github.com/justinmk)), Björn Linse ([@bfredl](http://github.com/bfredl))

---
# GSoC Ideas 2018

## UI protocol improvements ([DONE](https://github.com/neovim/neovim/pull/8707))

**Desirable Skills:**

- C and related tools
- Familiar with event-loop programming model

**Description:**

Nvim GUI:s are implemented as processes communicating with Nvim over a protocol. Currently this protocol exposes most functionality as a terminal-like screen grid. A long term goal is enabling richer UIs (including embeddings in GUI editors, like VSCode) by refactoring the protocol towards semantic updates and letting the GUI actually draw buffer contents and other screen elements. Currently this has been implemented for a few specific elements, like the completion popup menu and the command line.

**Expected Result:**

The UI protocol has gained new capabilities. This could involve substantial changes such as the GUI receiving redraw updates for each window separately, so that the GUI could be responsible for managing the overall layout of windows and statuslines.

Alternatively, improvements could be done within the current global screen grid, such as the ability to display grid-aligned images in signs, buffers and statuslines. It could also involve adding semantic information to the grid, so that GUI:s can identify screen elements reliably rather than guessing it from highlights.

- Code license: Apache 2.0

**Difficulty:**

Medium to Hard

**Student:** ([@UtkarshMe](https://github.com/UtkarshMe))

**Mentor:** Björn Linse ([@bfredl](http://github.com/bfredl))

___
## Java client

**Desirable Skills:**

- Familiar with Vim/Nvim and Vim script (VimL)
- Moderate/High experience in Java
- Familiar with event-loop programming model

**Description:**

Implement a Nvim [API client](https://github.com/neovim/neovim/wiki/Related-projects#api-clients) using Java. 

Implement a client, written in Java, which allows Java applications to control Nvim using the Nvim RPC API.  If you are familiar with AWS or any other SaaS, note that a Nvim API client is just like a SDK for a REST web service, except that Nvim uses msgpack, not HTTP/JSON.

The Nvmi RPC API is documented at [:help api](https://neovim.io/doc/user/api.html) and [:help rpc](https://neovim.io/doc/user/msgpack_rpc.html).

To correctly implement the client one needs to understand the [msgpack-rpc](https://github.com/msgpack-rpc/msgpack-rpc/blob/master/spec.md) protocol. Some sort of event-loop mechanism will be needed to handle notifications.

For reference, you can find clients in other languages at the [related projects](https://github.com/neovim/neovim/wiki/Related-projects#api-clients) wiki page.

The ultimate goal is to have a library that can be used to create plugins for [IntelliJ](https://www.jetbrains.com/help/idea/plugin-development-guidelines.html) and [Eclipse](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.platform.doc.isv%2Fguide%2Ffirstplugin.htm).  **Minimizing third-party dependencies** may help there.

**Expected Result:**

- Java library that can be used to build Neovim extensions (UIs and other applications).
  - Method signatures generated from `nvim --api-info`.
  - Since `nvim` is already required (for `--api-info`), the Java code-generation script could be written in Lua (which is built-in to `nvim`).
- Passes the test suite used by the Nvim [python-client](https://github.com/neovim/python-client).
  - Using the [python-client tests](https://github.com/neovim/python-client/tree/master/test) as a guide, create equivalent tests using a Java testing framework.
  - Test suite should be runnable from the command-line (should not require an IDE) via maven/gradle (or some other industry-standard build-tool).
- Builds (and passes tests) on Linux (Travis CI) and Windows (AppVeyor)
- End-user deliverable should be compatible Java 6 (this is negotiable)
- Source-code can be latest version of Java (no backwards-compatibility requirement)
- Code license: Apache 2.0

**Difficulty:**

Medium

**Mentor:** Justin M Keyes ([@justinmk](http://github.com/justinmk))

---
## C# client ([DONE](https://github.com/neovim/nvim.net))

**Desirable Skills:**

- Familiar with Vim/Nvim and Vim script (VimL)
- Moderate/High experience in C#
- Familiar with event-loop programming model

**Description:**

Implement a Nvim [API client](https://github.com/neovim/neovim/wiki/Related-projects#api-clients) using C#. 

Implement a client, written in C#, which allows C# applications to control Nvim using the Nvim RPC API.  If you are familiar with AWS or any other SaaS, note that a Nvim API client is just like a SDK for a REST web service, except that Nvim uses msgpack, not HTTP/JSON.

The Nvmi RPC API is documented at [:help api](https://neovim.io/doc/user/api.html) and [:help rpc](https://neovim.io/doc/user/msgpack_rpc.html).

To correctly implement the client one needs to understand the [msgpack-rpc](https://github.com/msgpack-rpc/msgpack-rpc/blob/master/spec.md) protocol. Some sort of event-loop mechanism will be needed to handle notifications ([hint1](https://blogs.msdn.microsoft.com/pfxteam/2012/01/20/await-synchronizationcontext-and-console-apps/), [hint2](https://blogs.msdn.microsoft.com/pfxteam/2012/02/02/await-synchronizationcontext-and-console-apps-part-3/)).

For reference, you can find clients in other languages at the [related projects](https://github.com/neovim/neovim/wiki/Related-projects#api-clients) wiki page.

The ultimate goal is to have a library that can be used to create plugins for Visual Studio.  **Minimizing third-party dependencies** may help there.

**Expected Result:**

- C# library that can be used to create C#-based Neovim extensions (UIs and other applications).
  - Method signatures generated from `nvim --api-info`.
  - Since `nvim` is already required (for `--api-info`), the C# code-generation build script could be written in Lua (which is built-in to `nvim`).
- Passes the test suite used by the Nvim [python-client](https://github.com/neovim/python-client).
  - Using the [python-client tests](https://github.com/neovim/python-client/tree/master/test) as a guide, create equivalent tests using a C# testing framework.
  - Test suite should be runnable from the command-line (should not require an IDE) via MSBuild or some other industry-standard build-tool.
- Builds (and passes tests) on Linux (Travis CI) and Windows (AppVeyor)
- Builds against **.NET Standard 2.0**
- Deliverable should be easy to install as a NuGet (or other) package.
- Source-code can be latest version of C# (no backwards-compatibility requirement)
- Code license: Apache 2.0

**Difficulty:**

Medium

**Student:** ([@b-r-o-c-k](https://github.com/b-r-o-c-k))

**Mentor:** Justin M Keyes ([@justinmk](http://github.com/justinmk))

---

# Contributing to this list:

* [Ideas page Manual](http://write.flossmanuals.net/gsoc-mentoring/making-your-ideas-page/)
* [Example](https://github.com/nim-lang/Nim/wiki/GSoC-2016-Ideas)

Please add your project ideas in the following format.

___
## Title

**Desirable Skills:**

**Description:**

**Expected Result:**

- Item1
- Item2

**Difficulty:** Easy/Medium/Hard

**Code license:** Apache 2.0

**Mentor:** Mentor name ([@MentorName](http://github.com/MentorName))
