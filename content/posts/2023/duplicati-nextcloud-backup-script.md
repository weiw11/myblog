---
title: "Duplicati Nextcloud Backup Script"
tags: ["duplicati", "ubuntu", "nextcloud"]
date: 2023-02-13T12:15:03-05:00
draft: false
---

When Duplicati backups and restores Nextcloud files, we want to enable maintenance mode.

However by default, Duplicati runs scripts on every operation including when it lists the restore folders, which we don't want.

The following scripts prevents Duplicati from running the script when it's only listing the restore folders.

{{< tabs "nc_scripts" >}}
{{< tab "NextcloudOn.sh" >}}

Script to turn **on** Nextcloud Maintenance mode

```sh
#!/bin/bash
if [ "%DUPLICATI__OPERATIONNAME%" != "List" ]
then
  nextcloud.occ maintenance:mode --on
fi
exit 0
```

{{< /tab >}}
{{< tab "NextcloudOff.sh" >}}

Script to turn **off** Nextcloud Maintenance mode

```sh
#!/bin/bash
if [ "%DUPLICATI__OPERATIONNAME%" != "List" ]
then
  nextcloud.occ maintenance:mode --off
  if [ "%DUPLICATI__OPERATIONNAME%" == "Restore" ]
  then
    nextcloud.occ files:scan --all
  fi
fi
exit 0
```

{{< /tab >}}
{{< /tabs >}}

If we restore the files, Nextcloud won't realize the files are there because it's not updated in the database, so we have to trigger a scan.
