# KESTREL-UPDATES
# Kestrel OS: install guide

Kestrel OS is a hobby operating system for 64-bit UEFI PCs. `KestrelOS.iso` is the **starter edition**: it boots, gets online, installs itself, and then downloads the full Kestrel with `sys update`.

## Read this first: you need Ethernet right now

**Kestrel can only get online through a wired Ethernet port, and only some ports work.**

- **Wi-Fi adapters do not work yet.** That includes USB Wi-Fi sticks (such as the TP-Link Archer T2U Plus) and built-in Wi-Fi cards. The commands `wifi scan` and `wifi join` exist, but they do not give you a working connection today. Don't plan on them.
- **The supported Ethernet chip is the Realtek RTL8125 (2.5 Gigabit).** Many recent motherboards have one. Other Ethernet chips (for example Intel I225/I226, Realtek 1 Gigabit chips, USB-to-Ethernet adapters) are **not supported** yet. If `net` says "no supported network adapter", your PC's port is not one Kestrel can use.
- **Without a supported Ethernet port, you cannot run `sys update`.** You can still boot the starter edition and look around.

## What you need

1. A UEFI PC (any normal PC from the last ~10 years; not legacy/BIOS-only).
2. An NVMe SSD to install to. SATA and USB disks are not supported yet. The disk must use 512-byte sectors (nearly all do).
3. A USB stick (the ISO is about 5 MB; any size works). **Everything on the stick will be erased.**
4. An Ethernet cable from your PC to your router, and a router that hands out addresses by DHCP (normal home routers do).
5. Another computer to put the ISO on the stick.

## Step 1: put the ISO on a USB stick

Use one of these on the other computer:

- **Rufus** (Windows): choose `KestrelOS.iso`, press Start, and if asked, pick **Write in DD Image mode**.
- **balenaEtcher** (Windows, Mac, Linux): *Flash from file*, pick `KestrelOS.iso`, pick the stick, Flash.
- **Linux/Mac terminal:** `sudo dd if=KestrelOS.iso of=/dev/sdX bs=4M` (replace `/dev/sdX` with your stick; check it carefully, as the wrong device will be erased).

## Step 2: boot it

1. Plug the Ethernet cable in, then plug the stick into the PC and restart.
2. Open the boot menu (usually F11, F12, F8 or Esc right after power-on) and choose the stick. It may be listed as "UEFI: <stick name>". Pick the **UEFI** entry.
3. If the PC refuses to start it, open the firmware settings (Del or F2) and **turn Secure Boot off**. Kestrel is not signed with a Microsoft key.
4. You should see `Kestrel OS` and a `kestrel>` prompt. Type `help` to see the commands.

## Step 3: check the Ethernet connection

```
net
```

This shows your adapter, whether the cable is connected, and your address. If the address is empty, run:

```
dhcp
```

Then prove you are online:

```
ping github.com
```

If you see replies, you're online.

### Ethernet troubleshooting

| What you see | What to do |
|---|---|
| `no supported network adapter` | Your PC's Ethernet chip isn't a Realtek RTL8125. It can't be used yet. |
| link down / `DHCP failed` | Check the cable and the router's lights. Try another cable or port. Run `dhcp` again after plugging in. |
| `dhcp` works but `ping github.com` fails | Try `dns github.com`. If that fails, your router's DNS is down or the network blocks it. |
| It worked on a hotel / school / work network and then stopped | These networks often need a login page. Kestrel can't use those; use your home router. |

## Step 4: install to the disk

```
install
```

It lists your NVMe disks and gives two choices (and a third to undo the first):

1. **Install alongside** what's on the disk. Nothing is erased. Kestrel is added to the EFI partition and a "Kestrel OS" boot entry is created. **Choose this if the PC has Windows or anything else you want to keep.**
2. **Erase the whole disk** and install only Kestrel. You must type `ERASE DISK` to confirm. Everything on that disk is lost.
3. **Remove** an alongside install.

Both choices ask you to type a confirmation first. When it says *Done*, nothing else is needed yet.

## Step 5: get the full Kestrel

With the Ethernet connection still working:

```
sys update
```

This downloads the full Kestrel from GitHub, checks it (size, SHA-256 and that it's a genuine Kestrel image), and replaces the starter edition **on the disk you just installed to**. Then:

```
reboot
```

Remove the USB stick, and when the PC restarts choose **Kestrel OS** in the firmware boot menu (some PCs show it automatically). The full version has the app system: the Files app, the Kestrel web browser, `app install`, and more.

`sys update` only updates the installed copy. If you run it without having run `install` first, it tells you to install first. The USB stick itself is never updated; to refresh the stick, write a new ISO to it.

## Command cheat sheet

| Command | What it does |
|---|---|
| `help` | List the commands |
| `net` / `dhcp` | Show or start the network connection |
| `ping <name>` / `dns <name>` | Test the connection |
| `install` | Install Kestrel to the NVMe disk |
| `sys check` | Is a newer version available? |
| `sys update` | Install the latest full version |
| `sys version` | Show the running version (starter edition shows "lite") |
| `reboot` / `shutdown` | Restart or power off |

## If something goes wrong

- **The PC doesn't show the stick in the boot menu:** rewrite it in *DD Image mode*, and check the UEFI/Secure Boot settings above.
- **`install` says no NVMe disk:** Kestrel only supports NVMe SSDs right now.
- **`sys update` says no release was found:** the update server hasn't got a published release yet. This is not a problem with your PC.
- **Anything else:** type `dmesg` to see the startup messages; that's the first thing to look at.



This was written by ai to clean it up
