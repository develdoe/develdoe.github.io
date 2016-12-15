---
date: '2016-12-15 15:55 +0100'
published: true
title: Restart Program Bash Script
---

```bash
vim launch.sh
```

```sh
#!/bin/sh

ps auxw | grep apache2 | grep -v grep > /dev/null

if [ $? != 0 ]
then
        /etc/init.d/apache2 start > /dev/null
fi
```

```bash
chmod +x launch.sh
```

*The cron utility allows us to schedule at what intervals the script should execute.*

```bash
crontab -e
```

**Every five minutes would be set up like this:**

```
*/5 * * * * ~/launch.sh
```