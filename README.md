# TextPLC Firmware

Controller firmware for [TextPLC](https://github.com/textplc) — an open-source industrial PLC where programs are plain text files on SD cards.

Designed for ARM Cortex-M class microcontrollers with hardware abstraction for portability across target platforms.

## What this does

Implements:

- **Lua interpreter** with PLC standard library (`DI[]`, `DO[]`, `AI[]`, `AO[]`, `Timer`, retained variables)
- **Deterministic cyclic execution** on isolated processor core with bounded watchdog
- **SD card program loading** — program is a plain text file
- **Optional Ed25519 signature verification** at boot
- **MODBUS RTU and TCP** master/slave
- **CAN bus** support (planned)

## Status

🚧 **Pre-release.** API will change. Public launch coming.

## Architecture

The firmware is structured for portability:

- **Hardware Abstraction Layer (HAL)** — isolates target-specific code from core logic
- **Deterministic core** — Lua interpreter + scan cycle runs on isolated processor context
- **Standards-compliant communication** — MODBUS, CAN with abstraction over physical layer

Specific reference hardware platform under evaluation. See [target evaluation](docs/target-evaluation.md) for current status.

## How a TextPLC program looks

\`\`\`lua
-- conveyor.lua — start/stop with safety timeout

config = {
    scan_time_ms = 10,
    retained = {"V.run_cycles"}
}

function main()
    if DI[0] and not DI[1] then     -- start, not stop
        DO[0] = true                 -- run motor
        T0:start(10000)              -- 10 second timeout
    end
    
    if DI[2] or T0:done() then      -- e-stop or timeout
        DO[0] = false
        T0:reset()
        V.run_cycles = V.run_cycles + 1
    end
end
\`\`\`

Save as `program.lua` on an SD card, insert into controller, power on. No IDE, no licensing, no proprietary software required.

## Quick start

Coming soon.

## Documentation

Coming soon — see [textplc.com](https://textplc.com).

## Contributing

See org-wide [CONTRIBUTING.md](https://github.com/textplc/.github/blob/main/CONTRIBUTING.md).

## Security

For security vulnerabilities, see [SECURITY.md](https://github.com/textplc/.github/blob/main/SECURITY.md). **Do not file public issues for security vulnerabilities.**

## License

Apache License 2.0. See [LICENSE](LICENSE).
