# apache2.2.x-mod_encoding

How to compile the apache2.2.x's mod_encoding? Here it is.

## Download the original files

	mod_encoding-20021209.tar.gz
	mod_encoding.c.apache2.20040616
	mod_encoding.c-apache2.2-20060520.patch

## Unpack & Replace & Patch

* Unpack:

		$ tar -xzf mod_encoding-20021209.tar.gz

* Replace with .c file for apache2.2.x (as the older file for apache1.3):

		$ cd mod_encoding-20021209
		$ cp ../mod_encoding.c.apache2.20040616 mod_encoding.c

* Patch:

		$ patch -p0 < ../mod_encoding.c-apache2.2-20060520.patch


## Build & Install **mod_encoding**

* Compile **iconv-hook**

		$ cd lib
		$ ./configure --prefix=/usr
		$ make

* Install **apxs** tool for building apache modules:

		$ yum install httpd-devel.x86_64

 Then, we get apxs at：/usr/sbin/apxs

* Build & install **mod_encoding.so**

		$ ./configure --with-apxs=/usr/sbin/apxs --with-iconv-hook=/usr/include
		$ make
		$ gcc -shared -o mod_encoding.so mod_encoding.o -Wc,-Wall -Llib -liconv_hook
		$ cp mod_encoding.so /etc/httpd/modules/

## Config & Restart apache2.2.x

* Find lines in file **httpd.conf** for loading apache modules, add a line:

		LoadModule encoding_module modules/mod_encoding.so

* Inner the right **VirtualHost** directive of that file, add lines: 

		<IfModule mod_encoding.c>
			EncodingEngine on
			NormalizeUsername on
			SetServerEncoding GBK
			DefaultClientEncoding UTF-8 GBK GB2312
		</IfModule>

* Restart apache

		sudo service httpd stop
		sudo service httpd start

## Original authors' marvelous works

> http://webdav.todo.gr.jp/download/mod_encoding-20021209.tar.gz
> http://webdav.todo.gr.jp/download/experimental/mod_encoding.c.apache2.20040616
> http://www.aconus.com/~oyaji/faq/mod_encoding.c-apache2.2-20060520.patch