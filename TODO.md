### TODO

The following php extensions could not be installed for `php7.4-fpm` using
`docker-php-ext-install`:

- enchant

The following php extensions could not be installed for `php(8.3|7.4)-fpm` using
`docker-php-ext-install`:

- oci8
- odbc
- pdo_dblib
- pdo_firebird
- pdo_oci
- pdo_odbc
- pspell
- random
- readline
- reflection
- snmp
- sodium
- spl
- standard
- tokenizer
- xmlreader
- zend_test
- json
- ldap
- hash

There are problems with installing `imap` and `imagick`:

    RUN apt-get install -y \
        libmagickwand-dev \
		libc-client-dev \
		libkrb5-dev \
	&& docker-php-ext-configure imap \
		--with-kerberos \
		--with-imap-ssl \
	&& docker-php-ext-install -j$(nproc) \
		imap \
	&& apt-get purge -y \
		libc-client-dev \
		libkrb5-dev

    COPY --from=mlocati/php-extension-installer /usr/bin/install-php-extensions /usr/local/bin/
    RUN install-php-extensions imagick/imagick@master

The following php extensions could not be installed for `nginx` using `apt-get`:

- php-filter
- php-gettext
- php-hash
- php-oci8
- php-pcntl
- php-pdo_dblib
- php-pdo_firebird
- php-pdo_oci
- php-pdo_pgsql
- php-pdo_sqlite
- php-recode
- php-reflection
- php-session
- php-spl
- php-standard
- php-wddx
- php-pdo_odbc
