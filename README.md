# Tools and Tips 

- Tools I used for development
- Methods I used for create protobuf files
- Backup docker images for all service I used
- and more..

# Topics

- [Mysql 5.7 docker image](#mysql)
- [Php 8.0.7 with asdf](#compiling-php-807-into-asdf)
- [SQL Server docker image](#sql-server-latest)
- [Stop nodes (pids) Script](#stop-nodes-pids)
- [Compiling Protobuf Files](#compiling-protobuf-files)
- [Export MySQl tables to Go struct](#export-mysql-tables-to-go-struct)
- [Git Credential Manager Core (create a secure way to store git username/passwords (ubuntu))](#git-credential-manager-core)
- [Curl message and data validation to Bash](#bash-data-validation-and-curl-messages)
- [PostgreSql (Latest) docker image](#postgresql)
- [XDebug PHP 8.0.7 in asdf install](#xdebug-php-807-asdf)

# Mysql
-------------
- Archive: mysql57.yaml

- Image | Description
  ------|-----------
  mysql57.yaml | Create a mysql instance for version 5.7 (create a adminer image in localhost:9898 to connect into mysql database via php) |

- Create the folder: ```/home/[user]/dockers/mysql57/conf.d/```
    - In ```conf.d``` directory create a text file called ```my.conf``` with:
    - ```shell
      [mysqld] 
      max_connections=200
      ``` 
- Create the folder: ```/home/[user]/dockers/mysql57/db```

# Compiling php 8.0.7 into asdf
-------------------------------

Requirments:
[
- Install all necessary packages
```shell
sudo apt install -y pkg-config build-essential autoconf bison re2c \
                    libxml2-dev libsqlite3-dev libcurl4 libcurl4-openssl-dev \
                    libgd-dev libonig-dev libpq-dev libzip-dev
```

- Then install php 8.0.7

```shell
asdf plugin-add php
asdf install php 8.0.7
``` 

# SQL server (latest)
-------------------------------

- Start server with:

```shell
docker run --name mssql -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=bfd972@!' -p 1433:1433 -d mcr microsoft.com/mssql/server:2017-latest
```

# Stop Nodes PIDs
-------------------------------

- Create a stop_nodes.sh file with:

```shell
#!/bin/bash
# declare all ports are used
declare -a portsServices=("9000" "9001" "9002" "9003" "9004" "9005" "9006" "9007")

# get length of array
portArrayLength=${#portsServices[@]}

# Kill all ports if exists
for (( i=1; i<${portArrayLength}+1; i++ ));
do
  echo "Killing port: - "  $i " / " ${portArrayLength} " : " ${portsServices[$i-1]}
  echo "Killing PID: " $(lsof -t -i:${portsServices[$i-1]})
  lsof -n -i:${portsServices[$i-1]} | grep LISTEN | tr -s ' ' | cut -f 2 -d ' ' | xargs kill -9
done
```

- Make executable with ```chmod +x stop_node.sh``` 

# Compiling Protobuf Files
-------------------------------

- See protobuf folder [```readme.md```](./protobuf/README.md) file

# Export MySQl tables to Go struct
-------------------------------

- Clone the repository: ```https://github.com/xxjwxc/gormt```
- Compile
- Configure ```config.xml``` file to your database
- Execute ```./gormt```


# Git Credential Manager Core

- For remote repositories we don't have access to use ssh key

1. Download the last `.deb` release of credential manager core: [Here](https://github.com/microsoft/Git-Credential-Manager-Core/releases/tag/v2.0.475)
2. Install package ```sudo dpkg -i <path-to-package>```
3. Configure the gui (if have (in ubuntu, will open in terminal only))
  ```
  export GCM_CREDENTIAL_STORE=secretservice
  ```
  or
  ```
  git config --global credential.helper store secretservice
  ``` 
  4. Finish, after the next time you needed to use the username/password, they will store your data encrypted saffely.

  # Bash data validation and curl messages
  
  - See [curlToVariable.sh](./curlToVariableBash.sh) archive in this directory

  # PostgreSql
-------------
- Archive: postgresql.yaml

- Image | Description
  ------|-----------
  postgresql.yaml | Create a PostgreSQL instance for version (latest) (create a adminer image in localhost:9797 to connect into PostgreSQL database via php) |

- Create the folder: ```/home/$(whoami)/dockers/postgresql```
- Run docker-compose: ```docker-compose -f postgresql.yaml up```
- Backup volumes from postgree folder: ```https://stackoverflow.com/a/57773315/9397637```

# XDebug PHP 8.0.7 asdf
------------------

1 - Download [xdebug-3.0.4.tgz](http://xdebug.org/files/xdebug-3.0.4.tgz)

2- Install the pre-requisites for compiling PHP extensions: ```apt-get install php-dev autoconf automake```

3 - Unpack the downloaded file with tar -xvzf xdebug-3.0.4.tgz

4 - Run: cd xdebug-3.0.4

5 - Run: phpize (See the FAQ if you don't have phpize).

As part of its output it should show:
``` 
Configuring for:
...
Zend Module Api No:      20200930
Zend Extension Api No:   420200930
If it does not, you are using the wrong phpize. Please follow this FAQ entry and skip the next step.
```
6 - Run: ./configure

7 - Run: make

8 - Run: ```cp modules/xdebug.so /home/$(whoami)/.asdf/installs/php/8.0.7/lib/php/extensions/no-debug-non-zts-20200930```

9 - Create ```/home/$(whoami)/.asdf/installs/php/8.0.7/php.ini``` and add the line
    ```
    [xdebug]
    zend_extension = /home/$(whoami)/.asdf/installs/php/8.0.7/lib/php/extensions/no-debug-non-zts-20200930/xdebug.so
    xdebug.mode=debug
    xdebug.client_host=127.0.0.1
    xdebug.client_port=9010
    ```

10 - Test ```php --version``` you need to see a return like this:
      ```
      PHP 7.3.6 (cli) (built: Jul  8 2021 13:39:39) ( NTS )
      Copyright (c) 1997-2018 The PHP Group
      Zend Engine v3.3.6, Copyright (c) 1998-2018 Zend Technologies
          with Xdebug v3.0.4, Copyright (c) 2002-2021, by Derick Rethans
      ```
      if any error like "Xdebug requires Zend Engine API version 420200930." use the tutorial in: https://xdebug.org/wizard

### Configuring PHPStorm
1. Open Run -> Edit Configurations...
2. Create new 'PHP Built-in Web Server'
3. Set values:
  ```
  Host: localhost
  Port: 8000
  ``` 
4. Document root: select Laravel's public catalog/directory
5. Check Use route script and select server.php in Laravel projects root directory.
6. Interpreter options: 
  - Xdebug ^2.*:
  ```
  -dxdebug.remote_enable=1 -dxdebug.remote_mode=req -dxdebug.remote_port=9000 -dxdebug.remote_host=127.0.0.1
  ```
  - Xdebug ^3.*:
  ```
  -dxdebug.mode=debug -dxdebug.start_with_request=trigger -dxdebug.client_port=9003 -dxdebug.client_host=127.0.0.1
  ``` 
7. click OK and run.

- In chrome/firefox configure a `page` script for start debug session for PHPStorm:
  - Start script: 
    ```javascript
    javascript:(/** @version 0.5.2 */function() {document.cookie='XDEBUG_SESSION='+'PHPSTORM'+';path=/;';})()
    ```
  - Stop script:
    ```javascript
    javascript:(/** @version 0.5.2 */function() {document.cookie='XDEBUG_SESSION='+''+';expires=Mon, 05 Jul 2000 00:00:00 GMT;path=/;';})()
    ```
