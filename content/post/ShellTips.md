---
title: "Shell Scripting Tips"
date: 2019-11-09T18:46:16-05:00
draft: false
---


- fff
	- Can add the following function to the `/usr/bin/fff`, put them in the case `key() {`:
	- Toggle executable flag.

	```bash
        ${FFF_KEY_EXECUTABLE:=X})
            [[ -f ${list[scroll]} && -w ${list[scroll]} ]] && {
                if [[ -x ${list[scroll]} ]]; then
                    chmod -x "${list[scroll]}"
                    status_line "Unset executable."
                else
                    chmod +x "${list[scroll]}"
                    status_line "Set executable."
                fi
            }
        ;;
	```
	- Open all Images

	```bash
        ${FFF_IMAGE_OPEN:=I})
            sxiv -ft * 2>/dev/null &
            status_line "Open all image in sxiv."
        ;;
	```

	- Extract files.

	```bash
	${FFF_EXTRACTION:=O})
	    ext "${list[scroll]}" > /dev/null 2>&1
	    open "$PWD"
            status_line "Extraction complete."
	```
