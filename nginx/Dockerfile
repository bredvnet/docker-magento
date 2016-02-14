FROM nginx:1.9.11
MAINTAINER Björn Rennfanz <bjoern.rennfanz@br-edv.net>

RUN usermod -u 1000 www-data
COPY nginx-magento.conf /etc/nginx/conf.d/default.conf
CMD ["nginx", "-g", "daemon off;"]
