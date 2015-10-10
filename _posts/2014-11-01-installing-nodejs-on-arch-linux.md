---
layout: post
title: "Installing Node.js on Arch Linux"
date: "2014-11-01"
permalink: "installing-node-js-on-arch-linux"
---

### Preface

**NOTE: These instructions are only relevent to Arch and Node at the time of this writing (2014-11-01). If they seem like they won't work for you, they most likely will not.**

Installing Node on Arch is ***very*** frustrating when installing it through Pacman or Yaourt. By default, it installs it in `/usr/bin`. That's not cool when installing global node modules, such as Grunt or PM2. If you do, you'll be greeted with the following error code:

```bash
npm ERR! Error: EACCES
```

It's a horrible sight to see, especially when [you don't have to use `sudo` when using the global flag](https://www.joyent.com/blog/installing-node-and-npm#step_4_install_node_and_npm) installing node modules.

So, as a forewarning, do *not* install the `nodejs` AUR package. It's bad, mmkay? Now, on to the step-by-step instructions.

---

#### Step 1. RTFM

Read the steps on [this](https://github.com/joyent/node/wiki/installation#building-on-linux) wiki article. Take note of the build requirements.

- GCC
- Python 2.6 or 2.7 (We're using 2.7 for these instructions).
- GNU Make
- We're also going to be using the `git` package.

#### Step 2. Install Dependencies

Run the following:

```bash
$ pacman -S gcc python2 git make
```

Build these packages as you normally would.

#### Step 3. Install Node.js

Arch generally comes with the latest Python--I know mine did. Unfortunately, it's Python 3.x and we *need* 2.(6|7) to install Node properly. We will temporarily change the default `python` command to use `python2` instead of `python3`. Run the following:

```bash
$ cd /usr/bin
$ ln -sf python2 python
```

This will make sure every instance of `#!/usr/bin/env python` in the build files use `python2`.

Next, `cd` into a directory you feel comfortable in, and run the following:

```bash
$ git clone https://github.com/joyent/node.git
$ git checkout v0.10.33 # Use the version you want to install
$ ./configure && make && sudo make install
```

#### Step 4. Rid of sudo

This should spit out a bunch of logging that you don't need to pay too much attention to. What you do need to take note of is that, by design, Node will be installed in `/usr/local/bin/node`, NPM will be installed in `/usr/local/bin/npm`, and your Node modules will be installed in `/usr/local/lib/node_modules`. This is great, since `/usr/local` is a directory that your user can own. Let's make that so by running the following:

```bash
$ chown -R $USER /usr/local/
```

Now we can install our Node modules globally without running `sudo`!

#### Step 5. Change Python Back

Now we need to revert that symlink change we made previously for Python. Run the following:

```bash
$ cd /usr/bin
$ ln -sf python3 python
```

#### Step 6. Inspecting Our Changes

Let's make sure we made successful changes. When you run the following commands, you should see the result in the comment.

```bash
$ which node # /usr/local/bin/node
$ which npm # /usr/local/bin/npm
$ which python # /usr/bin/python
```

### Conclusion

Now you can start writing some sweet Node code :) I hope this article helped you out!
