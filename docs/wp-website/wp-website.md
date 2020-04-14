# Steps:

1. Install PHP: `brew install php`

2. Install XAMPP: https://www.apachefriends.org/index.html

3. Setup XAMPP:


- Start XAMPP from tab `General`
![xamp_1]

- Start all services from tab `Services` (Apache, MySQL, ProFTPD)
![xamp_2]

- Enable `localhost:8080` from tab `Network`
![xamp_3]

- `Mount` ` /opt/lampp ` from tab `Volumnes`
![xamp_4]

- Open `/opt/lampp/` by clicking on `Explore` in `Volumnes`. In MacOS, full path might be in `~/.bitnami/stackman/machines/xampp/volumes/root`
  
- Clone `wp-website` repo into `/opt/lampp/htdocs`, remember name of cloned folder

- Change name content of `wp-config-sample.php` according to repo's README

- Create `.htaccess` in `/opt/lampp/{cloned_repo}` with content from repo's README. Change path to match `{cloned_repo}`. For example: `RewriteBase /{cloned_repo}/`

- Routes: 
  - Access Database Management Tool at `http://localhost:8080/phpmyadmin/`, 
  - Admin at `http://localhost:8080/wordpress/wp-admin` 
  - Wordpress site at `http://localhost:8080/wordpress/` (can be changed in wordpress site settings)

<!-- Images Reference -->
[xamp_1]: ./images/xampp_1_general.png "XAMPP General" 
[xamp_2]: ./images/xampp_2_services.png "XAMPP Services"
[xamp_3]: ./images/xampp_3_network.png "XAMPP Network"
[xamp_4]: ./images/xampp_4_volumnes.png "XAMPP Volumnes"
