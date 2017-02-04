---
date: '2016-12-15 22:11 +0100'
published: true
title: Simple Linux Script
category:
  - Linux
---
```bash
vim {name}.sh
```

*{name}.sh*

```
#!/bin/bash
echo "Hello $USER."
echo "Today is $(date)"
echo "Current working directory : $(pwd)"
```

**Permissions**

```bash
chmod +x hello.sh
```

**Run**

```bash
./hello.sh
```
