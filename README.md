# reliquary-archiver (macOS Fork)

_A macOS-compatible fork of [reliquary-archiver](https://github.com/IceDynamix/reliquary-archiver) — tool to create a relic export from network packets of a certain turn-based anime game._

> **This fork exists to provide macOS support**, specifically for players running Honkai Star Rail via [PlayCover](https://playcover.io/) on Apple Silicon Macs. The original project only ships Windows and Linux binaries.

json output format is based on the format of [HSR-Scanner](https://github.com/kel-z/HSR-Scanner)

made to be used with [fribbels hsr optimizer](https://github.com/fribbels/hsr-optimizer)

## What changed from the original?

**No code changes were necessary.** The original codebase already uses Rust's `#[cfg]` conditional compilation to gate all Windows-specific code (GUI, auto-update, pktmon, admin escalation). The default features (`pcap` + `stream`) compile and run natively on macOS.

This fork primarily serves as:
- A macOS build target and documentation hub
- A place to track macOS-specific issues if any arise

## macOS Setup (PlayCover)

### Prerequisites
- **Rust toolchain** — Install via [rustup](https://rustup.rs/) if not already installed
- **libpcap** — Pre-installed on macOS (no action needed)
- **PlayCover** — With Honkai Star Rail installed

### Build from source
```bash
git clone https://github.com/Siruyy/reliquary-archiver-for-mac.git
cd reliquary-archiver-for-mac
cargo build --release
```

> **Note:** The first build takes several minutes as it downloads game resource files and compiles all dependencies.

### Usage

#### One-time capture (exports a JSON file)
1. Open PlayCover and launch Honkai Star Rail
2. Get to the **"Click to Start"** screen — **do not enter the game yet**
3. Open Terminal and run:
   ```bash
   sudo ./target/release/reliquary-archiver
   ```
4. Wait until it says **"listening with a timeout"**
5. Click Start in the game
6. The archiver outputs a JSON file (e.g., `archive_output-2026-02-24T14-00-00.json`)
7. Import that JSON into [Fribbels HSR Optimizer](https://fribbels.github.io/hsr-optimizer)

#### Live Import (real-time streaming to optimizer)
1. Run the archiver with the stream flag:
   ```bash
   sudo ./target/release/reliquary-archiver -s
   ```
2. In the HSR Optimizer, enable **Live Import** and connect to `ws://127.0.0.1:23313/ws`
3. Launch the game — relics and light cones will stream in live
4. Press `Ctrl+C` in the terminal to stop

> **Important:** You must use `sudo` because macOS restricts raw packet capture to root/admin users.

### cli usage

```
Usage: reliquary-archiver [OPTIONS] [OUTPUT]

Arguments:
  [OUTPUT]  Path to output .json file to, per default: archive_output-%Y-%m-%dT%H-%M-%S.json

Options:
      --timeout <TIMEOUT>        How long to wait in seconds until timeout is triggered for live captures [default: 120]
  -s, --stream                   Host a websocket server to stream relic/lc updates in real-time. This will disable the timeout
  -p, --websocket-port <PORT>    Port to listen on for the websocket server, [default: 23313]
  -v, --verbose...               How verbose the output should be, can be set up to 3 times. Has no effect if RUST_LOG is set
  -l, --log-path <LOG_PATH>      Path to output log to
  -e, --exit-after-capture       Don't wait for enter to be pressed after capturing
  -h, --help                     Print help

Pcap Only:
      --pcap <PCAP>              Read packets from .pcap file instead of capturing live packets
```

to customize logging, either

- set the verbose flags
- or set `RUST_LOG` env variable to customize logging,
  see [here](https://docs.rs/tracing-subscriber/latest/tracing_subscriber/filter/struct.EnvFilter.html#directives)

to output logs to a file, provide `--log-path <path>`. file logs will always be trace-level.

## Credits

All credit goes to [IceDynamix](https://github.com/IceDynamix) for the original [reliquary-archiver](https://github.com/IceDynamix/reliquary-archiver) and the [reliquary](https://github.com/IceDynamix/reliquary) packet parsing library it's built on.

## related projects

want to do more with packet parsing? check out the
[standalone library](https://github.com/IceDynamix/reliquary) the archiver is built on top off!

looking to export your achievements? check out [stardb-exporter](https://github.com/juliuskreutz/stardb-exporter)!
