# ButtonDeej CH340 serial read fix — community test build

Unofficial Windows build of the ButtonDeej-compatible [Miodec/deej](https://github.com/Miodec/deej) fork. Prepared to test a CH340 serial-read compatibility fix; this is not an official deej release.

## Change

Windows serial connections now request `MinimumReadSize=1` instead of `0`. The serial library, port baud/data settings, line protocol, slider mapping, and button/macro event handling remain unchanged. Source: `source/pkg/deej/serial.go`; regression coverage: `source/pkg/deej/serial_test.go`.

## Test evidence and limits

Built from revision `3aa3cd85c8205b6f4e72dfc7c09daeee84c265eb` with Go 1.20.14. On the affected user's PC with CH340 driver 3.9.2024.09, the test build opened COM3 at 9600, detected four sliders and three buttons, and emitted slider and configured button-key events. `go test ./pkg/deej` passed. This confirms the fix on that setup; it is not a guarantee for every device, firmware, Windows version, or ButtonDeej fork.

## Try it

1. Extract the package to a writable folder.
2. Copy `config.example.yaml` to `config.yaml` beside `AudioCtrl-Fixed.exe`.
3. Edit the COM port, slider-to-app mappings, and button key mappings for your setup. The example contains no personal mappings.
4. Close Arduino Serial Monitor and other serial terminals, then run `AudioCtrl-Fixed.exe`.
5. If troubleshooting, inspect `logs/deej-latest-run.log` beside the executable.

Keep your old application and config as a backup during testing.

## Build and test from source

From `source` with Go installed:

```powershell
go test ./pkg/deej
go build -o AudioCtrl-Fixed.exe -ldflags "-H=windowsgui -s -w -X main.gitCommit=local-minread1 -X main.versionTag=serial-fix -X main.buildType=release" ./pkg/deej/cmd
```

The source snapshot contains the upstream MIT `LICENSE`, Go module files, project docs and firmware resources. `hardware/PCAudioButtonsV2.ino` is the firmware supplied for this investigation.

## Attribution

Based on [Miodec/deej](https://github.com/Miodec/deej), a fork of [omriharel/deej](https://github.com/omriharel/deej). The upstream MIT license is included at `source/LICENSE`; third-party names and marks belong to their owners.
