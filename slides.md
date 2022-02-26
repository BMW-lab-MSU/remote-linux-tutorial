---
# try also 'default' to start simple
theme: default
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
# background: https://source.unsplash.com/collection/94734566/1920x1080
# apply any windi css classes to the current slide
class: 'text-center'
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# some information about the slides, markdown enabled
info: |
  ## Remote computing 
  How to use remote Linux computers for scientific computing

---

<div class="flex flex-row content-evenly place-content-evenly items-center">
<div>
<img class="w-60" src="/Tux-simple.svg"/>
</div>
<div>
<ic-twotone-cloud class="text-13xl text-true-gray-400" />
</div>
<div>
<ion-terminal class="text-13xl text-true-gray-600" />
</div>
</div>

# Remote computing


---
layout: image-right
image: https://images.unsplash.com/photo-1542311121-071d8a3fd374?ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&ixlib=rb-1.2.1&auto=format&fit=crop&w=934&q=80
---

# Outline
- command-line (CLI) basics
- ssh
- tmux
- copying files between computers
- examples
    - MATLAB, X11 forwarding
    - outputting to a file
    - Jupyter notebooks


---

# CLI basics

<div class="grid grid-cols-3 gap-x-8">
<div>

## Navigation
- change directory: 
```bash
cd
```
- list directory contents: 
```bash
ls
```
- make directory:
```bash
mkdir
```

</div>

<div>

## File operations
- copy files:
```bash
cp
```
- move files:
```bash
mv
```
- delete files:
```bash
rm
```
</div>

<div>

## Misc
- redirect stdout to a file:
```bash
example-command > out.txt
```
- wildcards:
```bash
rm *.txt
```
- run in background:
```bash
some-command &
```
- tab completion: press tab to autocomplete commands, file names, etc.
</div>
</div>



---

# Connecting to the remote computer Using SSH (secure shell protocol)

<br><br>
usage: <code>ssh user@hostname</code>

**example:**
```bash
ssh trevor@bz-ece-whitgpu1
```

<br>

Note: when connected via the MSU VPN, add `.msu.montana.edu` to the hostname:
```bash
ssh bmw@bz-ece-whitlab1.msu.montana.edu
```

---

# Ensuring jobs don't accidentally die

<div class="grid grid-cols-2 gap-x-4"><div>
<emojione-warning class="text-6xl" />

- By default, exiting the ssh session kills your jobs
- Sometimes ssh likes to exit on its own <emojione-unamused-face style="display: inline-block"/>

<br>
<emojione-white-heavy-check-mark class="text-6xl" />

Use *tmux* so we can detach our processes from the controlling terminal

- Can have multiple panes (terminal multiplexing)
- Can have multiple windows
- Allows us to run multiple jobs easily

</div>
<div>
<img class="w-100 mx-auto items-center self-center" src="/tmux.png" />
</div>
</div>



---

# tmux basics

<div class="grid grid-cols-2 gap-x-4">
<div>

- **create a new session**
```bash
tmux
```
- **attach to last session**
```bash
tmux attach
```
- **detach from a session**
```
ctrl+b d
```
- create a new window
```
ctrl+b  c
```
- change window
```
ctrl+b  <window-number>
```

</div>
<div>

- split pane horizontally
```
ctrl+b "
```
- split pane vertically
```
ctrl+b %
```
- switch between panes
```
ctrl+b <arrow-key>
``` 

<br> <br>

### Guides / cheatsheets
- <a href="https://www.ocf.berkeley.edu/~ckuehl/tmux/">https://www.ocf.berkeley.edu/~ckuehl/tmux/</a>
- <a href="https://tmuxcheatseet.com/">https://tmuxcheatsheet.com/</a>
</div>
</div>

---

# Copying files using SCP (secure copy)

From local to remote:
```
scp file user@hostname:<location>

scp test.py trevor@bz-ece-whitgpu1:~/project/
```

From remote to local:
```
scp user@hostname:<file-location> <local-file-location>

scp trevor@bz-ece-whitgpu1:~/project/test.py some-dir/test2.py
```

### Need to sync lots of files?
- Globus, OneDrive, Box, etc.   <mdi-cloud-download style="display: inline-block" class="text-blue-300"/>
- use git for code (our lab has a GitHub Organization) <emojione-nerd-face style="display: inline-block"/> <fa-brands-github style="display: inline-block"/>

---
layout: cover
class: text-center
---

# Examples

---

# Showing plot windows with X11 forwarding
What if we need plots?

```matlab
x = 0:0.001:2*pi;
y = sum(rand(4,1) .* sin(randi(5, 4, 1) * pi .* x + rand(4,1)));
plot(x,y)
```

<br><br>

- use X11 forwarding if you only need to display a few graphics
- if you need lots of graphics, use VNC, RDP, etc.

<br>

### X11 forwarding
```bash
ssh -Y user@hostname
```

- On Windows, use MobaXterm

Note: X11 forwarding might be obsolete in the future if Wayland becomes the de-facto display server

---

# Multiple jobs, redirecting output to a file

### Redirect output
```matlab
function silly(n)
disp("n = " + num2str(n))
pause(1)
disp("n^2 = " + num2str(n^2))
end
```

- run from cli and redirect output to a file
```bash
matlab -batch "silly 2" > 2.txt
```
- if you want to see the output while the program is running
```bash
matlab -batch "silly 2" | tee 2.txt
```

<br>

### Running multiple jobs
1. run in the background using a trailing &
2. use multiple tmux windows/panes

---

# Jupyter notebooks

- use port forwarding, a.k.a tunneling
```bash
ssh -L remote-port:host:host-port user@hostname

ssh -L 8888:localhost:8888 trevor@bmw-lab-fedora
```

- Jupyter notebook's default port is 8888
- launch notebook as so:
```bash
jupyter notebook --no-browser
```

