# Chromium

Chromium is an open-source browser project that aims to build a safer, faster,
and more stable way for all users to experience the web.

The project's web site is https://www.chromium.org.

Documentation in the source is rooted in [docs/README.md](docs/README.md).

Learn how to [Get Around the Chromium Source Code Directory Structure
](https://www.chromium.org/developers/how-tos/getting-around-the-chrome-source-code).

For historical reasons, there are some small top level directories. Now the
guidance is that new top level directories are for product (e.g. Chrome,
Android WebView, Ash). Even if these products have multiple executables, the
code should be in subdirectories of the product.

# Trident

## Chromium Version

```
Chromium=87.0.4280.141
V8=8.7.220.31
```
## Build Instructions

1. Fetch the Chromium code by following the [instruction](https://www.chromium.org/developers/how-tos/get-the-code).
2. `cd path/to/chromium/src` as your working directory.
3. Checkout to the correct version by `git checkout tags/87.0.4280.141` and run `gclient sync -D` afterwards.
4. Generate the compilation configuration files by running `gn gen out/Default`. Add compiling args to `out/Default/args.gn`. For a quick build, you can add the following args.
```
is_debug = false
is_component_build = false
enable_nacl = false
symbol_level = 0
```
5. Stay in `path/to/chromium/src` and run `git apply path/to/trident.patch`.
6. Now you can build the binary by running `autoninja -C out/Default chrome`.

**It's important that every time you need to run `gclient sync -D` in `tags/87.0.4280.141` when you switch to other Chromium tags**

## Misc

By default the logs of the instrumentation hooks are written into a file of `/tmp/forensic-default.log`.

You can change it by setting `--forensic-log-file=/absolute/path/to/file`. 

For example, you can run this command for a quick function check.
```bash
chromium/src/out/[Debug|Release|Default]/chrome --forensic-log-file=/tmp/google.log --headless --no-sandbox --disable-gpu --disable-dev-shm-usage https://google.com
```

There are multiple ways to log the events for later use (i.e., graph construction, feature extraction discussed in the paper).
You can change the logging function `ForensicRecorder::log` in `third_party/blink/renderer/core/inspector/forensic/forensic_recorder.cc` to implement whatever you want.

The `SEAgent` is a Chrome DevTools domain as defined in `third_party/blink/public/devtools_protocol/browser_protocol.pdl`


## Customization

All the instrumentation hooks are implemented with the internal probes as defined in `third_party/blink/renderer/core/probe/core_probes.json5`. We took advantages of the existing ones and added some more as needed for our problem modeling and graph construction as described in the paper. All the probes, `DidAddEventListener` for example, are defined in `third_party/blink/renderer/core/probe/core_probes.pidl`.

## Note

To collect data at scale, you may want to build your own crawler using `Puppeteer`. We will not release our crawler as it is a customized and currently being activately maintain for multiple projects.

## Paper Bibtex
```
@inproceedings{yang2023trident,
  title={TRIDENT: Towards Detecting and Mitigating Web-based Social Engineering Attacks},
  author={Yang, Zheng and Allen, Joey and Landen, Matthew and Perdisci, Roberto and Lee, Wenke},
  booktitle={32st USENIX Security Symposium, USENIX Security},
  volume={2023},
  pages={1681--1698},
  year={2023}
}
```
