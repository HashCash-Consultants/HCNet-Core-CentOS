# HCNet Core

[![HashCash Consultants](https://github.com/HashCash-Consultants/HCNet-Core/raw/master/img/hashcash-logo.png)](https://www.hashcashconsultants.com/)

HC-net is a open source with a [public repository](https://github.com/HashCash-Consultants/HCNet-Core)

# Build Dependencies

- CPU - dual core 2.5 GHz Intel Xeon
- RAM - 8gb 
- Storage - 500gb
- c++ toolchain and headers that supports c++14
    - `clang` >= 5.0
    - `g++` >= 6.0
- `pkg-config`
- `libpq-dev` unless you `./configure --disable-postgres` in the build step below.
- 64-bit system
- `clang-format-5.0` (for `make format` to work)
-  Postgres db >=9.3 (aurora requires version more than 9.3) 

### CentOS

#### General CentOS Setup
If you just started a fresh instance of CentOS you should take some steps before the installation of hcnet-core to assure the system is secure and up to date.

	The first thing to do is to run:
	sudo yum upgrade
	sudo yum install vim

This will get the repository and all system packages up to date. And of course give vim so that we can more easily edit configuration files.

    #    We will be compiling hcnet-core from source and need the developer tools installed:
    sudo yum groupinstall 'Development Tools'
    sudo yum install postgresql-devel

#### Installing packages
    # if using clang
    sudo yum install devtoolset-7 llvm-toolset-7
    sudo scl enable devtoolset-7 llvm-toolset-7 bash
    clang --version
    # clang with libstdc++
    sudo yum install centos-release-scl
	sudo yum install devtoolset-7-gcc*
	scl enable devtoolset-7 bash
	gcc --version
	g++ --version
	c++ --version
    # building with libstdc++
    sudo rpm -i https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
	sudo yum install svn


#### Configuring libpq
At one point during the hcnet-core compilation you would run ./configure and it would fail with the following error:
No package 'libpq' found
The problem is that when you install PostgreSQL dev tools on CentOS it doesn't install the required file for pkg-config to find it. To solve this you can create the file manually.

	/usr/lib64/pkgconfig/libpq.pc:
	prefix=/usr
	libdir=${prefix}/lib64
	includedir=${prefix}/include/pgsql
	Name: LibPQ
	Version: 5.5.0
	Description: PostgreSQL client library
	Requires:
	Libs: -L${libdir}/libpq.so -lpq
	Cflags: -I${includedir}

## Basic Installation

- `git clone https://github.com/HashCash-Consultants/HCNet-Core`
- `cd HCNet-Core`
- `git submodule init`
- `git submodule update`
- Type `./autogen.sh`.
- Type `./configure`   
- Type `make -j`(for aggressive parallel build)


After the process is completed you can see the HcNet-core build inside /home/centos/HCNet-Core/src 

Postgres database need to install to store core data but before that you must setup aurora. For that please check https://github.com/HashCash-Consultants/Go-CentOS
## Install postgres database
```
- First add the latest version to your rpm for install using yum.
	sudo rpm -Uvh http://yum.postgresql.org/9.6/redhat/rhel-7-x86_64/pgdg-centos96-9.6-3.noarch.rpm

- Then run the install command:
	sudo yum install postgresql96-server postgresql96

- Initialize with this:
	sudo /usr/pgsql-9.6/bin/postgresql96-setup initdb

- Next edit the pg_hba.conf file, in the same folder:
	sudo vim var/lib/pgsql/9.6/data/pg_hba.conf
	Scroll to the bottom of the file and add these lines if they don’t already exist:
	#IPv4 remote connections (all users and IP addresses):
	host all all 0.0.0.0/0 md5

	And update the following lines from 
	host all all 127.0.0.1/32 ident
	host all all          ::1/128 ident

	to 
	host all all 127.0.0.1/32 md5
	host all all          ::1/128 md5
    On the second line beginning with ‘host’ make sure there is no # added. You want it to read as above.
	Exit saving changes.

- Now start and enable postgresdb
	sudo systemctl start postgresql-9.6
	sudo systemctl enable postgresql-9.6 

- Check postgresdb start/stop status 
    sudo systemctl status postgresql-9.6

```
- Postgres user for HCNet core
```
sudo -s
su – postgres
createuser <username> --pwprompt
Enter password for new role: <Enter password>
Enter it again: <Enter the pwd again>
```
Note: This is required for DB url that need to be maintained in HcNet-core.cfg and if you want to set up 5 nodes, you need to create 5 users.
- After creating the user, you need to add them. So exit from postgres and login as root user.
```
exit
adduser <username>;
```
To verify if user is created, execute following commands
```
su - postgres
psql
\du
```
After that create a database. If you have five cores, then create five databases.
```
CREATE DATABASE <DB_NAME> OWNER <user created username>;
```
