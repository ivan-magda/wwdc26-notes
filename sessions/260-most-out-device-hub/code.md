# Code samples — Session 260

_No Code tab for this session._ Device Hub is a GUI app, so the talk has no Swift
snippets. The only concrete, scriptable interface named is the `devicectl` CLI.

## Spoken APIs / commands

`devicectl` is described as a command-line tool built on the same underlying
technology as Device Hub. The transcript names these capabilities (15:52–16:25);
exact flag spellings are illustrative, not quoted from a slide — verify against the
`devicectl` documentation.

```bash
# List your devices
xcrun devicectl list devices

# Install an app onto a device
xcrun devicectl device install app --device <id> /path/to/MyApp.app

# Change settings, e.g. switch between dark and light modes
xcrun devicectl device configure --device <id> ...

# Get more information about a device
xcrun devicectl device info details --device <id>

# Structured output for scripts / CI workflows
xcrun devicectl list devices --json-output devices.json
```

Capabilities the transcript attributes to `devicectl`:

- Listing devices
- Installing apps
- Capturing diagnostics
- Changing settings (e.g. dark/light appearance)
- Querying device information
- `--json-output` for structured output to integrate into scripts and CI
