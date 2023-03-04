FROM ubuntu/mysql

MAINTAINER Jose Pablo <josepablo@profesor.usac.edu.gt>

ARG MOODLE_USER="dockeruser"
ARG MOODLE_USER_FIRSTNAME="Usuario"
ARG MOODLE_USER_LASTNAME="Docker"
ARG MOODLE_USER_EMAIL="docker@user.com"
ARG MOODLE_USER_PASSWORD="Calleviva123"
ARG MOODLE_USER_PASSWORD_FRANCISCO=""
ARG MOODLE_USER_PASSWORD_JOSEPABLO=""
ARG MYSQL_ROOT_PASSWORD="Lld39dm332"
ARG MOODLE_DB_NAME="mdl"
ARG MOODLE_DB_USER="mdl"
ARG MOODLE_DB_USER_PW="jloeMLDS9433"

ENV MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD}

ARG DEBIAN_FRONTEND=noninteractive
ARG TZ=America/Guatemala

COPY mysql.cnf /etc/mysql/conf.d/mysql.cnf
COPY db.zip /db.zip

RUN apt-get update && apt-get install -y \ 
    nano \ 
    net-tools \ 
    iputils-ping \ 
    zip \ 
    unzip \ 
	apt-utils \ 
	locales \ 
	tzdata \ 
	netcat \  
    && unzip /db.zip -d /db.sql \ 
    && rm /db.zip \ 
    && sed -i "s|{MOODLE-USER}|${MOODLE_USER}|g" /db.sql/db.sql \ 
    && sed -i "s|{MOODLE-USER-FIRSTNAME}|${MOODLE_USER_FIRSTNAME}|g" /db.sql/db.sql \ 
    && sed -i "s|{MOODLE-USER-LASTNAME}|${MOODLE_USER_LASTNAME}|g" /db.sql/db.sql \ 
    && sed -i "s|{MOODLE-USER-EMAIL}|${MOODLE_USER_EMAIL}|g" /db.sql/db.sql \ 
	&& password=$(printf "%s" "${MOODLE_USER_PASSWORD}" | md5sum | cut -d' ' -f1) \ 
    && sed -i "s|{MOODLE-USER-PASSWORD}|${password}|g" /db.sql/db.sql \ 
    && sed -i "s|{MOODLE-USER-PASSWORD-FRANCISCO}|${MOODLE_USER_PASSWORD_FRANCISCO}|g" /db.sql/db.sql \ 
    && sed -i "s|{MOODLE-USER-PASSWORD-JOSEPABLO}|${MOODLE_USER_PASSWORD_JOSEPABLO}|g" /db.sql/db.sql \ 
    && echo "CREATE DATABASE ${MOODLE_DB_NAME};\r\n" > /db.sql/db-final.sql \
    && echo "CREATE USER '${MOODLE_DB_USER}'@'%' IDENTIFIED WITH mysql_native_password BY '${MOODLE_DB_USER_PW}';\r\n" >> /db.sql/db-final.sql \
    && echo "GRANT ALL PRIVILEGES ON ${MOODLE_DB_NAME}.* TO '${MOODLE_DB_USER}'@'%';\r\n" >> /db.sql/db-final.sql \
    && echo "SET time_zone = '-6:00';" >> /db.sql/db-final.sql \
    && echo "USE ${MOODLE_DB_NAME};\r\n" >> /db.sql/db-final.sql \
    && cat /db.sql/db.sql >> /db.sql/db-final.sql \
    && mv /db.sql/db-final.sql /docker-entrypoint-initdb.d/ \
    && rm /db.sql -R

VOLUME /var/lib/mysql

EXPOSE 3306

CMD ["mysqld","--user","mysql"]