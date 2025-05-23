<!-- markdownlint-disable first-line-h1 -->
<div align="center">

<a href="https://github.com/gabboman/wafrn">
  <img src="https://app.wafrn.net/assets/logo.png" alt="Wafrn logo" width="350"/>
</a>

**[Wafrn](https://github.com/gabboman/wafrn) &ndash; The social media that respects you.**

---

</div>

# Wafrn

Wafrn is an open source social network that connects with the Fediverse. The frontend is Tumblr-inspired.

The "main" wafrn app is located at [app.wafrn.net](https://app.wafrn.net).

- [Project structure](#project-structure)
- [Host Wafrn yourself](#host-wafrn-yourself)
  - [What will you need](#what-will-you-need)
  - [First steps](#first-steps)
  - [Populate database](#populate-database)
  - [Update wafrn](#update-wafrn)
- [Contributing](#contributing)
- [License](#license)

## Project Structure

Wafrn is split between an [Angular](https://angular.dev) frontend and a [NodeJS](https://nodejs.org/en) backend.

```text
packages/
├── frontend/
│   ├── routes/
│   ├── util/
│   ├── README.md
│   └── ...
└── backend/
    ├── src/
    │   ├── app/
    │   ├── assets/
    │   └── ...
    ├── README.md
    └── ...
```

(Tree made with [tree.nathanfriend.io](https://tree.nathanfriend.io/))

## Host Wafrn Yourself

> [!NOTE]  
> THIS GUIDE NEEDS UPDATING. IT WILL GET UPDATED SOON. SORRY

<details>

<summary>If you're unhappy with my moderation style or you would like to host your own stuff, you can host your own version.</summary>

### What will you need

Before trying to host your own wafrn, we advise you to please, very please, [join our discord channel](https://discord.gg/EXpCBpvM) to get support

Next decide if you wish to install and run wafrn natively or using docker

### Native setup

First, you will need a Debian 12 VPS. The cheap Contabo one can do the trick with no problem. Maybe even the OVH one that costs 3 euros too. But I advise as a minimum the Contabo one.
You also need a domain.
You will also need a way of sending emails to the people registering. An SMTP server or a free Brevo account with SMTP enabled can do the trick.

#### First steps

First, point the domain to your Debian VPS. Once that is done, we download the installer and execute it, as root.
The installer will install all required packages, create the user and clone the repo and configure Apache.

**DO NOT PRESS ENTER BLINDLY DURING THE INSTALL PROCESS**, as it will ask some stuff and my bash-fu is not that good

Remember, run this as root!

```bash
wget https://raw.githubusercontent.com/gabboman/wafrn/main/install/installer.sh
bash installer.sh
```

This script will download all requirements and will create a user in your system.

Follow the instructions of the script. It will leave the system ready with wafrn installed, the frontend deployed and the server ready to start. You're almost there!

#### Populate database

Ok, we have the stuff ready. Log in as the user we just created (it has asked it during the previous script)

Now we will edit the backend environment file

```bash
cd wafrn
nano packages/backend/environment.ts
```

There is also an option called `adminPassword`. You can edit it too and set the admin password. In this state, it should be a random password.

Once we have edited the environment file, we can do the first start of the backend!

```bash
# We execute this command in the root of the project, in the wafrn folder.
pm2 start --name wafrn start.sh
```

After this, we need to set the `forcesync` to false in the previous file, and to delete the password from the environment.ts file

This step is VERY IMPORTANT. Without it, it will DESTROY YOUR DATABASE every time wafrn starts!

```bash
nano packages/backend/environment.ts
# forceSync: true -> forceSync: false
```

Now that we have the database ready and the environment ready, we register the workers with pm2

```bash
pm2 start --name workers -i max script_workers.sh
pm2 save
pm2 startup
```

This last command asks you to run something as root. Do it, so when the server restarts wafrn will also start.

You're ready!

**Remember, remove the admin password from the environment.ts in the backend package!**

#### Update wafrn

To update wafrn, you just do the command `npm run full:upgrade` in the wafrn folder.

This will do a pull the latest changes and keep the waffle up to date

### Docker

To get wafrn running under docker we have provided a shiny `docker-compose.yml` file. The steps below have been tested on an Ubuntu machine with Docker installed via the official install scripts.

#### Checkout project

You'll need to get the project files ready in a directory of your choice. 

```sh
git clone https://github.com/gabboman/wafrn
cd wafrn
```

Should do the trick

#### Configure environment

Create your own `.env` file by using the example provided:

```sh
cp .env.example .env
```

Next you'll need to fill in all of the details of your domain. For example if you're trying to run your website under `wafrn.example.com` (and your DNS is already pointing to the computer running docker) you'll need to update the following details:

```sh
DOMAINNAME=wafrn.example.com
BACKEND_URL=wafrn.example.com
FRONTEND_URL=wafrn.example.com
SERVER_NAME=wafrn.example.com
CACHE_URL=wafrn.example.com
MEDIA_URL=wafrn.example.com
```

Also make sure to get your SMTP settings, your `ACME_EMAIL` and anything that looks like a password in the config changed from the default.

#### Run

Next to run the setup just call

```
docker compose build && docker compose up
```

Once the scripts run and everything is okay you should be able to access your website at `https://wafrn.example.com`

</details>

## Contributing

If you would like to help develop the Frontend or Backend, read the README.md of the respective package.

- [Frontend - README.md](./packages/frontend/README.md)
- [Backend - README.md](./packages/backend/README.md)

## License

The frontend uses [Apache License 2.0](https://choosealicense.com/licenses/apache-2.0/).

The backend uses [GNU AGPLv3](https://choosealicense.com/licenses/agpl-3.0/)
