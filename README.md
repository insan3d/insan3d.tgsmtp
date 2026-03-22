# `insan3d.tgsmtp`

An Ansible role that sets up `tgsmtp` as a `sendmail` alias. `tgsmtp` is a simple script that parses standard input as an email and sends the message to a Telegram group.

The installed wrapper is root-only by default and is intended for root-owned jobs such as cron or system scripts.

## Requirements

- Ansible >= 2.20
- A Telegram bot token (obtain from [@BotFather](https://t.me/botfather))
- A Telegram chat ID (can be a group or private chat)

## Role Variables

### Required Variables

| Variable | Type | Description |
|----------|------|-------------|
| `tgsmtp_telegtam_token` | string | Telegram bot token |
| `tgsmtp_telegtam_chat_id` | string | Telegram chat ID (group or private) |

### Optional Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `tgsmtp_format` | `pre` | Telegram markup parsing mode. Options: `pre`, `html`, `markdownv2` |
| `tgsmtp_install_path` | `/usr/local/bin/tgsmtp` | Path to install the `tgsmtp` script |
| `tgsmtp_alias_path` | `/usr/sbin/sendmail` | Path to install the `sendmail` alias script |
| `tgsmtp_alias_unicode` | `false` | Whether to parse Unicode escapes in input |
| `tgsmtp_python_executable` | `/usr/bin/python3` | Python executable to use |
| `tgsmtp_log_path` | `/var/log/tgsmtp.log` | Path to the log file |
| `tgsmtp_logrotate_install` | `true` | Whether to install `logrotate` configuration |

## Dependencies

None.

## Example Playbook

```yaml
- hosts: servers
  roles:
    - role: insan3d.tgsmtp
      vars:
        tgsmtp_telegtam_token: "your_bot_token_here"
        tgsmtp_telegtam_chat_id: "your_chat_id_here"
        tgsmtp_format: "html"  # optional
        tgsmtp_logrotate_install: false  # optional, disable logrotate if desired
```

## Usage

After applying this role, root can use `sendmail` to send emails that will be forwarded to your Telegram chat:

```bash
echo "Subject: Test Message\n\nThis is a test message." | sendmail -t
```

The script logs to `/var/log/tgsmtp.log` (configurable via `tgsmtp_log_path`). By default, log rotation is configured to rotate weekly, keep 4 rotations, and compress old logs. This can be disabled by setting `tgsmtp_logrotate_install` to `false`.

## License

MIT

## Author Information

- **Author**: Alexander Pozlevich
- **Email**: apozlevich@gmail.com

## Links

- [Role Repository](https://github.com/insan3d/insan3d.tgsmtp)
- [tgsmtp Script Repository](https://github.com/insan3d/tgsmtp)
