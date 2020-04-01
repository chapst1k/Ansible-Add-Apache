This is an ansible playbook that will allow you to add domains to the AWS shared Ohio server.

## Usage

**The below steps assume you have installed ansible. See:**
https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html

1. Clone the code to your repository.
2. Open your terminal and `cd` into the cloned repository.
3. Run the following command:
`ansible-playbook -i inventory/inventory-live.yml playbooks/setup-domain.yml`
4. Follow the prompts:
  - Enter the username: {{ Typically, everything before the .tld ('example' for the domain 'example.com') }}
  - Enter the password: {{ Type the password you would like this user to have }}
  - Confirm the password: {{ Re-type the password }}
  - Enter the database name: {{ db_username_tld }}
  - Enter the database username: {{ dbu_username }} or {{ username }}
  - Enter the database password: {{ randomly generated password, preferably something hard }}
  - Enter the domain name (without the www): {{ example.com for 'example.com' and 'www.example.com' }}
5. Once the prompts above have been entered, the playbook will run and complete the following tasks:
  - Add a user to the system and provide that user with the password you provided.
  - Add the user to the SFTP group to allow for SFTP connections.
  - Create an Apache virtual host using the file from 'playbooks/templates/vhost.conf.js' as the template.
  - Create a database / database user / database password with the prompted variables.
  - Restart Apache.
6. See the file 'setup-domain.yml' for all of the steps. The code is commented pretty well and will provide a better understanding.

**For a quick site, the above will be all you need. However, some manual steps are required for SSL/git access for the user/**

## Let's encrypt:
Let's encrypt will only work once the site is live and accessible via the web. Once that happens:
1. SSH into the server
2. Type the following command, being sure to change the domain name and path accordingly:
`sudo certbot --authenticator webroot --installer apache --domains example.com,www.example.com --webroot-path=/home/example/example.com/web`
3. The certbot program will typically ask if you would like to redirect to https. Enter no at the prompt and we'll do that manually.
4. Next, edit the Apache virtual host for the domain with `sudo nano /etc/apache2/sites-available/example.com.conf`
5. Uncomment lines 21, 23, 24 and save the file `ctrl-x` enter `y` at the prompt and press `enter`.
6. Lastly, graceully restart apache with `sudo service apache2 graceful`. If no errors arise, the site will redirect to the https version automatically.

## Git access
By default, the SFTP access from the playbook will lock the SFTP connection to the user's home folder. In order to allow for a git clone to work, we need to lighten the permissions.

**Note, for Drupal 8, your webroot will change. You will need to edit the Apache vhost to account for this change.**
- `sudo nano /etc/apache2/sites-available/example.com.conf` and `sudo nano /etc/apache2/sites-available/example.com-le-ssl.conf`
- Anywhere you see '/home/example/httpdocs' will need changed to '/home/example/example.com/web'
- Restart apache `sudo service apache2 graceful`

## Fixing file permissions

#### Drupal
1. SSH into the server
2. Run the command: `sudo ./fix-permissions.sh --drupal_user=example --drupal_path=/home/example/example.com/web`

#### Wordpress
1. SSH into the server
2. Run the command: `sudo ./wp-permissions-script-less-secure --wp_user=www-data --wp_path=/home/example/httpdocs --httpd_group=example`

## MySQL Import
1. `mysql -u example -p db_example_com < example.sql`

## MySQL Export
1. `mysqldump -u example -p db_example_com > example.sql`
2. `gzip example.sql`
3. If you would like to use the file with ddev pull, export it with this command (just a name change):
`mysqldump -u example -p db_example_com > db-"$(date '+%F')T$(date '+%H-%M-%S')_$(date '+%Z')".sql`

## Files directory export
1. To save files for ddev pull execution run the following command:
`sudo tar -zcvf files-"$(date '+%F')T$(date '+%H-%M-%S')_$(date '+%Z')".tar.gz -C /home/example/example.com/web/sites/default/files/ .`

## DDEV Pull
To make files / databases available for `ddev pull` see MySQL Export and Files directory export above. Then, you will download those files and upload them into the 'drud-midnetmedia-hosting' S3 bucket. In S3, you'll need to create a directory named for the project, typically the domain name. Inside of that directory, you'll need to create a 'staging' and a 'production' directory to which you'll upload the two export files from earlier.
