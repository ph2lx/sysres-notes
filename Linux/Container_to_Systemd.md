# Documentation Symphonie – Podman / Systemd

## Table des matières
1. Tapez le titre du chapitre (niveau 1)
2. Tapez le titre du chapitre (niveau 2)
3. Tapez le titre du chapitre (niveau 3)
4. Tapez le titre du chapitre (niveau 1)
5. Tapez le titre du chapitre (niveau 2)
6. Tapez le titre du chapitre (niveau 3)

---

# Snapshot état d’origine

## Désactivation de cron

```bash
crontab -l
SHELL=/bin/bash
PATH=/sbin:/bin:/usr/sbin:/usr/bin
#MAILTO=root
#@reboot /bin/bash /Exploitation/Redhat_Update.sh
#@reboot sleep 120 && /astechReboot.sh >> /var/log/astechreboot.log 2>&1
#00 1 * * * sudo /bin/podman-compose -f /home/... exec -T php bash /checkDroits.sh >/dev/null 2>&1
