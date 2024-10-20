# MIFARE Classic
Here are the steps to follow in order to read your cards. Your goal is to find as many keys as possible. The keys unlock sections of your card for the Flipper to read them - you must have a card. Once you read enough sections, you can use an emulated or cloned card at the original card reader to unlock it (sometimes even without finding all of the keys!).

> [!IMPORTANT]
> The features described in this guide are not yet officially released. Please select your firmware below for the relevant preparation instructions:

<table><tr><td><details>
<summary> <a href="#"><img src="https://cdn.flipperzero.one/favicon-16x16.png"/></a>&nbsp; Official firmware (OFW)</summary><br>

Flash [this experimental build](https://github.com/noproto/xero-firmware/releases/download/1.1.0-rc-xero/flipper-z-f7-update-local.tgz) to follow this guide. When you complete all of the steps, restore the latest official firmware on the Flipper Zero. You may skip this step when this status badge is purple with the text "merged": [![Status](https://img.shields.io/github/pulls/detail/state/flipperdevices/flipperzero-firmware/3822?label=Status&style=flat-square)](https://github.com/flipperdevices/flipperzero-firmware/pull/3822)

</details></td></tr></table>

<table><tr><td><details>
<summary>Custom firmware (CFW)</summary><br>
<table><tr><td><details>
<summary> <a href="#"><img src="https://flipperunleashed.com/images/favicon-16x16.png"/></a>&nbsp; Unleashed</summary><br>

A version incorporating all changes has not yet been released. In the meantime, try the [latest dev build (1064)](https://unleashedflip.com/fw/dev/flipper-z-f7-update-1064.tgz).

</details></td></tr></table>
<table><tr><td><details>
<summary> <a href="#"><img src="https://momentum-fw.dev/favicon-16x16.png"/></a>&nbsp; Momentum</summary><br>

Update to [mntm-dev-2e3c6d32](https://up.momentum-fw.dev/firmware/dev/flipper-z-f7-update-mntm-dev-2e3c6d32.tgz) (released on Oct 18) or later.

</details></td></tr></table>
<table><tr><td><details>
<summary> <a href="#"><img src="https://i.imgur.com/Of1QdI1.png"/></a>&nbsp; RogueMaster</summary><br>

Update to [RM1019-1748-0.420.0-81df18a](https://github.com/RogueMaster/flipperzero-firmware-wPlugins/releases/tag/RM1019-1748-0.420.0-81df18a) (released to subscribers on Oct 18, public on Oct 19) or later.

</details></td></tr></table>
<table><tr><td><details>
<summary> <a href="#"><img src="https://i.imgur.com/vUvGNJ7.png"/></a>&nbsp; Xero</summary><br>

Update to [1.1.0-rc-xero](https://github.com/noproto/xero-firmware/releases/tag/1.1.0-rc-xero) (released on Oct 18) or later.

</details></td></tr></table>
</details></td></tr></table>

## Reading the card

Steps:

1. **Read from NFC app**: Try to scan your MIFARE Classic card with NFC -> Read. It will try a dictionary (and KDF) attack of default keys to unlock your card, as well as any keys you may have found through other methods. If it finds 32/32 keys (or 80/80) with 16/16 sectors (or 40/40), congratulations and [proceed to "Emulation"](mifareclassic.md#emulation). If not, wait for nonce collection to complete (be patient!) and continue to step 2.
2. **Crack with MFKey app**: Navigate to Main Menu -> Applications -> NFC -> MFKey, press OK to run. Do not interrupt the cracking process, it may take a while! When the cracking process is complete, the number of new user keys (or candidate keys) that are found will be shown. If more than zero keys are found, return to step 1 and repeat the process.

## Troubleshooting

Q: **I have zero keys, how do I find more?**

A: You can find more keys from the card reader (the same one you would normally tap your card against) using the Mfkey32 attack. Navigate to NFC -> Extract MF Keys and hold the Flipper Zero up to the reader. If the reader unlocks during this process, it is using the UID of the card and is highly insecure. Otherwise, it'll collect "nonces" from the reader. You can crack the nonces to find the reader keys by running MFKey following Extract MF Keys (navigate to Main Menu -> Applications -> NFC -> MFKey, press OK to run).

When the cracking process is complete, the number of new user keys (or candidate keys) that are found will be shown. If more than zero keys are found, return to step 1 of [Reading the card](#reading-the-card) and repeat the process.

Q: **When I read the card in the NFC app, it says "(Hard)" at the top. How do I find new keys?**

A: You need to perform a Hardnested attack using the nonces your Flipper Zero saved when reading the card. Follow the guide to run HardnestedRecovery [here](https://github.com/noproto/HardnestedRecovery#usage).

## Emulation

![Emulation](https://gist.githubusercontent.com/noproto/2ee35c1916b358924c08f77645a46d81/raw/c4f19920a2ccbe4c66998476a3d4d76c2f98ad78/emulation.png "Emulation")

Go to NFC -> Saved -> (The name you assigned your card) -> Emulate and hold it up to the reader. Try various angles to find the correct positioning.

Even if you have all of the keys and try to emulate your card, you may find the reader still does not accept the emulated card (<https://flp.wiki/nfc/mfc/emulation/>). This is often a timing issue (slow emulation on the Flipper Zero). You have three options:

* Update your Flipper Zero to the latest firmware (0.94.0 or above). Flipper Devices rewrote the NFC stack, which improved MIFARE Classic dictionary attacks and emulation.
* Purchase a special kind of MIFARE Classic card called a magic card to clone the data onto a physical card. Magic cards are more likely to be recognized by the card reader, however some readers may be programmed to detect magic cards. To find shops to purchase magic cards from, search for the magic card you need (ensure the listing mentions the term "UID changeable"):
    - 4 byte UID (e.g. `AA BB CC DD`): Gen1a Magic Card (💲), Gen2 Magic Card - 4 byte UID variant (💲💲)
    - 7 byte UID (e.g. `AA BB CC DD EE FF 00`): Gen2 Magic Card - 7 byte UID variant (💲💲), Gen4/Ultimate Magic Card (💲💲💲)

    Follow the official guide to write the card data to a magic card: (<https://docs.flipper.net/nfc/magic-cards>).
* If you have a spare identical MIFARE Classic card (1K for 1K, 4K for 4K, EV1 for EV1, etc.), have all of the keys to the spare card, and the access conditions on the spare card allow: you can duplicate the data from the initial card to the spare card and it could possibly work (if the reader is indifferent to the UID of the card, and if the keys are *diversified* - you will need the diversified keys from the reader using Mfkey32/KDF provided they are not already present on the card). ⚠️ IMPORTANT: Save the data stored on the spare card before overwriting it, otherwise it will be irrecoverably erased. This is less reliable than using a magic card, but an option if you are unable to obtain a magic card.
