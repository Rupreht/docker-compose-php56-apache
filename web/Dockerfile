FROM nginx:alpine

ADD nginx.conf /etc/nginx/nginx.conf
ADD mime.types /etc/nginx/mime.types

RUN mkdir -p /var/www/html/static /var/www/html/media

#RUN chown -R nginx:nginx /media
#RUN chown -R nginx:nginx /static

WORKDIR /var/www/html
