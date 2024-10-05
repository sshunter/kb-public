# Diary Extension

I wrote a quick diary extension for vscode that will open today's diary's
markdown file.

`Open Diary Entry` in the command pallette.

- Code currently lives at
  ~\Documentswork\vs-code-daily-diary-extension\daily-diary-extension on my windows pc
  but this will change when I clean it up.
- To install the extension from disk
  - `npx @vscode/vsce package`
  - In VSCode you can install from command palette with "Extensions: Install from VSIX..."

## Future enhancements

- check things in to github
- a command to open yesterday's diary entry, since my workflow in the morning is
to just roll everything in progress into the new file and I want to keep doing
that manually.
- a command to open the diary's parent directory
- a configuration option for the diary's parent directory IF I PUBLISH
- a configuration option for the diary's file name format IF I PUBLISH
- create a real publisher for package.json IF I PUBLISH or if my fake one starts causing problems.

## References

<https://code.visualstudio.com/api/working-with-extensions/publishing-extension#usage>
