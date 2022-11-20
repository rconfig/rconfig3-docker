<!-- References:
https://www.twilio.com/blog/get-started-docker-laravel
https://laravel-for-newbie.kejyun.com/en/advanced/scheduling/docker/
https://github.com/mohammadain/laravel-docker-cron/blob/master/Dockerfile -->

<!-- Improved compatibility of back to top link: See: https://github.com/othneildrew/Best-README-Template/pull/73 -->

<a name="readme-top"></a>

<!-- PROJECT LOGO -->
<br />
<div align="center">
  <a href="https://github.com/rconfig/rconfig3-docker">
    <img src="https://www.rconfig.com/images/new_logos/red_logos/artwork_red_horizontalArtboard_1_96px.png" alt="Logo" >
  </a>

  <h3 align="center">rConfig v3 Docker Compose Repository</h3>

  <p align="center">
    A repo for to setup containers for the purpose of running rConfig 3 in Docker.
    <br />
    <a href="https://www.rconfig.com/docs"><strong>Explore the docs »</strong></a>
    <br />
    <br />
    <a href="https://github.com/rconfig/rconfig3-docker/#intro">Intro</a>
    ·
    <a href="https://github.com/rconfig/rconfig3-docker/#setup">Installation</a>
    ·
    <a href="https://github.com/rconfig/rconfig3-docker/#usage">Usage</a>
    ·
    <a href="https://github.com/rconfig/rconfig3-docker/#contributing">Contributing</a>
    ·
    <a href="https://github.com/rconfig/rconfig3-docker/#license">License</a>
    ·
    <a href="https://github.com/rconfig/rconfig3-docker/#support">Support</a>
  </p>
</div>

<!-- Intro -->

<a name="intro"></a>

## Intro

rConfig v3 is our free open source Network Configuration Management (NCM) software package with superior NCM features and capabilities to help you easily manage configurations on small and lab based heterogenous networks.

rConfig v6 is our flagship professional version of rConfig aimed at high value networks and business operations. rConfig v6 runs natively on many variants of Linux. Within this repo, we have developed docker compose files and related artifacts to allow our customers run rConfig v6 within a Docker environment.

Supported OS for the docker files;

- Rocky Linux 8.4+
- RHEL Linux 8.4+
- CentOS Linux 8.4+

These scripts are not tested on Docker Desktop or Docker for Windows at the time of this commit.

Of course, you may download or clone these files, and edit as you see fit.

Check out our v3 docs `help.rconfig.com` to learn more.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- Installation -->

<a name="setup"></a>

## Installation

We have made it super easy to get started with rConfig v3 on Docker. Follow the steps below to get started.

### Prerequisites

Some prerequisites are needed before you get started.

- OS
  - Any latest version of CentOS, RHEL, Rocky or Ubunutu
- Docker
  - Any latest version of Docker or Docker-CE
  - Any latest version of Docker Compose

Your OS will need `git` installed to clone this repo. Internet access for the host VM is assumed also - at least to github and rconfig.com.

#### Useful URLS

- [Docker installation for CentOS/ Rocky](https://docs.docker.com/engine/install/centos/)
- [Manual Docker Compose installation for CentOS/ Rocky](https://docs.docker.com/compose/install/other/) (useful incase previous does not install it )

### Installation

1. Clone this repository to a directory of your choosing.

   ```sh
   cd /var/www/html
   git clone https://github.com/rconfig/rconfig3-docker.git
   ```

2. Create top level .env file

   ```sh
   cd rconfig3-docker
   cp .env.example .env
   ```

3. Edit the .env file with your DB and other parameters

   ```sh
   vim .env
   ```

   ```sh
   MYSQL_USER=root
   MYSQL_ROOT_PASSWORD=
   MYSQL_PASSWORD=
   MYSQL_DATABASE=tmpdatabase
   MYSQL_PORT=3306

   #EXPOSED PORTS
   EXPOSED_APP_PORT=8080
   EXPOSED_APP_HTTPS_PORT=4443
   EXPOSED_DB_PORT=3307

   ```

If you are uncertain about any of the above, the most important items to change are the MYSQL_ROOT_PASSWORD, DB_PASSWORD and EXPOSED_APP_PORT & EXPOSED_APP_HTTPS_PORT if they are already exposed on your host.

4. clone the rconfig applications files to the src directory

   ```sh
   cd src
   git clone https://github.com/rconfig/rconfig.git
   ```

5. Bring up the containers. This time we remove the Horizon container from the build as the application init is not complete so will throw errors. (This will take a few minutes the first time)

   ```sh
   docker-compose up -d --build
   ```

6. Once the containers are up and running with no errors from the previous output we can follow the setup wizard to complete the installation. Navigate to http://yourhost:8080/install and follow the steps. The `Database Server` input field on the `Database Setup` page must always be `mariadb`.
   Note: if the DB installation does not work, login to the php-apache container and apply the correct permissions per below.

   ```sh
   docker-compose exec php-apache /bin/bash
   chwon -R 33 /home/rconfig
   ```

7. When the setup wizard has completed successfully, login to the app by https. https://yourhostname:8443/login.php. You will encounter an SSL error, and you can skip this. It is a

8. Once the installation completes with no errors, exit the container, bring everything down, and start all containers up. You should hit the login page again to validate everything is working as expected.

   ```sh
   exit
   docker-compose down
   docker-compose up -d --build
   ```

9. your final output from the previous step should look like this

```sh
Creating rconfig3-mariadb ... done
Creating rconfig3-php-apache ... done
```

When troubleshooting issues, the following commands are useful.
`docker ps -a`
`docker logs CONTAINERNAME`

#### Disk or storage permissions issues

If you have permissions issues, such as errors that rConfig cannot write to the storage directory, follow these steps.

1. Login to the php-apache container

   ```sh
   docker-compose exec php-apache /bin/bash
   ```

2. View the users list and find the id for the `www-data` account

   ```sh
   more /etc/passwd | grep www
   ```

3. The output should be similar to this

   ```sh
   www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
   ```

4. Simply apply the correct permissions to the `/home/rconfig/` directory
   ```sh
   chown -R 33 /home/rconfig/
   ```

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- USAGE EXAMPLES -->

<a name="usage"></a>

## Usage

The default port for the web app is 8080. So go ahead and login to http://yourhostname.domain.com:8080/install with the rConfig default creds per the documentation for install wizard. When the install is complete the login url will be https://yourhostname.domain.com:8443/login.php

_Please refer to the [Documentation](help.rconfig.com)_

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- CONTRIBUTING -->

<a name="contributing"></a>

## Contributing

Contributions are what make the open source community such an amazing place to learn, inspire, and create. Any contributions you make are **greatly appreciated**.

If you have a suggestion that would make this better, please fork the repo and create a pull request. You can also simply open an issue with the tag "enhancement".
Don't forget to give the project a star! Thanks again!

1. Fork the Project
2. Create your Feature Branch (`git checkout -b feature/AmazingFeature`)
3. Commit your Changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the Branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- LICENSE -->

<a name="license"></a>

## License

This code base for this repository's code is distributed under the MIT License. See `LICENSE.txt` for more information. rConfig v6 is excluded from this license and repository.

<p align="right">(<a href="#readme-top">back to top</a>)</p>

<!-- https://github.com/othneildrew/Best-README-Template/blob/master/README.md -->

<a name="support"></a>

## Support

Although we provide this code free and open source, rConfig v3 is only supported with a gold subscription from rConfig.com. You may raise issues in this or the main rConfig repository, but our support teams do not monitor those for support requests.
