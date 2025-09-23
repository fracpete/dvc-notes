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

