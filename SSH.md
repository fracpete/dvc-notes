# DVC with remote SSH server

Notes:

* git/dvc commands are typically executed from within the locally cloned directory.
* based on DVC tutorials/documentation:

  * https://dvc.org/doc/start
  * https://dvc.org/doc/user-guide/data-management/remote-storage/ssh


## Prerequisites

* install git
* install dvc

  https://dvc.org/doc/install

* remote server `REMOTE` (nickname for `HOST`), accessible via ssh/sftp 
  with user `USER` at `HOST`
* remote path `/data/dvc/dvc-test`


##  Setup project

* create github repo, e.g.:

  https://github.com/fracpete/dvc-test-ssh

* check out repo:

  ```bash
  git clone git@github.com:fracpete/dvc-test-ssh.git
  cd dvc-test-ssh
  ```

* initialize dvc within cloned repo:

  ```bash
  dvc init
  git commit -a -m "initialized dvc"
  git push
  ```

* configure default remote

  * add remote host:

    ```bash
    dvc remote add -d REMOTE ssh://USER@HOST:/data/dvc/dvc-test
    ```

  * ask for password:

    ```bash
    dvc remote modify REMOTE ask_password true
    ```

  * commit changes:

    ```bash
    git commit -a -m "configured remote"
    ```

* add initial data

  * create dir:

    ```bash
    mkdir data
    [copy binary data into "data" dir]
    ```

  * let data directory be managed by dvc:

    ```bash
    dvc add data
    ```

  * add dvc control file to git:

    ```bash
    git add data.dvc
    git commit -m "added data control file" data.dvc .gitignore
    ```

  * push data to remote:

    ```bash
    dvc push
    ```

  * push changes to repo:

    ```bash
    git push
    ```
  

## Update data

* add/delete/modify files in the data directory

* update local cache and hash in data.dvc file ("dvc add" does automatic commit)

  ```bash
  dvc add data
  ```

* commit the updated hash

  ```bash
  git commit -m "data update A" data.dvc
  ```

* push the data changes to the remote host

  ```bash
  dvc push
  ```

* push the modified hash to the repo

  ```bash
  git push
  ```


## Switching versions

* checkout the revelant commit or branch

  ```bash
  git checkout COMMITHASH
  ```

* update the data (according to the current hash in data.dvc)

  ```bash
  dvc pull
  ```


## Switch back to latest

* checkout main branch

  ```bash
  git checkout main
  ```

* update the data again

  ```bash
  dvc pull
  ```


## Checkout repo elsewhere

* clone the repo:

  ```bash
  cd /somewhere/else
  git clone git@github.com:fracpete/dvc-test-ssh.git
  cd dvc-test-ssh
  ```

* grab the data:

  ```bash
  dvc pull
  ```

