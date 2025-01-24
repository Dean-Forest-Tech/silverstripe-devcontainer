FROM nginx:stable

# Remove and re-create nginx user and map to local system ID
ARG USER_ID
ARG GROUP_ID

RUN if [ ${USER_ID:-0} -ne 0 ] && [ ${GROUP_ID:-0} -ne 0 ]; then \
    userdel -f nginx &&\
    if getent group nginx ; then groupdel nginx; fi &&\
    groupadd -g ${GROUP_ID} nginx &&\
    useradd -l -u ${USER_ID} -g nginx nginx &&\
    install -d -m 0755 -o nginx -g nginx /home/nginx &&\
    chown --changes --silent --no-dereference --recursive \
          --from=101:101 ${USER_ID}:${GROUP_ID} \
        /home/nginx \
;fi

RUN apt-get update -y && apt-get install -y \
    nano \
    net-tools \
	--no-install-recommends && \
	apt-get autoremove -y