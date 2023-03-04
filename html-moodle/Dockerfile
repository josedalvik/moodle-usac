FROM ubuntu:jammy

MAINTAINER Jose Pablo <josepablo@profesor.usac.edu.gt>

ARG MOODLE_DB_IP="db"
ARG MOODLE_DB_NAME="mdl"
ARG MOODLE_DB_USER="mdl"
ARG MOODLE_DB_USER_PW="jloeMLDS9433"
ARG MOODLE_DB_PORT="3306"
ARG MOODLE_PUBLIC_URL="http://127.0.0.1/capacitacion"
ARG MOODLE_MOODLE_DATA="/var/www/moodledata"
ARG MOODLE_HTML="/var/www/html/capacitacion"

ARG DEBIAN_FRONTEND=noninteractive
ARG TZ=America/Guatemala

COPY html.zip /html.zip
COPY moodledata.zip /moodledata.zip
COPY supervisor.conf /etc/supervisor.conf

RUN apt-get update && apt-get install -y \ 
    apache2 \ 
	software-properties-common \ 
	apt-utils \ 
	locales \ 
	tzdata \ 
	netcat \ 
	&& add-apt-repository ppa:ondrej/php -y \ 
	&& apt-get update \ 
	&& date \ 
	&& [ -f "/etc/localtime" ] && unlink /etc/localtime \ 
	&& ln -s /usr/share/zoneinfo/America/Guatemala /etc/localtime \ 
	&& date \ 
	&& apt-get install -y php7.3-fpm \ 
	libapache2-mod-fcgid \ 
	&& a2enconf php7.3-fpm \ 
	&& a2enmod proxy \ 
	&& a2enmod proxy_fcgi \ 
	&& apt-get install -y php7.3-curl \ 
	php7.3-zip  \  
	php7.3-gd  \  
	php7.3-intl  \  
	php7.3-xmlrpc  \  
	php7.3-soap  \  
	php7.3-imagick  \  
	php7.3-xml  \  
	php7.3-mbstring  \  
	php7.3-fpm  \  
	php7.3-mysql  \  
	unzip  \  
	zip  \  
	imagemagick  \  
	php-imagick  \  
	php7.3-imagick  \  
	ghostscript  \  
	libgs-dev  \  
	php7.3-xml  \  
	php7.3-mbstring  \  
	nano  \  
	net-tools  \  
	iputils-ping  \  
	cron  \  
	supervisor \ 
	&& unzip /html.zip -d ${MOODLE_HTML} \ 
	&& unzip /moodledata.zip -d ${MOODLE_MOODLE_DATA} \ 
	&& chmod 755 ${MOODLE_MOODLE_DATA} -R \ 
	&& chown www-data:www-data /var/www/ -R \ 
	&& [ -f "/var/www/html/index.html" ] && rm /var/www/html/index.html -R \ 
	&& rm /html.zip \ 
	&& rm /moodledata.zip \ 
	&& sed -i "s|{MOODLE-DB-IP}|${MOODLE_DB_IP}|g" ${MOODLE_HTML}/config.php \ 
	&& sed -i "s|{MOODLE-DB-NAME}|${MOODLE_DB_NAME}|g" ${MOODLE_HTML}/config.php \ 
	&& sed -i "s|{MOODLE-DB-USER}|${MOODLE_DB_USER}|g" ${MOODLE_HTML}/config.php \ 
	&& sed -i "s|{MOODLE-DB-USER-PW}|${MOODLE_DB_USER_PW}|g" ${MOODLE_HTML}/config.php \ 
	&& sed -i "s|{MOODLE-DB-PORT}|${MOODLE_DB_PORT}|g" ${MOODLE_HTML}/config.php \ 
	&& sed -i "s|{MOODLE-PUBLIC-URL}|${MOODLE_PUBLIC_URL}|g" ${MOODLE_HTML}/config.php \ 
	&& sed -i "s|{MOODLE-MOODLE-DATA}|${MOODLE_MOODLE_DATA}|g" ${MOODLE_HTML}/config.php \ 
	&& crontab -u www-data -l > cronphp \ 
	&& echo "* * * * * /usr/bin/php7.3 ${MOODLE_HTML}/admin/cli/cron.php" > cronphp \ 
	&& crontab -u www-data cronphp \ 
	&& rm cronphp

COPY mpm_event.conf /etc/apache2/mods-enabled/mpm_event.conf
COPY apache2.conf /etc/apache2/apache2.conf
COPY www.conf /etc/php/7.3/fpm/pool.d/www.conf
COPY ports.conf /etc/apache2/ports.conf

VOLUME /var/www/

EXPOSE 80

CMD ["supervisord", "-c", "/etc/supervisor.conf"]