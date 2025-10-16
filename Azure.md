# DVC using an Azure Blob storage container

Notes:

* git/dvc commands are typically executed from within the locally cloned directory.
* based on DVC tutorials/documentation:

  * https://dvc.org/doc/start
  * https://dvc.org/doc/user-guide/data-management/remote-storage/azure-blob-storage
  * https://medium.com/@docs.thiru/dvc-with-azure-blob-storage-06f965b846f3

* replace `myremote` with an appropriate name


## Prerequisites

* install git
* install dvc

  https://dvc.org/doc/install

* install Azure CLI

  https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-linux?view=azure-cli-latest&pivots=apt

  **NB:** On Linux Mint, replace the `Suites` in `/etc/apt/sources.list.d/azure-cli.sources` with the name of the underlying Ubuntu release, e.g., `noble`.

* log into Azure with CLI

  ```bash
  az login [--use-device-code]
  ```


* create a [blob storage container](https://learn.microsoft.com/en-us/azure/storage/blobs/blob-containers-portal) in your storage account, e.g.:

  * container name: `dvctest`
  * anonymous access: `disabled`
  * account key access: `enabled`
  * performance: `standard`


* create a SAS (shared access signature) token for the container 
  (Settings -> Shared access tokens):

  * signing method: `Account key`
  * permissions: `read/add/create/write/delete/list`
  * expiry: select appropriate date/time, e.g., +1 year
  * if appropriate, limit the IP addresses that can access
  * allowed protocols: HTTPS only
  * click on *generate SAS token and URL*
  * copy SAS URL


##  Setup project

* create github repo, e.g.:

  https://github.com/fracpete/dvc-test-azure

* check out repo:

  ```bash
  git clone git@github.com:fracpete/dvc-test-azure.git
  cd dvc-test-azure
  ```

* initialize dvc within cloned repo:

  ```bash
  dvc init
  git commit -a -m "initialized dvc"
  git push
  ```

* configure default remote and SAS token

  * add Azure container and its (optional) sub-folder:

    ```bash
    dvc remote add -d myremote azure://<mycontainer>/<path>
    ```
    
    E.g., when you want to manage multiple projects within the same 
    bucket, separate them with a sub-folder:

    ```bash
    dvc remote add -d myremote azure://dvctest/project1
    ```

  * add *local* config file for the key that you used for generating 
    the SAS token and the SAS token itself, which gets excluded 
    automatically from git:

    ```bash
    dvc remote modify --local myremote account_name 'mystorage'
    dvc remote modify --local myremote sas_token 'SASTOKEN'
    ```

  * commit changes:

    ```bash
    git commit -a -m "configured azure storage container"
    ```

* add initial data

  * create dir:

    ```bash
    mkdir data
    [copy binary data into "data" dir]
    ```

  * let data directory be managed by dvc (executed from above the `data` dir):

    ```bash
    dvc add data
    ```

  * add dvc control file to git:

    ```bash
    git add data.dvc .gitignore
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

* log into Azure with CLI

  ```bash
  az login [--use-device-code]
  ```

* clone the repo:

  ```bash
  cd /somewhere/else
  git clone git@github.com:fracpete/dvc-test-azure.git
  cd dvc-test-azure
  ```

* add *local* config file for the key that you used for generating 
  the SAS token and the SAS token itself, which gets excluded 
  automatically from git:

  ```bash
  dvc remote modify --local myremote account_name 'mystorage'
  dvc remote modify --local myremote sas_token 'SASTOKEN'
  ```

* grab the data:

  ```bash
  dvc pull
  ```

