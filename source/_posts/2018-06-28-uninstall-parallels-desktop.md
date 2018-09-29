title: 完全卸载 parallels desktop
date: 2018-06-28 19:45:50
tags:
- mac
---

sudo rm -rf /Users/这里改成你的系统用户名/Library/Preferences/com.parallel*

sudo rm -rf /Users/这里改成你的系统用户名/Library/Parallels

sudo rm -rf /private/var/db/Parallels

sudo rm -rf /Library/Logs/parallels*

sudo rm -rf /Library/Preferences/Parallels

sudo rm -rf  /private/var/.Parallels_swap
