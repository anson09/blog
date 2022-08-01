---
order: 8
---

# 10x Command

服务器抓包与请求拦截

```bash
 tcpdump -i ens192 -Xnns 0 port not 22 and tcp and not dst net 192.168
 iptables -I OUTPUT -p tcp -d 13.250.94.254 -j REJECT
```

---

```bash
alias
dirs -v
git shortlog -sn
git status --ignore
git check-ignore -v <file>
journalctl -u shadowsocks-libev.service|xray.service -f
```

---

`cron`

- Command PATH, expand `PATH=$PATH:other_path` in crontab file first line (default is `/usr/bin:/bin`)
- Files permission, go settings->security set cron full disk access or specific folders

If cron job's output(1|2) not redirect, it will mail to local(`mail` command check `/var/mail/`), omit mail(`>/dev/null 2>&1`), you could add `MAILTO="someone@somewhere.com"` in crontab file, this send mail to network mailbox
