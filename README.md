# `insan3d.tgsmtp`

An Ansible role that sets up `tgsmtp` as a `sendmail`-style wrapper. `tgsmtp` is a simple script that parses standard input as an email and sends the message to a Telegram group.

The installed wrapper is root-only by default and is intended for root-owned jobs such as cron or system scripts. It installs beside the system MTA by default; replacing `/usr/sbin/sendmail` requires an explicit opt-in. The `tgsmtp` script is downloaded from a pinned release tag and verified by checksum.

## Requirements

- Ansible >= 2.20
- Python >= 3.11 on managed hosts
- A Telegram bot token (obtain from [@BotFather](https://t.me/botfather))
- A Telegram chat ID (can be a group or private chat)

## Role Variables

### Required Variables

| Variable | Type | Description |
|----------|------|-------------|
| `tgsmtp_telegram_token` | string | Telegram bot token |
| `tgsmtp_telegram_chat_id` | string | Telegram chat ID (group or private) |

### Optional Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `tgsmtp_format` | `pre` | Telegram markup parsing mode. Options: `pre`, `html`, `markdownv2` |
| `tgsmtp_install_path` | `/usr/local/bin/tgsmtp` | Path to install the `tgsmtp` script |
| `tgsmtp_alias_path` | `/usr/local/sbin/tgsmtp-sendmail` | Path to install the `sendmail`-style wrapper |
| `tgsmtp_alias_unicode` | `false` | Whether to parse Unicode escapes in input |
| `tgsmtp_alias_backup` | `true` | Whether to back up an existing wrapper before replacing it |
| `tgsmtp_replace_system_sendmail` | `false` | Whether to allow replacing `/usr/sbin/sendmail` |
| `tgsmtp_script_version` | `v1.0.1` | `tgsmtp` release tag used by the default download URL |
| `tgsmtp_script_url` | tag URL | URL to download the `tgsmtp` script from |
| `tgsmtp_script_hash` | sha512 | Checksum for the downloaded `tgsmtp` script |
| `tgsmtp_python_executable` | `/usr/bin/python3` | Python executable to use |
| `tgsmtp_log_path` | `/var/log/tgsmtp.log` | Path to the log file |
| `tgsmtp_logrotate_install` | `true` | Whether to install `logrotate` configuration |
| `tgsmtp_verbose` | `false` | Whether to run `tgsmtp` with verbose logging |

## Dependencies

None.

## Example Playbook

```yaml
- hosts: servers
  roles:
    - role: insan3d.tgsmtp
      vars:
        tgsmtp_telegram_token: "your_bot_token_here"
        tgsmtp_telegram_chat_id: "your_chat_id_here"
        tgsmtp_format: "html"  # optional
        tgsmtp_logrotate_install: false  # optional, disable logrotate if desired
```

## Usage

After applying this role, root can use the installed wrapper to send emails that will be forwarded to your Telegram chat:

```bash
echo "Subject: Test Message\n\nThis is a test message." | /usr/local/sbin/tgsmtp-sendmail -t
```

To deliberately replace the system sendmail interface, set both:

```yaml
tgsmtp_alias_path: /usr/sbin/sendmail
tgsmtp_replace_system_sendmail: true
```

The script logs to `/var/log/tgsmtp.log` (configurable via `tgsmtp_log_path`). By default, the log is root-only, log rotation is configured to rotate weekly, keep 4 rotations, and compress old logs. This can be disabled by setting `tgsmtp_logrotate_install` to `false`.

## Development Checks

The role uses a lightweight Molecule scenario with the default driver and local Ansible connection. Docker is not required. The scenario installs into `/tmp/tgsmtp-molecule` and uses `sudo`, because the role manages root-owned files.

```bash
uv run --group dev ansible-lint .
uv run --group dev molecule test
```

By default Molecule installs the sibling checkout at `../tgsmtp/tgsmtp.py`. Override `TGSMTP_SCRIPT_URL` to test another source. Live Telegram smoke is opt-in:

```bash
TGSMTP_TEST_TOKEN=... TGSMTP_TEST_CHAT_ID=... TGSMTP_LIVE=1 uv run --group dev molecule converge
TGSMTP_TEST_TOKEN=... TGSMTP_TEST_CHAT_ID=... TGSMTP_LIVE=1 uv run --group dev molecule verify
uv run --group dev molecule cleanup
```

## License

MIT

## Author Information

- **Author**: Alexander Pozlevich
- **Email**: apozlevich@gmail.com

## Links

- [Role Repository](https://github.com/insan3d/insan3d.tgsmtp)
- [tgsmtp Script Repository](https://github.com/insan3d/tgsmtp)
