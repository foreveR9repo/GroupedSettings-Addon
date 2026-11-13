# GroupedSettings Addon for Other Apps

An iOS tweak designed to adapt and apply the exact visual customization logic from GroupedSettings17 to compatible Apps.

## Overview

- **Tweak Name:** GroupedSettings Addon for Other Apps
- **Target OS:** iOS 15.0 – 26.0.1
- **Primary Function:** Replicates the styling, hierarchy, and appearance mechanics of GroupedSettings17 across targeted applications without altering default features or adding extra settings panels.

## Technical Details

- **Frameworks:** UIKit, Logos (Theos)
- **Origin:** Adaptation of the GroupedSettings17 source code logic, modified to hook into selected application bundle IDs.

## App detection

The sub-menus only list apps that are **actually installed** on the device
(read from `LSApplicationWorkspace`, recomputed every time a sub-menu is opened):

- built-in Apple and "popular" third-party apps are hidden if they are not installed
  (e.g. a removed Apple app, WhatsApp not present);
- apps that are still downloading (placeholders), offloaded or deleted are ignored;
- any other installed app is automatically added to the "Third-party apps" menu;
- if the system API is unavailable, the built-in lists are shown as-is
  (rather than an empty menu).

## Dependency and repository

This addon requires **GroupedSettings17** (`com.raphetsoso.groupedsettings17`), declared as a
`Depends`. That package is **not available on Havoc**; it is distributed on this repository:

https://raphetsoso.github.io/repo/

Add the repository to Sileo / Zebra / Cydia, then install the addon: GroupedSettings17 will be
installed automatically. If the repository has not been added, the package manager will show a
missing dependency error (`com.raphetsoso.groupedsettings17`): in that case, add the repository
above. When installing through `dpkg`, the `preinst` script prints the same advice.

## How per-app switches reach the apps

The per-app switches live in the GroupedSettings17 domain (`addon.<bundle id>`).
Reading another package's preference domain from inside a sandboxed app can silently
return nothing, which used to be interpreted as "use the default" (ON for Apple apps),
so turning an app OFF had no effect.

Settings now mirrors every `addon.*` key into
`/var/jb/var/mobile/Library/Preferences/com.raphetsoso.groupedsettings17addon.mirror.plist`
(refreshed each time Settings launches and each time a switch changes). Apps read, in
order: cfprefsd, the mirror, the domain plist, then the default. With `GS_DEBUG` on,
`GroupedSettings17Addon.log` shows which source answered, e.g.
`pref addon.com.apple.mobilesafari : valeur=0 source=miroir -> désactivé`.
