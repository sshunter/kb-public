# Microphone Setup

## Voicemeeter Potato Install

**Note:** I have used up all my license regens at this point. I'll need to
repurchase when I get a new PC (after inevitably trying newer options).

1. Download here: <https://vb-audio.com/Voicemeeter/potato.htm> and install.
2. Make sure you reboot before proceeding. If not, you may not be presented with
   the registration dialog and you may find the only registration dialog is for
   additional plugin that you don't have a license to. And trying to generated a
   response code for this challenge will burn a key regen.
3. After rebooting settles down, launch Voicemeeter.
4. Choose "About Box/License" in the menu.
5. Go to <https://shop.vb-audio.com/en/module/vblicensing/myLicenses> and login.
   If the Challenge in Voicemeeter doesn't match the website, you will need to
   regen a response.
6. When the challenges match, copy the Response from the webpage into the dialog
   and apply. It should show as ACTIVATED.

Voicemeeter Potato is now installed.

## audiodg.exe Priority and CPU Affinity

See [Permanently Fix Crackling Audio on
Windows!](https://www.youtube.com/watch?v=Gb-PsrIVChY) which will describe the
problem we are addressing and these steps to fix it.

1. Download Process Lasso here: <https://bitsum.com/download-process-lasso/> and install.
2. I have a lifetime license, but you can use for free (with nag screens) until
   you dig it out.
3. Find `audiodg.exe` in the process list
4. Set CPU Priority -> Always -> High.
5. Choose CPU Affinity -> Always -> Set CPU Affinity and in the dialog clear the
   selection and choose one core (Don't use 0 because youtuber doesn't like it.
   Most likely real reason would be some old software just wants core 0 or
   something)
6. Process Lasso should start on boot. If not, figure that out and update these notes.

## Voicemeeter Potato Setup

See [How-To Setup Voicemeeter Banana & Potato Correctly !!](https://www.youtube.com/watch?v=52Rs7tqFYm8)

**TODO** Re-run thru this and document things. Mostly frustrated at this point
because labels/names changed.

## LATER

- [Light Host VST host with
  Voicemeeter](https://www.youtube.com/watch?v=AyzQZScP2Sw) instead of Equalizer
  APO. Also has links to VST plugins
- [Voicemeeter Update](https://www.youtube.com/watch?v=YxWE-enkOhY) Does this
  mean I don't need VSTs at all, for my use cases?
  [Voicemeeter Setup](https://www.youtube.com/watch?v=52Rs7tqFYm8) Note that I
  am intending to route the mic through voicemeeter. Its pretty much the only
  thing I want to do. I have gone thru this but not added notes. I was pretty
  frustrated at the time.
