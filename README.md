# Kiosk Signage add-ons for Home Assistant

This repository lets Home Assistant install the **Kiosk Signage Server** add-on
with a few clicks. The server runs digital signage: the Kiosk Player app on your
TVs and the Kiosk Admin app on your phone both talk to it.

## Install

1. In Home Assistant, go to **Settings → Add-ons → Add-on store**.
2. Open the **⋮** menu (top right) and choose **Repositories**.
3. Paste this address and click **Add**, then **Close**:

   ```
   https://github.com/hybrid87sg/kiosk-addons
   ```

4. Refresh the page. Find **Kiosk Signage Server** in the store, open it and click **Install**.
5. Open the add-on's **Documentation** tab and follow the setup steps there.

Home Assistant downloads the add-on ready-made, so nothing is built on your
device. It works on Home Assistant OS and Supervised, on normal PCs (amd64) and
Raspberry Pi 4/5 (aarch64).

## Updates

When a new version is published, Home Assistant shows an **Update** button on the
add-on's page. Your settings, your key file and the add-on's data are kept.

## What's in here

Only the add-on's description and its documentation. The add-on itself is
downloaded from `ghcr.io/hybrid87sg/kiosk-server`. Your Google key and your
settings are never part of it: they stay on your own Home Assistant.

The files here are produced by the Kiosk Signage release process. Please don't
edit them by hand; they are replaced at the next release.
