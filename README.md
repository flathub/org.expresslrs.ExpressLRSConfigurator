# ExpressLRS Configurator Flatpak

## Building

```
flatpak run org.flatpak.Builder build-dir --user --ccache --force-clean --install org.expresslrs.ExpressLRSConfigurator.yml
```

Then you can run it via the command line:

```
flatpak run org.expresslrs.ExpressLRSConfigurator
```

or just search for the installed app on your system

## Advisory app checks

The `smoke-test` option in `flathub.json` opts into Vorarbeiter's advisory checks
of the existing x86_64 build. It verifies startup and uses `ci/screenshots.yml` to
capture the home and configurator screens. The recipe selects Wayland explicitly
and does not require a connected radio or flash firmware.

Once Vorarbeiter's smoke-test integration is deployed, PRs receive a bot comment
with the results, screenshot previews, and a download link for diagnostics.
Failures do not block publication. Artifacts and previews are retained for 14 days.

To reproduce screenshot capture locally, export the app into `repo`, then run the
recipe:

```sh
flatpak run org.flatpak.Builder --user --force-clean --install-deps-from=flathub \
  --repo=repo --default-branch=test build-dir org.expresslrs.ExpressLRSConfigurator.yml

docker run --rm --privileged --tmpfs /run --tmpfs /dev/dri \
  -v "$PWD:/workspace" -w /workspace \
  ghcr.io/razzeee/flatpak-smoke-screenshots:v0.1.0 sh -c '
    install -d -o 65534 -g 65534 artifacts/capture
    exec setpriv --reuid=65534 --regid=65534 --clear-groups \
      flatpak-smoke screenshot-repo repo \
      app/org.expresslrs.ExpressLRSConfigurator/x86_64/test \
      --recipe ci/screenshots.yml --output artifacts/capture --allow-network-remotes
  '
```

Use the branch exported by your local build in place of `test`. Review captures
before using them as app-listing screenshots.
