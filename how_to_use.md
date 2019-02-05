# How to use Docker4Drupal with custom codebase
This explanation is intended for those Drupal installation based in [custom source code](https://wodby.com/docs/stacks/drupal/local/#mount-my-codebase). For this, its going to be required a database dump and a source code directory (generated with the [drush archive-dump](https://drushcommands.com/drush-8x/core/archive-dump/) command).

> The 'drush archive-dump' does a _backup your code, files, and database into a single file_, a compressed *.tgz* file. This .tgz file mainly contains (1) a directory with the Drupal instance source code (containing the content from the **"/var/www/html/web/"** directory in server), (2) a SQL file with a database dump.

Let's call the dumpfile as $DRUPAL_DMP.

In order to use your data with this docker project, you must do the followings steps:
1. Make a git clone of the project in $D4D_DIR.
2. Copy your **web/** codebase in "data/" project directory.
```bash
cp $DRUPAL_DMP/web $D4D_DIR/data
```
3. Copy your SQL dump file in "mariadb-init" directory.
```bash
cp $DRUPAL_DMP/$DB_DUMP.sql $D4D_DIR/mariadb-init
```
4. Modify the variables in the **$D4D_DIR/.env** file.
    * The values of DB_NAME, DB_USER, and DB_PASSWORD variables must be the same as in specified in the _$D4D_DIR/data/web/sites/default/settings.php_.
    * Optionally, you may modify the PROJECT_NAME and PROJECT_BASE_URL variables.
5. Now you can create the Drupal containers (using the make target defined at *docker.mk* file).
```bash
make up
```
6. Now you should be able to access to your Drupal application at http://drupal.docker.localhost:8000 location.
   * If you cannot, you must configure a new host mapping at your */etc/hosts* system file (in order with Drupal documentation at https://wodby.com/docs/stacks/drupal/local/#domains).
   ```bash
   127.0.0.1    drupal.docker.localhost
   ```
   * This docker project is shipped with more services. In example, there is a [Portainer](https://www.portainer.io/) service accesible at http://portainer.docker.localhost:8000. You must modify the /etc/hosts file in order to use it too.
   

## Vinculate services to an existing network

If you want to make this services reachables from another network, you can specify the name of this network on the **DOCKER_EXTERNAL_NETWORK_NAME** environment variable (specified in the *.env* file).
Then you must uncomment the following lines in the *docker-compose.yml* file:
```bashnetworks:
### Configure this option if you want to add this services to an external network...
networks:
  default:
    external:
      name: ${DOCKER_EXTERNAL_NETWORK_NAME}
```
