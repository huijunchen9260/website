---
title: "Arch Tips"
date: 2019-11-09T18:11:12-05:00
draft: false
categories: ["Linux"]
tags:
- Arch
---

## Firefox cannot paste anything on facebook manager

Reference: [Solution](https://webapps.stackexchange.com/questions/73068/why-cant-i-paste-into-comments-in-facebook-in-firefox#75989)

Type `about:config` in New Tab, and search for `dom.event.clipboardevents.enabled`, and change the value to enable.

## Get system info on command line

`uname -a`

## Clean orphan packages

`sudo pacman -Rns $(pacman -Qtdq)`


## Clean pacman cache

Safe way:

Install pacchace first: `sudo pacman -S pacman-contrib`
Then run paccache:
		- Delete all except three recent package: `sudo paccache -r`

Direct way:
		- Delete all: `sudo pacman -Scc`
## Printer:
	- HP Printers:
		- Reference: [Link(Second Answer)](https://unix.stackexchange.com/questions/359531/installing-hp-printer-driver-for-arch-linux/)
		- Plug-in printer.
		- GUI configuration for printers: `sudo pacman -S system-config-printer`
		- Install CUPS, Common UNIX Printing System, and HP Linux-imaging and printing:
			- `sudo pacman -S cups hplip`
		- Install driver with root priviledge: `sudo hp-setup -i`
		- Check your printer is ready in `system-config-printer`
		- Go to `zathura` and print your file!
		- If the printer says "unplugged or turned off", then delete the printer in the `system-config-printer` and add it again.

