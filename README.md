
## prerequiste

    1. make
    2. gcc 
    3. gcc-c++ 
    4. boost-devel
    5. libtool
    6. apr-devel
    7. kernel-devel 
    8. expat-devel 
    9. zlib-devel 
    10. openssl-devel 
    11. libcurl-devel 
    12. xml-security-c-devel 
    13. httpd-devel

## apache & dependencies

1. download apr-1.7.0.tar.gz from https://apr.apache.org/download.cgi
2. download apr-util-1.6.1.tar.gz from https://apr.apache.org/download.cgi
3. download httpd-2.4.41.tar.gz from https://httpd.apache.org/download.cgi
4. download openssl-1.1.1e.tar.gz from https://www.openssl.org/source


	[psoft@pmhpsssodz01vt app]$ ls -l /home/psoft/temp/*.gz
	-rw-rw-r--. 1 psoft psoft 1093896 Aug  7 04:51 /home/psoft/temp/apr-1.7.0.tar.gz
	-rw-rw-r--. 1 psoft psoft  554301 Aug  7 04:51 /home/psoft/temp/apr-util-1.6.1.tar.gz
	-rw-rw-r--. 1 psoft psoft 9267917 Aug  7 04:51 /home/psoft/temp/httpd-2.4.41.tar.gz
	-rw-rw-r--. 1 psoft psoft 9792634 Aug  7 04:51 /home/psoft/temp/openssl-1.1.1e.tar.gz

## setup/build/install
### build/install openssl
	[psoft@pmhpsssodz01vt temp]$ tar xvf openssl-1.1.1e.tar.gz
	[psoft@pmhpsssodz01vt temp]$ cd /home/psoft/temp/openssl-1.1.1e
	[psoft@pmhpsssodz01vt openssl-1.1.1e]$ ./config --prefix=/app/openssl --openssldir=/app/openssl
	[psoft@pmhpsssodz01vt openssl-1.1.1e]$ make && make install

### place/extract apr, apr-util in srclib folder of httpd
	[psoft@pmhpsssodz01vt temp]$ tar xvf httpd-2.4.41.tar.gz
	[psoft@pmhpsssodz01vt temp]$ cd /home/psoft/temp/httpd-2.4.41/srclib
	[psoft@pmhpsssodz01vt srclib]$ tar xvf /home/psoft/temp/apr-1.7.0.tar.gz
	[psoft@pmhpsssodz01vt srclib]$ mv apr-1.7.0/ apr
	[psoft@pmhpsssodz01vt srclib]$ tar xvf /home/psoft/temp/apr-util-1.6.1.tar.gz
	[psoft@pmhpsssodz01vt srclib]$ mv apr-util-1.6.1/ apr-util

### build/install httpd
	[psoft@pmhpsssodz01vt srclib]$ cd /home/psoft/temp/httpd-2.4.41
	[psoft@pmhpsssodz01vt httpd-2.4.41]$ ./configure --prefix=/app/httpd --enable-ssl --enable-proxy --enable-proxy-connect --enable-proxy-http --enable-rewrite --with-included-apr --with-ssl=/app/openssl
							  ./configure --prefix=/app/httpd --enable-so --enable-ssl --enable-proxy --enable-proxy-connect --enable-proxy-http --enable-rewrite --with-included-apr --with-ssl=/app/openssl
	[psoft@pmhpsssodz01vt httpd-2.4.41]$ make && make install

### create self-signed certificate for https
	[psoft@pmhpsssodz01vt httpd-2.4.41] cd /app/openssl/bin
	[psoft@pmhpsssodz01vt bin]$ openssl req -x509 -newkey rsa:4096 -keyout /app/httpd/conf/server.key -out /app/httpd/conf/server.crt -nodes -days 900 -subj "/C=US/ST=MD/L=Baltimore/O=Medstar/OU=Security/CN=pmhpsssodz01vt.medstar.net"
	[psoft@pmhpsssodz01vt bin]$ ls /app/httpd/conf/server*
	/app/httpd/conf/server.crt  /app/httpd/conf/server.key
	[psoft@pmhpsssodz01vt bin]$ ls /app
	httpd  openssl
	[psoft@pmhpsssodz01vt bin]$

### build shibboleth-sp and dependencies
------------------------------
1. download log4shib-2.0.0.tar.gz from http://shibboleth.net/downloads/log4shib/latest/
2. download xerces-c-3.2.2.tar.gz from http://mirror.hosting90.cz/apache/xerces/c/3/sources/
3. download xml-security-c-2.0.2.tar.gz from http://mirror.hosting90.cz/apache/santuario/c-library/
4. download xmltooling-3.0.5.tar.gz from http://shibboleth.net/downloads/c++-opensaml/latest/
5. download opensaml-3.0.1.tar.gz from http://shibboleth.net/downloads/c++-opensaml/latest/
6. download shibboleth-sp-3.0.4.tar.gz from http://shibboleth.net/downloads/service-provider/latest/

### build/install shibboleth dependencies
	[psoft@pmhpsssodz01vt shibb-sp]$ ls
	log4shib-2.0.0         opensaml-3.0.1         shibboleth-sp-3.0.4         xerces-c-3.2.2         xml-security-c-2.0.2         xmltooling-3.0.5
	log4shib-2.0.0.tar.gz  opensaml-3.0.1.tar.gz  shibboleth-sp-3.0.4.tar.gz  xerces-c-3.2.2.tar.gz  xml-security-c-2.0.2.tar.gz  xmltooling-3.0.5.tar.gz
	[psoft@pmhpsssodz01vt shibb-sp]$ cd /home/psoft/temp/shibb-sp/log4shib-2.0.0
	[psoft@pmhpsssodz01vt log4shib-2.0.0]$ ./configure --prefix=/app/log4shib
	[psoft@pmhpsssodz01vt log4shib-2.0.0]$ make && make install
	[psoft@pmhpsssodz01vt log4shib-2.0.0]$ cd /home/psoft/temp/shibb-sp/xerces-c-3.2.2
	[psoft@pmhpsssodz01vt xerces-c-3.2.2]$ ./configure --prefix=/app/xerces-c
	[psoft@pmhpsssodz01vt xerces-c-3.2.2]$ make && make install
	[psoft@pmhpsssodz01vt xerces-c-3.2.2]$ cd /home/psoft/temp/shibb-sp/xml-security-c-2.0.2
	[psoft@pmhpsssodz01vt xml-security-c-2.0.2]$ export PKG_CONFIG_PATH=/app/xerces-c/lib/pkgconfig:$PKG_CONFIG_PATH
	[psoft@pmhpsssodz01vt xml-security-c-2.0.2]$ ./configure --prefix=/app/xml-security-c
	[psoft@pmhpsssodz01vt xml-security-c-2.0.2]$ make && make install
	[psoft@pmhpsssodz01vt xml-security-c-2.0.2]$ cd /home/psoft/temp/shibb-sp/xmltooling-3.0.5
	[psoft@pmhpsssodz01vt xml-security-c-2.0.2]$ export PKG_CONFIG_PATH=/app/log4shib/lib/pkgconfig:$PKG_CONFIG_PATH
	[psoft@pmhpsssodz01vt xmltooling-3.0.5]$ ./configure --prefix=/app/xmltooling -C
	[psoft@pmhpsssodz01vt xmltooling-3.0.5]$ make && make install
	[psoft@pmhpsssodz01vt xmltooling-3.0.5]$ cd /home/psoft/temp/shibb-sp/opensaml-3.0.1
	[psoft@pmhpsssodz01vt opensaml-3.0.1]$ export PKG_CONFIG_PATH=/app/xmltooling/lib/pkgconfig:$PKG_CONFIG_PATH
	[psoft@pmhpsssodz01vt opensaml-3.0.1]$ ./configure --prefix=/app/opensaml -C

### build/install shibboleth
	[psoft@pmhpsssodz01vt opensaml-3.0.1]$ cd /home/psoft/temp/shibb-sp/shibboleth-sp-3.0.4
	[psoft@pmhpsssodz01vt shibboleth-sp-3.0.4]$ make && make install
	[psoft@pmhpsssodz01vt shibboleth-sp-3.0.4]$ export PKG_CONFIG_PATH=/app/opensaml/lib/pkgconfig:/app/xmltooling/lib/pkgconfig:/app/xmltooling/lib/pkgconfig:/app/xml-security-c/lib/pkgconfig:/app/log4shib/lib/pkgconfig:/app/xerces-c/lib/pkgconfig
	[psoft@pmhpsssodz01vt shibboleth-sp-3.0.4]$ export LD_LIBRARY_PATH=/app/openssl/lib
	[psoft@pmhpsssodz01vt app]$ cd ~
	[psoft@pmhpsssodz01vt ~]$ ls /app/httpd/conf/server.*
	/app/httpd/conf/server.crt  /app/httpd/conf/server.key
	[psoft@pmhpsssodz01vt ~]$ ls /app/shibboleth-sp/etc/shibboleth/*.pem
	/app/shibboleth-sp/etc/shibboleth//sp-encrypt-cert.pem  /app/shibboleth-sp/etc/shibboleth//sp-encrypt-key.pem  /app/shibboleth-sp/etc/shibboleth//sp-signing-cert.pem  /app/shibboleth-sp/etc/shibboleth//sp-signing-key.pem
	[psoft@pmhpsssodz01vt ~]$

## apache configuration
### /app/httpd/conf/extra/httpd-ssl.conf

update servername (dns of reverse proxy server)

	ServerName npdess.medstar.net:443

add shibboleth handler  basically provides shibboleth endpoints (like metadata)

	<Location /Shibboleth.sso>
		SetHandler shib
	</Location>

add application handler

	<Location />
	    AuthType shibboleth
	    ShibRequestSetting requireSession 1
	    ShibUseHeaders On
	    require valid-user
	</Location>
	
	ProxyPass / http://10.130.164.51:8080/
	ProxyPassReverse / http://10.130.164.51:8080/
	ProxyPreserveHost On

**note:** `http://10.130.164.51:8080/` is just a sample, this shall be replaces with - actuall application url that needs to be protected/secured (i.e only authenticated users shall be able to access application.)

*<u>application will recieve **remote_user** header that will contain userid of user that logged in via external(azure) idp</u>* 

### /app/httpd/conf/httpd.conf 

make sure following modules are enabled in loadmodule section

	LoadModule ssl_module modules/mod_ssl.so
	LoadModule proxy_module modules/mod_proxy.so
	LoadModule proxy_connect_module modules/mod_proxy_connect.so
	LoadModule proxy_http_module modules/mod_proxy_http.so
	LoadModule headers_module modules/mod_headers.so

add mod_shib module to loadmodule section

	LoadModule mod_shib /app/httpd/modules/mod_shib_24.so

add/enable/uncomment following line

	Include conf/extra/httpd-ssl.conf

## shibboleth configuration
### /app/shibboleth-sp/etc/shibboleth/shibboleth2.xml

create directory `/app/shibboleth-sp/etc/shibboleth/idp-metadata` , and place metadata xml from idp(s) in this directory

add entityid of idp(s)

    <SSO entityID="https://samltest.id/saml/idp" discoveryProtocol="SAMLDS" 
    	discoveryURL="https://npdess.medstar.net/DS"> SAML2 SAML1 </SSO>

add metadataprovider for idp(s) from filesystem

    <MetadataProvider type="Folder" path="/app/shibboleth-sp/etc/shibboleth/idp-metadata"/>

### /app/shibboleth-sp/etc/shibboleth/attribute-map.xml

add upn mapped claim as eppn 

    <Attribute name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" id="eppn">
        <AttributeDecoder xsi:type="ScopedAttributeDecoder" caseSensitive="false"/>
    </Attribute>

### /app/shibboleth-sp/etc/shibboleth/attribute-policy.xml

add eppn attributerule as

    <afp:AttributeRule attributeID="eppn">
        <afp:PermitValueRule xsi:type="ANY"/>
    </afp:AttributeRule>

## start shibd 
issue command

	/app/shibboleth-sp/sbin/shibd

or incase of some try

	/app/shibboleth-sp/sbin/shibd -f

**Note:** Incase of issues kill shibd and start 

## restart apache 

	sudo LD_LIBRARY_PATH=/app/openssl/lib /app/httpd/bin/apachectl -f /app/httpd/conf/httpd.conf -k restart
