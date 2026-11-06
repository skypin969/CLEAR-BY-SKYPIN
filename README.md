# CLEAR BY SKYPIN

**A root module that cleans junk from your Android phone, safely, from a simple menu in Termux.**

Telegram: [@Skypin969](https://t.me/Skypin969) · Version: **v9.6.9** · Works with KernelSU, KernelSU Next, Magisk and APatch

```
==========================================
            CLEAR  BY  SKYPIN
==========================================
Telegram : @Skypin969
Optimize your Android system by removing unnecessary files.

Main Menu
==========================================
1. Clear Dalvik Cache
2. Remove System Dropbox Logs
3. Delete Temporary Files
4. Erase Tombstone Logs
5. Remove Empty Directories
6. Clear Thumbnail Cache
7. Clean System and Shader Cache
8. Perform FSTRIM Partition Optimization
9. Clear RAM Cache
10. Kill Third-Party Apps
11. Clear Deleted Items (gallery trash)
12. Remove Leftover App Data
13. Find Duplicate Files (Download)
14. Run All (safe, without 1, 10, 13)
0. Exit
==========================================
```

## What is it?

CLEAR BY SKYPIN is a systemless root module. After you install it, you open Termux, type `su` and then `clearopt`, and you get a colored menu with 14 cleaning and optimization tools.

It is built around one idea: **clean as much junk as possible, but never touch anything important.** Every action shows a preview first, and nothing is deleted until you confirm.

It was written from scratch, step by step, and tested on real devices on two different ROMs. It does not depend on BusyBox, a separate FSTRIM module, or a specific ROM.

## Features

| # | Menu | What it really does |
|---|------|---------------------|
| 1 | **Clear Dalvik Cache** | Resets the compiled code (ART/dex) of every app with `cmd package compile --reset`, then recompiles with `bg-dexopt-job`. If a ROM refuses the bulk command, it resets app by app. Takes several minutes. |
| 2 | **Remove System Dropbox Logs** | Deletes old system logs from `/data/system/dropbox`, `/data/misc/logd` and `/data/local/traces` (older than 24 hours). |
| 3 | **Delete Temporary Files** | Removes old files from `/data/local/tmp` and `/data/anr`, old items inside app cache folders in shared storage, and leftover download files (`.tmp`, `.temp`, `.crdownload`, `.part`, `.partial`). Then trims app caches with `pm trim-caches`. |
| 4 | **Erase Tombstone Logs** | Deletes old crash dumps from `/data/tombstones`, and clears the logcat buffer. |
| 5 | **Remove Empty Directories** | Deletes empty folders in shared storage, in several passes so nested empty folders disappear too. Top-level folders are never deleted. |
| 6 | **Clear Thumbnail Cache** | Deletes `.thumbnails` folders and `.thumbdata*` files. They are rebuilt automatically. |
| 7 | **Clean System and Shader Cache** | Deletes old shader and GPU cache folders of apps. They are rebuilt automatically. |
| 8 | **Perform FSTRIM Partition Optimization** | Runs FSTRIM only on safe partitions (`/data`, `/cache`, `/metadata`, `/system`, `/system_ext`, `/vendor`, `/product`, `/odm`, and only if they are f2fs or ext4). If the ROM has no `fstrim` program, it uses Android's built-in `sm fstrim`. Then it runs a **smart f2fs garbage collection**: it only starts when the filesystem is dirty enough, only when the battery is above 30% or charging, and it always switches back to normal afterwards. |
| 9 | **Clear RAM Cache** | Drops kernel caches and compacts memory. Shows available RAM before and after. |
| 10 | **Kill Third-Party Apps** | Force-stops all user-installed apps and also **removes their cards from Recents**. It never stops Termux, the root manager, your keyboard, your launcher, or anything in the whitelist. |
| 11 | **Clear Deleted Items (gallery trash)** | Permanently empties the gallery trash (Android 11+ `.trashed-*` items) and then rescans the media database, so photos and videos you already deleted are really gone. |
| 12 | **Remove Leftover App Data** | Finds `Android/data` and `Android/obb` folders of apps that are already uninstalled and removes them. `Android/media` leftovers are only reported, never deleted. |
| 13 | **Find Duplicate Files (Download)** | Finds identical files in the `Download` folder using SHA-256. It keeps the oldest copy and lets you delete the rest, one group at a time. |
| 14 | **Run All** | Runs the safe cleanups in one go (11, 3, 2+4, 6, 7, 5, 12, 8, 9). It skips 1, 10 and 13 on purpose, because they take long or need your decision. |

## Safety first

This module was designed so that cleaning cannot hurt your data.

- **Preview by default.** Every action first shows what it would do. Nothing is changed until you answer `y` to `Run for real? (y/n)`.
- **One deletion gate.** Every delete goes through a single guard function. It refuses system paths (`/system`, `/vendor`, `/data/adb`, `/data/data`, ...) and any path that contains important folders such as `/databases/`, `/shared_prefs/`, `/files/`, `/DCIM/`, `/Pictures/`, `/Documents/`, `/Movies/`, `/Music/`, `/Media/` and `/Backups/`.
- **No chats or app data are deleted.** Only caches, temporary files, logs and trash are touched. WhatsApp and Telegram chats, and their chat lists, are not affected.
- **Your own protect list.** Add any path prefix to `/data/adb/clearopt/protect.txt` (one per line) and it will never be deleted.
- **Everything is logged** to `/data/adb/clearopt/log.txt`.
- **FSTRIM never touches** `nvdata`, `nvcfg`, `persist` or `protect_*` partitions.

## Requirements

- A rooted device with **KernelSU**, **KernelSU Next**, **Magisk (v20.4+)** or **APatch**
- **Termux** (to open the menu)
- **Android 11 or newer** (menu 11 needs the Android 11+ gallery trash; the other menus work on older versions in theory but are untested)
- No BusyBox needed

## Installation

1. Download `CLEAR-BY-SKYPIN-v9.6.9.zip` from the **Releases** page.
2. Open your root manager (KernelSU / Magisk / APatch), go to **Modules**, tap **Install** (or the **+** button) and choose the zip.
3. The installer shows the **CLEAR BY SKYPIN** banner. Wait until it says `Module installed successfully`.
4. **Reboot.**

Or install from Termux with KernelSU:

```sh
su -c "/data/adb/ksud module install /sdcard/Download/CLEAR-BY-SKYPIN-v9.6.9.zip"
```

## How to use

Open Termux and run:

```sh
su
clearopt
```

If the short command is not found, run the script directly:

```sh
su -c "sh /data/adb/clearopt/clearopt.sh"
```

Then type a number and press Enter. For every cleaning action you will see a preview and the question `Run for real? (y/n)`. Answer `y` to clean, or `n` to cancel.

> Tip: type `su` first, wait for the `#` prompt, and only then type `clearopt`.

### Command line mode

You can also run a single task without the menu. By default these commands only **preview**. Add `DRY=0` to really run them.

```sh
su -c "clearopt info"                  # device, ROM, filesystems, gallery app
su -c "clearopt trash"                 # preview gallery trash cleanup
su -c "DRY=0 clearopt trash"           # really clean it
su -c "clearopt all"                   # preview everything safe
su -c "clearopt dup"                   # list duplicate groups in Download
su -c "DRY=0 clearopt dupdel 2"        # delete the extra copies of group 2
```

Available commands: `menu`, `all`, `info`, `trash`, `temp`, `logs`, `thumb`, `shader`, `empty`, `fstrim`, `gc`, `orphan`, `dup`, `dupdel N`, `ram`, `dalvik`, `kill`.

### Optional settings

Set these as environment variables in front of a command:

| Variable | Default | Meaning |
|----------|---------|---------|
| `DRY` | `1` | `1` = preview only, `0` = really delete |
| `AGE` | `1440` | Minimum age in minutes of temp files and logs to delete (24 hours) |
| `AGE_PART` | `10080` | Minimum age in minutes of partial downloads to delete (7 days) |
| `GC_MIN` | `5` | Start f2fs GC only if dirty segments are at least this percent |
| `FORCE` | `0` | `1` = run GC even below the threshold |
| `GC_SEC` | `60` | How many seconds forced GC runs |
| `DUP_DIRS` | `Download` | Folders to scan for duplicates |

### Config files

All config lives in `/data/adb/clearopt/`:

| File | Purpose |
|------|---------|
| `protect.txt` | Paths that must never be deleted (one prefix per line) |
| `whitelist.txt` | Apps that menu 10 must never stop (one package per line). Created on first use with Termux, Termux:API, Termux:X11, Magisk, KernelSU and KernelSU Next |
| `log.txt` | Log of everything that was deleted or skipped |

## Good to know

- **Alarms and reminders.** Force-stopping an app cancels its pending alarms. If you use a third-party clock, alarm or calendar app, add its package name to `whitelist.txt` before using menu 10.
- **Menu 1 takes time** (several minutes) and the first launch of apps afterwards can be slightly slower while they recompile. Keep the phone charging.
- **Menu 10 is optional.** Apps you kill simply restart when you open them again.
- **Menu 11 is permanent.** Items emptied from the gallery trash cannot be restored.
- **Menu 12** only removes data of apps that are already uninstalled.
- **Menu 13** only scans the `Download` folder, keeps the oldest copy of each group, and deletes only the group number you choose.
- **FSTRIM often reports 0 bytes** on modern f2fs phones. That is normal, because the filesystem already trims free blocks continuously. The garbage collection step is what makes a visible difference.
- **Gallery apps.** The trash cleanup works with apps that use the standard Android trash (for example the LineageOS Glimpse gallery and the Xiaomi gallery). Galleries that keep their own private trash may not be covered.

## Tested devices

| Device | ROM | Root | Result |
|--------|-----|------|--------|
| Redmi Note 15 4G | HyperOS, Android 16 | KernelSU Next | All menus tested |
| Poco X7 5G | LineageOS 24, Android 17 | KernelSU Next | All menus tested |

Different phones and ROMs behave differently, so please try the preview first. If something does not work on your device, open an issue with your device, ROM, Android version and the output of `su -c "clearopt info"`.

## Uninstall

Remove the module in your root manager and reboot. The module deletes its script automatically. To remove the settings and logs as well:

```sh
su -c "rm -rf /data/adb/clearopt"
```

## Folder structure

```
module/
├── module.prop
├── customize.sh          # installer screen with the banner
├── uninstall.sh
├── system/bin/clearopt   # the cleaner script
└── META-INF/             # standard flashable-zip files
```

## Disclaimer

This module deletes files. It is designed to be careful and shows a preview first, but you use it at your own risk. Keep a backup of anything important. The author is not responsible for data loss or other problems caused by its use.

## Author

Made by **Skypin** — Telegram: [@Skypin969](https://t.me/Skypin969)

## License

Released under the MIT License. See [LICENSE](LICENSE).
