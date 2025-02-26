# Tips and Tricks

## Basics 

### Mouse Support

Enabling mouse support comes in handy when using an iPad without a keyboard as you can freely change panes in tmux or scroll throughout your apps with your fingers, few popular ones include:

**TMUX:**
```
set -g mouse on
```

**VIM:**
```vim
set mouse=a
```

**EMACS:**
```
(xterm-mouse-mode 1)
```

### Serving development website over HTTPS

Fastest way of using HTTPS on remote machines for development is with the Caddy web server. First install it as an executable, but not service, then create Caddyfile with reverse_proxy line like this:

```json
example.com {
  reverse_proxy 127.0.0.1:1313
}
```

Or start Caddy directly in reverse-proxy mode from CLI:

```bash
sudo caddy reverse-proxy --from example.com --to 0.0.0.0:1313
```

### Starting TMUX/Screen on session start

Adding startup commands is as easy as going to Host settings, scrolling to bottom and inserting command we want to use:

```shell
tmux a -t session_name
screen -rd session_name
```
Or anything other that we want to run when starting a new session. It also can be run from Blink command line:
```shell
mosh host -- tmux a -t session_name
mosh host -- screen -rd session_name
```

### SSH port forwarding from remote machine to iPad

Forwards connections from a port on a local system to a port on a remote host:

```bash
ssh -L 3000:localhost:3000 ssh-host
```
### Blink: Using screen corners

Taping three fingers on the screen will bring a Blink menu in which you can set the Cover, Fill, and Fit setting, adapting Blink to different screens and devices.

- Cover: Edge to Edge
- Fill: Space at the Top and Bottom
- Fit: 5 points on all sides

![img](./tips-and-tricks/Cover-Fill-Fit.png)

### External Display: Apple TV

You can use Apple TV as a second monitor, just start screen share on Apple TV and Blink will use it as second monitor, not just mirroring. This is really cool if you are doing a presentation, or if you are on the couch with your phone and want to have a bigger display!

### External Display: Split View

For the external display to work, Blink needs to be an active window on the iPad. A very cool way to do this is to use Split View on the iPad, giving Blink ½ or ¼ of the screen, while the rest can be taken by Safari or anything else you need!

### Display: What is Blink Window? 

From iPad OS 13, we have multiple windows support inside Blink, and each window can have infinite tabs. 
![Blink Shell Windows](./tips-and-tricks/windows.png)

You can also move tabs between the iPad window and External Display window with `⇧ Shift + ⌘ Cmd + O` or change focus to the other window using `⌘ Cmd + O`.

### Keyboard: Separate Keys

By switching off the "Same for both sides" option in Config > Keyboard > ⌥ Option you can use one key as a `Meta` and the second as a normal `⌥ Option/Alt`. It's particularly helpful for users that use languages containing diacritic letters.

## Advanced

### Inline Images

Blink supports displaying images over SSH when using [iTerm2 imgcat](https://iterm2.com/utilities/imgcat). Put it in local .bin folder ($HOME/.bin or any other in your %PATH) and use it as this:

```bash
imgcat image.png
```

**NOTE: This does not work in terminal multiplexers (TMUX, GNU Screen) or inside Mosh.**

Alternative that works inside every environment, but needs Rust installed is VIU. Install it using Cargo:

```bash
cargo install viu
```
And use:

```bash
viu image.png/animation.gif
```
More information: [VIU GitHub Repository](https://github.com/atanunq/viu)

### TMUX: Copy/Paste using OSC52

Enabling copy/paste between tmux and iOS needs SSH or Blink Mosh Server.

```
set -g set-clipboard on
set -ag terminal-overrides "vte*:XT:Ms=\\E]52;c;%p2%s\\7,xterm*:XT:Ms=\\E]52;c;%p2%s\\7"
```

VIM users can set this to have similar experience to VIM:

```
bind-key -T copy-mode-vi v send-keys -X begin-selection
bind-key -T copy-mode-vi y send-keys -X copy-selection
bind-key -T copy-mode-vi r send-keys -X rectangle-toggle
```

### How to enable OSC52 support in Mosh?
You need to build Mosh from source with this patch: [Support additional OSC 52 clipboard types #1104](https://github.com/mobile-shell/mosh/pull/1104#issuecomment-710754740)

## Editors

### VIM: Copy/Paste between remote machine and iOS

To enable copy/paste you'll need to install [ojroques/vim-oscyank](https://github.com/ojroques/vim-oscyank) and add this line to .vimrc:

```vim
autocmd TextYankPost * if v:event.operator is 'y' && v:event.regname is '' | OSCYankReg " | endif"
```
And enable this setting:
```vim
set clipboard& clipboard^=unnamed,unnamedplus
```

### VIM: Exit insert mode without ESC key

Change CapsLock in Blink Settings to Control and press Ctrl(CapsLock) + [ to exit insert mode on iPad keyboards without ESC key.

### Emacs: Copy/Paste using OSC52

To enable copy/paste on Emacs, you can install [clipetty](https://github.com/spudlyo/clipetty). See the GitHub repo for the complete documentation, but for basic configuration using [`use-package`](https://github.com/jwiegley/use-package), you can add this to your config:

```
(use-package clipetty
  :ensure t
  :hook (after-init . global-clipetty-mode))
```

### Emacs: Custom Presses

Using the Config > Keyboard > Custom Presses settings you can add new shortcuts. For instance, sending `ctrl-space` when hitting `ctrl-space`, as opposed to bringing up iPadOS's emoji menu. Now Emacs cut & paste doesn't need to rely on `ALT - x set-mark-command`.

### Emacs: Change ALT to Meta

Change "Press send" to **None** and "As modifier" to **ESC** option in Config > Keyboard > ⌥ Option so you can use it as `Meta`. From now on `⌥ Option/Alt - x` will work as `Meta - x`.
