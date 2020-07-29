# Methodology

#### Spend some time and read over the results of your enumeration.

#### If Linux Smart Enumeration level 0 or 1 finds something interesting, make a note of it.

#### Have a quick look around for files in your user’s home directory and other common locations \(e.g. /var/backup, /var/logs\).

#### If your user has a history file, read it, it may have important information like commands or even passwords.

#### Try things that don’t have many steps first, e.g. Sudo, Cron Jobs, SUID files.

#### Have a good look at root processes, enumerate their versions and search for exploits.

#### Check for internal ports that you might be able to forward to your attacking machine.

#### If you still don’t have root, re-read your full enumeration dumps and highlight anything that seems odd. This might be a process or file name you aren’t familiar with, an “unusual” filesystem configured \(on Linux, anything that isn’t ext, swap, or tmpfs\), or even a username.

#### At this stage you can also start to think about Kernel Exploits.

