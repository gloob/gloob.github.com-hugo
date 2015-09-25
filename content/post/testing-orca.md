---
Description: How to test Orca.
Keywords:
- Community
- Orca
- OSS
- Accessibility
- a11y
- GNOME
Tags:
- Community
- OSS
- Accessibility
- GNOME
Topics:
- Accessibility
- OSS
- GNOME
date: 2010-08-01
title: Testing Orca
---

# How to test Orca

## Prerequisites

* orca master: `$ git clone git://git.gnome.org/orca orca`
* accerciser master: `$ git clone git://git.gnome.org/accerciser` (Impressive tool for AT)
* gtk2-examples: (probably) `$ sudo apt-get install gtk2.0-examples`

Run it!

```bash
cd ./test/harness (in orca root)
./runall.sh -a ../keystrokes/gtk-demo/ > ~/gtk-demo.out 2>&1
```
