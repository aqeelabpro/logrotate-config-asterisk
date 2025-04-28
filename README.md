# 📦 Asterisk Logrotate Configuration

A standardized and production-ready **Logrotate** configuration for managing Asterisk PBX log files efficiently.

This setup ensures:
- Regular rotation of critical logs.
- Disk space optimization through compression.
- Proper permission handling to maintain security.
- Seamless Asterisk logging continuity after rotations.

---

## ✨ Features

- **Daily rotation** of Asterisk logs.
- **Retention** of the last 7 log archives.
- **Automatic compression** of rotated logs using gzip.
- **Secure file permissions** for all newly created logs.
- **Automatic Asterisk logger reload** post-rotation to prevent service disruption.
- **Minimal downtime** and **optimized storage** usage.

---

## 📂 Files Managed

This configuration targets the following logs typically found under `/var/log/asterisk/`:

- `full`
- `freepbx.log`
- `freepbx_security.log`
- `core-fastagi_out.log`
- `core-calltransfer-monitor_out.log`
- `ucp_err.log`
- `ucp_out.log`
- and other related `.log` files.

---

## 🔧 Example Configuration

```bash
/var/log/asterisk/*.log {
    daily
    rotate 7
    compress
    delaycompress
    missingok
    notifempty
    sharedscripts
    create 0640 asterisk asterisk
    su asterisk asterisk

    postrotate
        if [ -f /var/run/asterisk/asterisk.pid ]; then
            /usr/sbin/asterisk -rx 'logger reload' > /dev/null 2>/dev/null || true
        fi
    endscript
}
```

---

## ⚙️ Key Configuration Options

| Option            | Description |
|-------------------|-------------|
| `daily`           | Rotate logs every 24 hours. |
| `rotate 7`        | Retain 7 compressed backups before deletion. |
| `compress`        | Use gzip to compress rotated logs, saving disk space. |
| `delaycompress`   | Defer compression until the next rotation cycle. |
| `missingok`       | Skip missing log files without error. |
| `notifempty`      | Avoid rotating empty log files. |
| `sharedscripts`   | Run the `postrotate` script only once per rotation cycle. |
| `create 0640 asterisk asterisk` | Set secure permissions for new logs. |
| `su asterisk asterisk` | Run rotation tasks with the correct user/group to prevent permission issues. |
| `postrotate`      | Reload Asterisk's logger service after rotation to continue smooth logging. |

---

## 🛡 Security Considerations

- The `/var/log/asterisk/` directory should have strict permissions, typically `750`.
- Ownership of log files and directory must be consistently set to `asterisk:asterisk`.
- Improper permissions may cause Logrotate to fail and result in uncontrolled log growth.

Example permissions:

```bash
drwxr-x--- 5 asterisk asterisk 4096 Apr 28 08:10 /var/log/asterisk
```

---

> NOTE: change 750 to any permission you want as required
