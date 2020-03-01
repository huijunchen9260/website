---
title: "Dmenufm Interesting Bugs"
date: 2019-12-27T23:22:41-05:00
draft: false
categories: ["Linux"]
tags:
- Dmenu
- Dmenufm
showDate: true
---

## Dmenufm Interesting Bugs

This is the interesting bugs that I encountered today.

This bug is the one I encounter when I try to implementing `BulkListMass` function after I finished implementing `FM_EYE` function.

The `BulkListMass` function is activated by selecting the variable `$masselection` in the `ActionMenu`. The functionality is good, but somehow `FM_EYE` seems to enter an endless loop, maximizes the usage of CPU, and eventually crash the whole system.

The reason for crashing is that I forgot to set the initial value for the `$masselection` variable, like `masselection="placeholder"`. Just a simple placeholder can prevent the endless loop.

Ok, so why not assigning `$masselection` a variable will cause system to crash? Let's show and explain the relevant part of code of dmenufm so that you can get a clear view.


```sh
# The relevant part for ActionMenu:

ActionMenu () { # Usage: ActionMenu [MSG] [BG_COLOR]
    while [ -n "$actCHOICE" ]; do
	...
	if ...
	elif [ "$allowbulk" != "NotAllowed" ]; then
	    actCHOICE=$(printf '%s\n' "$BACKWARD" "$TARGET" "$allowbulk" "$allselection" "$masselection" "$FM_HIS" "$list" | sed "/^$/ d" | yprompt "$1" "$2")
	...
	fi
	# Outcome matching
	if ...
	elif [ "$actCHOICE" = "$allselection" ] || [ "$actCHOICE" = "$masselection" ]; then
	    bulkselection="false"
	    HERE=$(printf '%s' "$PWD")
	    name=$(printf '%s' "${PWD##*/}")
	    break
	...
	elif [ -f "$actCHOICE" ]; then
	    if ...
	    else
		rollmenu="$actCHOICE"
		HERE=$(printf '%s' "$PWD/$actCHOICE")
		name=$(printf '%s' "$actCHOICE")
		[ "$bulkselection" = "true" ] && bulklist=$(printf '%s\n' "$bulklist" "$HERE")
		break
	    fi
	fi
    done
```

```sh
# Relevant part for FM_EYE

FM_EYE () {
    ...
    ActionMenu "Preview mode: " "$FM_ACTION_COLOR_LV1" || return && allowbulk="NotAllowed"
    [ -n "$HERE" ] && FileOpen "$HERE" &
    while [ -n "$HERE" ]; do
	wmctrl -r :ACTIVE: -N "PREVIEW" &
	ActionMenu "Preview mode: " "$FM_ACTION_COLOR_LV1" || return && allowbulk="NotAllowed"
	[ -n "$HERE" ] && FileOpen "$HERE" &
	wmctrl -c "PREVIEW"
    done
    wmctrl -c :ACTIVE:
    ...
}
```

`...` is the symbol representing irrelevant code.

The explanation is the following:

Since I didn't assign the value for `$masselection`, `$masselection` is an empty string.

In the `FM_EYE` function, the first usage of `ActionMenu` will generate a string for variable `$HERE`, which according to `ActionMenu` function, is the absolute path of the chosen file (`HERE=$(printf '%s' "$PWD/$actCHOICE")`). We also open the file by `FileOpen`.

Next, we enter the `while` loop. Since `$HERE` is not empty string, `[ -n "$HERE" ]` is success. After that, I use `wmctrl` to rename the active window as "PREVIEW".

Furthermore, another usage of `ActionMenu` assigned a new absolute path based on chosen file to `$HERE`, and then open it in the background (the `&` part). Notice that now the active window is the file opened by the second usage of `ActionMenu`, and we know that previous window is named "PREVIEW".

After that, use `wmctrl` to close the window named "PREVIEW", i.e., the window opened by first `ActionMenu`.

After existing the loop, use `wmctrl` to close active window, which would be the window opened by the second `ActionMenu`.

Why the failing to assign a value for `$masselection` will cause system to crash? It is all start when leaving the `ActionMenu`.

When you press `ESC` to escape `ActionMenu`, the `$actCHOICE` is "assigned" as an empty string. Since `$masselection` is also empty string, the test `[ "$actCHOICE" = "$masselection" ]` success! Thus, now `$HERE` is assigned as current working directory (`HERE=$(printf '%s' "$PWD")`). Thus, the `while` loop in `FM_EYE` will execute again, and then we enter an endless loop.
