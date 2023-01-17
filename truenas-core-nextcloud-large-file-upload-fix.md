By default the maximum file size for upload is 512MB which could be very limiting. I happened to have a 15GB video file on my phone (recording was turned on unintentionally and the video ran for 2+ hours) and that's why I started digging around for a work-around. The nextcloud plugin I am running on my Truenas Core (13) is 25.0.2. Here are the steps:

While nextcloud is running,

1. open `/usr/local/etc/nginx/conf.d/nextcloud.inc`
and change `client_max_body_size` to the desired value
```
client_max_body_size 20G;
```
2. `service nginx restart``

3. open `/usr/local/etc/php/php.truenas.ini` and change the following values in [PHP] section to the same value as step 1
```
post_max_size=20G
upload_max_filesize=20G
```
4. `service php-fpm restart`