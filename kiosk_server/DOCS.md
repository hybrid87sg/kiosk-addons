# Kiosk Signage Server

The server that **Kiosk Admin** (phone) and **Kiosk Player** (TV) connect to. It
remembers your playlists, players and users, and passes your videos and pictures
from Google Drive to the TVs.

## Before you start

You need the one-time Google setup, which gives you:

- **Google Drive folder ID**: the last part of the Signage folder's web address.
- **The service account key file** (a `.json` file), and the folder shared with that service account.

Signing in doesn't involve Google: people use a username and password you make here.

## Install

1. Go to **Settings → Add-ons → Add-on store**, open the **⋮** menu (top right)
   and choose **Repositories**. Paste `https://github.com/hybrid87sg/kiosk-addons`,
   click **Add**, then **Close**, and refresh the page.
2. Find **Kiosk Signage Server** in the store, open it and click **Install**.
   Home Assistant downloads it ready-made; nothing is built on your device.
3. Open the add-on's **Configuration** tab, fill in the settings, click **Save**,
   then **Start**. The **Log** tab should say `Kiosk server running on port 3000`.
4. Switch on **Start on boot**, **Watchdog** and **Show in sidebar**.
5. Add your Google key. Open **Kiosk Signage** in the sidebar, go to
   **Settings → Google key**, and either **upload** the `.json` key file or
   **paste** its text. The key stays on this Home Assistant.
6. The same card shows the service account's email. In Google Drive, share your
   signage folder with that email as **Editor** (**Content manager** on a shared
   drive). Then press **Test connection** under **Google Drive folder**.
7. Make your own username: **Users → Add someone**, with a password of at least
   8 characters and the role **Spongebob**. You sign in with it on the phone app.
   This sidebar page never asks for it: your Home Assistant login covers it.

**Another way to add the key:** copy the key file into the add-on's config
folder (the **addon_configs** share, in the folder ending in `_kiosk_server`)
and name it `service-account.json`. A key added on the Settings page is used
instead of this file.

**Updates:** when a new version is out, Home Assistant shows an **Update** button
on this add-on's page. Your settings, key file and data are kept.

**Installing by hand instead:** copy the `kiosk_server` folder from a release's
`kiosk_server-addon.zip` into the **addons** share, choose **Check for updates**
in the store's ⋮ menu, and install it from **Local add-ons**. Home Assistant
then builds it on your device, which takes a few minutes. An add-on installed
this way is a separate add-on from the store one, with its own settings and data.

## The web interface: the main control

- **In Home Assistant:** open **Kiosk Signage** in the sidebar. It's for Home Assistant admins, who get
  full (Spongebob) access.
- **In any browser:** go to `http://<address>:3000`, or click **Open web UI** on this add-on's page.
  People you made a user for sign in with their username and password; they don't need a Home
  Assistant account.

### Playlists and locations

Every location has two playlists made for it, **Landscape** and **Portrait**.
A screen plays the one that fits how it is mounted, with nothing to assign, so
every screen has to be in a location. They are listed under **Locations**. For
a screen that should show something different, add an **extra playlist** there
and choose it in the screen's **Edit** form. A landscape screen can also be set to
**upside down**; that needs Kiosk Player 0.5.0 or newer on the TV.

## Files, dates and Google Drive

Each file can have a first and last day to show (Library → calendar button). Before the first day it
waits in the library; after the last day it leaves Kiosk, every playlist and every screen, and waits under
**Expired and removed**, where it can be restored. Kiosk never deletes anything from Google Drive.

## Locations and working hours

The web interface's **Locations** menu lists every location; each can have working hours. Outside them,
its screens go black (and into standby where the TV allows it), and come back at opening time.

## Notifications

This add-on can ask Home Assistant to send notifications. In the web interface, **Settings →
Notifications**, tick the devices (phones with the Home Assistant app appear as `notify.mobile_app_…`).
They hear about screens that are offline for 5 minutes during working hours, and when they're back.

## Adding TVs

On each TV, open Kiosk Player and choose **Find my server automatically**. The TV then shows up on the
web interface's Screens page under **New screens waiting**; click **Add**. The TV must be on the same
network as Home Assistant. If it can't find the server, type `http://<Home Assistant's IP address>:3000`
instead (Settings → System → Network shows the IP address).

For the phone app, use your Home Assistant's Tailscale address plus `:3000`.

## Other sites (relay mode)

With Home Assistants at several sites, keep this add-on in **main** mode on one of them. At each other
site, install the Tailscale add-on (with userspace networking off) and this add-on in **relay** mode, with
that site's **Site name** and the main server's Tailscale address (e.g. `100.101.102.103:3000`). A relay
needs no Google settings. Its TVs find it by themselves and show up on the main web interface, labelled
with their site.

## Managing screens away from home

Your phone needs a way into your home network. The simplest is the **Tailscale**
add-on plus the Tailscale app on your phone. Then use the Home Assistant
machine's Tailscale address in Kiosk Admin.

## Playback copies

Videos are often 4K. Weak Android TV boxes play 1080p H.264 smoothly and struggle
with anything heavier. **Make playback copies** stays off until you turn it on,
so installing an update does not start encoding the videos you already have. Once it is on, the
add-on uses ffmpeg to make:

- a **1080p H.264** copy (High profile, level 4.1, constant frame rate), which
  is what most boxes download
- a **720p** copy, for a box that reports it cannot decode 1080p
- optionally a **4K HEVC** copy (8-bit), only for a box with a 4K screen that
  says it can decode HEVC

The original file in Google Drive is not changed. Kiosk Player tells the server
what it can decode, and downloads the copy that fits. An older player app gets
the 1080p copy, or the original until that copy is ready.

Making copies uses a lot of processor time. Each copy runs at low priority, on
one core by default (**Encoder threads**), and is stopped if it runs longer
than two hours. After a restart, copies of videos already in the library start
one at a time, with a short pause between them, so a Raspberry Pi can finish
booting. On a slow machine, turn **Make playback copies** off in the add-on's
Configuration tab (or on the web Settings page) if Home Assistant becomes
unresponsive. Leave **Also make a 4K HEVC copy** off unless this machine is a
faster computer.

## Keep a local copy of each file

With **Keep a local copy of each file** on, each file is downloaded from Google
Drive once and then sent to every TV from this machine, which saves internet
data and is faster. The copies are stored in this add-on's data folder and are
deleted automatically when a file is removed from the library.
