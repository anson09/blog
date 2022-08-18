---
order: 8
---

# 10x Command

## capture package and request interception

```bash
tcpdump -i ens192 -Xnns 0 port not 22 and tcp and not dst net 192.168
iptables -I OUTPUT -p tcp -d 13.250.94.254 -j REJECT
```

## one-line

- `alias`
- `dirs -v`
- `git shortlog -sn`
- `git status --ignore`
- `git check-ignore -v <file>`
- `journalctl -u shadowsocks-libev.service|xray.service -f`
- `nc -kl port` **could use as a http request log service**

## cron

- Command PATH, expand `PATH=$PATH:other_path` in crontab file first line (default is `/usr/bin:/bin`)
- Files permission, go settings->security set cron full disk access or specific folders

If cron job's output(1\|2) not redirect, it will mail to local(`mail` command check `/var/mail/`), omit mail(`>/dev/null 2>&1`), you could add `MAILTO="someone@somewhere.com"` in crontab file, this send mail to network mailbox

## ln

### Symlinks:

- When do path resolve, it assume itself as the source inode path
- have itself's inode id
- unaccessible when source inode is deleted

### Hard link:

- have the same inode number with the source file
- inode reference count +/- 1 when create/delete a hard link
- remove source file don't influence hard link file
- directory can't have hard link
- `exa -lH` show reference count field
- `ls -i <filename>|choose 0|xargs find . -inum` find all hard link reference
