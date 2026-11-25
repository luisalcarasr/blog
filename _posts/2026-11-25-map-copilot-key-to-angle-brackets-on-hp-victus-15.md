---
layout: post
title: Map the Copilot Key to < and > on an HP Victus 15
---

On my HP Victus 15, the Copilot key on the built-in keyboard sends the
`leftmeta + leftshift + f23` key combination in GNOME on Wayland. I use
[`keyd`](https://github.com/rvaiya/keyd) to remap that combination to
`102nd`, the XKB key that produces `<` and `>` with the appropriate
modifier.

## Configure `keyd`

Add the following to `/etc/keyd/default.conf`:

```ini
[ids]
0001:0001:3ca03456

[main]
leftshift+leftmeta+f23 = 102nd
```

The device ID limits this mapping to the built-in `AT Translated Set 2
keyboard`.

## Use and verify the mapping

- Press the Copilot key to type `<`.
- Press Shift + Copilot to type `>`.

I verified the mapping with `xev`: pressing Copilot generated `less`
(`<`), and pressing Shift + Copilot generated `greater` (`>`). I also
enabled `keyd` to start with the system.

## Revert the change

To disable this mapping:

```bash
sudo mv /etc/keyd/default.conf /etc/keyd/default.conf.disabled
sudo keyd reload
```
