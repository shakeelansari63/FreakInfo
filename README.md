# Install Python2.7 on HDP (Credit: danieleriksson.net)

yum update

yum groupinstall -y "development tools"

yum install -y zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel expat-devel

yum install -y wget


wget http://python.org/ftp/python/2.7.14/Python-2.7.14.tar.xz

tar xf Python-2.7.14.tar.xz

cd Python-2.7.14

./configure --prefix=/usr/local --enable-unicode=ucs4 --enable-shared LDFLAGS="-Wl,-rpath /usr/local/lib"

make && make altinstall


wget http://python.org/ftp/python/3.6.3/Python-3.6.3.tar.xz

tar xf Python-3.6.3.tar.xz

cd Python-3.6.3

./configure --prefix=/usr/local --enable-shared LDFLAGS="-Wl,-rpath /usr/local/lib"

make && make altinstall


strip /usr/local/lib/libpython2.7.so.1.0

strip /usr/local/lib/libpython3.6m.so.1.0


wget https://bootstrap.pypa.io/get-pip.py

python2.7 get-pip.py

python3.6 get-pip.py

