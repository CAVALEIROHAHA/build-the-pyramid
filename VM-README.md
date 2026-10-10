# Build the Pyramid — VM protection

Based on Prometheus by Elias Oelschner, https://github.com/prometheus-lua/Prometheus

The build tool uses Prometheus pinned to commit `a4efc5f381c50ae2a111bdbb9272fa3203685be3`, with one local emitter fix: its sortable block array no longer receives unused entries indexed by random block IDs. Those entries could create holes or overwrite list positions for particular seeds. The patched file's hash is recorded in the build manifest. Its license is retained at `tools/prometheus/LICENSE`. This document accompanies the generated files and provides the required attribution; include it as a README when distributing or hosting this version.

## Files to upload

1. Upload `platoboost/BuildThePyramid.obfuscated.luau` over the existing `BuildThePyramid.obfuscated.luau` in your repository.
2. Upload `platoboost/Loader.obfuscated.luau` over the existing `Loader.luau`.
3. Upload this document as `VM-README.md`, or include its attribution line in the repository README.

The existing loadstring URL pointing to `Loader.luau` continues to work. The service configuration and access-marker format are unchanged; both newly generated files receive stronger obfuscation. The editable source remains in `BuildThePyramid.Hub.luau`.

Do not upload `settings.json`, `.gate-tag`, `Loader.configured.luau`, `Main.guarded.luau`, generated test files, or build intermediates. Those local files are not distribution artifacts.

## Protection

- The actual application and key-loader logic are transformed through two VM passes.
- String literals are encrypted. Constants are shuffled and encoded, and numeric expressions are randomized.
- Each build uses a new random seed. `protection-manifest.json` records output and input hashes and the pinned compiler version.
- This replaces the old packer that reconstructed the original source for `loadstring`. The generated VM still runs on the recipient's machine and can be analyzed; no specific cracking time or cost is guaranteed.
- Runtime checks that depend on debug hooks or analysis-tool detection are not inserted, to preserve executor compatibility.
- The existing key guard remains client-side; this build does not introduce server-side authorization or make an embedded secret inaccessible.

## Build and validation

Run `platoboost/build.ps1` to regenerate both files. It compiles candidates and executes the actual protected artifacts against mocked Roblox/provider environments before replacing the distribution files.

The tests cover the gate marker, GUI creation and callbacks, dropdowns, keybind capture, workouts across coroutine yields, Water of Nu discovery and priority, large-map scanning, model additions/removals, Get Key, invalid keys, nonce response verification, valid keys, and cleanup.

The water scanner indexes candidate models and follows Workspace descendant events instead of scanning the entire map every poll. Live executor testing is still required to measure device-specific performance and compatibility; mock timings are not live-game timings.

The first previous distribution files are kept under `platoboost/rollback/pre-vm`. To regenerate using the old packer, run `tools/build-platoboost.py --legacy` with Python.
