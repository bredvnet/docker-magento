FROM mysql:5.5
MAINTAINER Björn Rennfanz <bjoern.rennfanz@br-edv.net>

# Create new configuration with longer runtime
RUN mkdir -p /etc/mysql/conf.d \
	&& { \
		echo '[mysqld]'; \
		echo 'skip-host-cache'; \
		echo 'skip-name-resolve'; \
		echo 'datadir = /var/lib/mysql'; \
		echo 'max_allowed_packet = 4096M'; \
		echo 'wait_timeout = 3600'; \
		echo '!includedir /etc/mysql/conf.d/'; \
	} > /etc/mysql/my.cnf

# Create user and deamon dirs
RUN usermod -u 1000 mysql
RUN mkdir -p /var/run/mysqld
RUN chmod -R 777 /var/run/mysqld