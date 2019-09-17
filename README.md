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


In order to make changes, you'll need to install the proper version of clang-format.

In order to install the llvm (clang) toolchain, you may have to follow instructions on https://apt.llvm.org/

    sudo apt-get install clang-format-5.0

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
- Type `./configure`   *(If configure complains about compiler versions, try `CXX=clang-5.0 ./configure` or `CXX=g++-6 ./configure` or similar, depending on your compiler.)*
- Type `make` or `make -j`(for aggressive parallel build)
- Type `make check` to run tests.
- Type `make install` to install.

## Building with clang and libc++

On some systems, building with `libc++`, [LLVM's version of the standard library](https://libcxx.llvm.org/) can be done instead of `libstdc++` (typically used on Linux).

NB: there are newer versions available of both clang and libc++, you will have to use the versions suited for your system.

You may need to install additional packages for this, for example, on Linux Ubuntu:

    # install libc++ headers
    sudo apt-get install libc++-dev libc++abi-dev

Here are sample steps to achieve this:

    export CC=clang-5.0
    export CXX=clang++-5.0
    export CFLAGS="-O3 -g1 -fno-omit-frame-pointer"
    export CXXFLAGS="$CFLAGS -stdlib=libc++ -isystem /usr/include/libcxxabi"
    git clone https://github.com/HashCash-Consultants/HCNet-Core
    cd HCNet-Core/
    ./autogen.sh && ./configure && make -j6

Postgres database need to install to store core data
## Install postgres database
```
sudo apt-get update
sudo apt-get install postgresql postgresql-contrib
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
