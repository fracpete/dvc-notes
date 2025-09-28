# dvc-notes
Notes on using DVC (https://dvc.org) for data version control.

* [Remote server via SSH](SSH.md)
* [AWS S3 bucket](S3.md)


## Opting out of analytics

By default, dvc is collecting anonymous usage statistics:

https://dvc.org/doc/user-guide/analytics

But you can opt out:

https://dvc.org/doc/user-guide/analytics#opting-out

Opt out globally with one command:

```bash
dvc config --global core.analytics false
```


## Deleting old data revisions

To keep data usage in check, e.g., when paying for cloud storage, you can also 
delete files from old revisions using the `dvc gc` command.

The following command will keep only the last three revisions (`--num 3`) starting with
the current version (`--rev HEAD`), deleting them locally and in the remote (`--cloud`):

```bash
dvc gc --num 3 --rev HEAD --cloud
```

**NB:** The git repo will still list these old revisions, of course.

