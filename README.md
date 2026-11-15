<p align="center">
  <img src="patches-bundle.png" alt="XMount" width="128">
</p>

<h1 align="center">XMount</h1>

<p align="center">
  <a href="https://morphe.software/add-source?github=ahmetxa/XMount">
    <img src="assets/add-to-morphe.svg" alt="Add to Morphe" height="48">
  </a>
</p>

XMount is a [Morphe](https://github.com/MorpheApp) patch bundle for the new X (Twitter) Android app.
It is built from [ahmetxa/piko `x-lite`](https://github.com/ahmetxa/piko/tree/x-lite), a fork of
the [Piko](https://github.com/crimera/piko) `x-lite` branch by [crimera](https://github.com/crimera).
Every patch comes from Piko; XMount adds root mount support on top and publishes the result.

## Differences from Piko

| | Piko | XMount |
| --- | --- | --- |
| Root **mount** install | Nearly every NewX patch is unavailable | Every patch except **NewX: Clone** can be selected |
| Piko settings on a mount | Not reachable | Open normally |
| Downloader folder picker and notification actions on a mount | Not reachable | Work |
| Crash log copy action on a mount | Not reachable | Works |

### Root mount support

A root mount install bind-mounts the patched APK over the installed one, but Android keeps using the
**stock manifest**. The activities and receivers that patches add to the manifest (the Piko settings
screen, the downloader folder picker and its Retry/Cancel/Delete notification receivers, and the crash
log copy receiver) are therefore unknown to the system and cannot be started. This is why Piko marks
those patches unavailable on a mount.

XMount reaches these components through a registered stock X component instead:

- When the extension code starts one of its own components, the intent is redirected to a stock X
  activity or receiver that is not exported, enabled, runs in the main process and needs no
  permission. The real target is recorded on the intent.
- X's `AppComponentFactory` (`androidx.core.app.CoreComponentFactory`) is patched so that, for exactly
  that intent, the process creates the real extension class in place of the stock one. Recent X builds
  ship the factory without these callbacks, so the patch adds them when they are missing.
- Only the selected non-exported stock component of the matching kind is swapped, so other apps cannot
  use an exported X component to start an extension class.

**NewX: Clone** stays unavailable on a mount, because a mount cannot change the package name.

### Known limitations

- On a mount, extension screens run under the stock component's manifest entry, so that entry's theme,
  `configChanges` and keyboard settings apply to them.
- If a future X build has no usable `AppComponentFactory`, patching stops with an error instead of
  producing an app whose Piko settings do not open.

## Credits

- [crimera/piko](https://github.com/crimera/piko) - the Piko patches XMount is built from
- [crimera/piko-newx](https://github.com/crimera/piko-newx) - the build and release pipeline this
  repository is forked from
- [Morphe](https://github.com/MorpheApp) - patcher
- [ReVanced](https://github.com/ReVanced) - previous patcher
- [j-hc](https://github.com/j-hc) - the project is inspired by j-hc's revanced builder template
