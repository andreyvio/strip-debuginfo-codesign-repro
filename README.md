# Repro

The change that started passing "strip=debuginfo" to the linker:
https://github.com/rust-lang/cargo/pull/13257

## nightly-2024-01-17 
```
$ cargo clean && cargo +nightly-2024-01-17 build --verbose
     Removed 20 files, 717.4KiB total
   Compiling repro v0.1.0 (/Users/andrey/repro)
     Running `/Users/andrey/.rustup/toolchains/nightly-2024-01-17-aarch64-apple-darwin/bin/rustc --crate-name build_script_build --edition=2021 build.rs --error-format=json --json=diagnostic-rendered-ansi,artifacts,future-incompat --diagnostic-width=245 --crate-type bin --emit=dep-info,link -C embed-bitcode=no -C metadata=1f72d3f9199ec0db -C extra-filename=-1f72d3f9199ec0db --out-dir /Users/andrey/repro/target/debug/build/repro-1f72d3f9199ec0db -C incremental=/Users/andrey/repro/target/debug/incremental -L dependency=/Users/andrey/repro/target/debug/deps`
     Running `/Users/andrey/repro/target/debug/build/repro-1f72d3f9199ec0db/build-script-build`
     Running `/Users/andrey/.rustup/toolchains/nightly-2024-01-17-aarch64-apple-darwin/bin/rustc --crate-name repro --edition=2021 src/main.rs --error-format=json --json=diagnostic-rendered-ansi,artifacts,future-incompat --diagnostic-width=245 --crate-type bin --emit=dep-info,link -C embed-bitcode=no -C metadata=ee8efba2fd560940 -C extra-filename=-ee8efba2fd560940 --out-dir /Users/andrey/repro/target/debug/deps -C incremental=/Users/andrey/repro/target/debug/incremental -L dependency=/Users/andrey/repro/target/debug/deps`
    Finished dev [unoptimized] target(s) in 0.30s
$ codesign -dv --verbose=4 $(find . -name 'build-script-build')
Executable=/Users/andrey/repro/target/debug/build/repro-1f72d3f9199ec0db/build-script-build
Identifier=build_script_build-1f72d3f9199ec0db
Format=Mach-O thin (arm64)
CodeDirectory v=20400 size=3228 flags=0x20002(adhoc,linker-signed) hashes=97+0 location=embedded
VersionPlatform=1
VersionMin=917504
VersionSDK=918016
Hash type=sha256 size=32
CandidateCDHash sha256=d20690c3de6fc711c50ab7238da9712d4aa50f1c
CandidateCDHashFull sha256=d20690c3de6fc711c50ab7238da9712d4aa50f1cc295fec98fa88c38ff354137
Hash choices=sha256
CMSDigest=d20690c3de6fc711c50ab7238da9712d4aa50f1cc295fec98fa88c38ff354137
CMSDigestType=2
Executable Segment base=0
Executable Segment limit=205944
Executable Segment flags=0x1
Page size=4096
CDHash=d20690c3de6fc711c50ab7238da9712d4aa50f1c
Signature=adhoc
Info.plist=not bound
TeamIdentifier=not set
Sealed Resources=none
Internal requirements=none
```

## nightly-2024-01-18
```
$ cargo clean && cargo +nightly-2024-01-18 build --verbose
     Removed 43 files, 1.7MiB total
   Compiling repro v0.1.0 (/Users/andrey/repro)
     Running `/Users/andrey/.rustup/toolchains/nightly-2024-01-18-aarch64-apple-darwin/bin/rustc --crate-name build_script_build --edition=2021 build.rs --error-format=json --json=diagnostic-rendered-ansi,artifacts,future-incompat --diagnostic-width=245 --crate-type bin --emit=dep-info,link -C embed-bitcode=no -C metadata=9775281f9409828e -C extra-filename=-9775281f9409828e --out-dir /Users/andrey/repro/target/debug/build/repro-9775281f9409828e -C incremental=/Users/andrey/repro/target/debug/incremental -C strip=debuginfo -L dependency=/Users/andrey/repro/target/debug/deps`
     Running `/Users/andrey/repro/target/debug/build/repro-9775281f9409828e/build-script-build`
error: failed to run custom build command for `repro v0.1.0 (/Users/andrey/repro)`

Caused by:
  process didn't exit successfully: `/Users/andrey/repro/target/debug/build/repro-9775281f9409828e/build-script-build` (signal: 9, SIGKILL: kill)
$ codesign -dv --verbose=4 $(find . -name 'build-script-build')
./target/debug/build/repro-9775281f9409828e/build-script-build: code object is not signed at all
```

Note the `-C strip=debuginfo` when compiling the build script.
