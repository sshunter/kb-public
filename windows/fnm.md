# Fast Node Manager (fnm)

<https://github.com/Schniz/fnm7>

- Node version manager
- Honors .node-version file
- Supports windows, which is why we're here

## Install

- Follow their readme, I'm using winget and powershell so we'll do that
    - `winget install --id Schniz.fnm`
    - Add `fnm env --use-on-cd --shell powershell | Out-String |
      Invoke-Expression` to your profile ($profile is path in powershell window)

I haven't dont any customization beyond that.
