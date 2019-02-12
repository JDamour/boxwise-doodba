# Boxwise dev-setup

## What is doodba?

This is just a little customization of the Docker Odoo Base created by Tecnativa for Boxwise. The complete doc can be fount [here](https://github.com/Tecnativa/doodba#doodba)

## Why should you use it?

Within a few commands you can create use a lot of odoo dev tools, e.g. the python debugger [wdb](https://github.com/Kozea/wdb/#wdb---web-debugger) or the [odoo-shell](https://www.odoo.com/documentation/user/11.0/odoo_sh/advanced/containers.html#run-an-odoo-server).

## Installation

1. Clone this repo.
2. Enter the folder.

    cd boxwise-doodba

Step 3 and 4 are needed if the current user wants to have writing access.
3. Make sure that the folder odoo/auto has the right access rights.

    chown -R $USER:1000 odoo/auto
    chmod -R ug+rwX odoo/auto

4. Populate the id variables `UID`, `GID` and `UMASK`.

    export UID GID="$(id -g $USER)" UMASK="$(umask)"

5. Adjust the file `odoo/custom/src/repos.yaml` and `odoo/custom/src/addons.yaml`. Here, you can set if you need any extra modules and where to clone them from. Check out [the original doc](https://github.com/Tecnativa/doodba#optodoocustomsrcreposyaml) for more help and some examples.

6. Build your development setup.

    docker-compose -f setup-devel.yaml run --rm odoo

7. Create a link of the development docker-compose file `devel.yaml` to make it your default docker-compose file.

    ln -s devel.yaml docker-compose.yml

8. Clone the boxwise repos you need into `odoo/src/custom/private/`.

8. Start your development environment.

    docker-compose up

## Default

### URL / Port for 11.0

    localhost:11069 or 127.0.0.1:11069

### Login credentials

    email: admin
    password: admin

## Quick ref

Feel free to add useful commands you found in this section.

All section titles are links, too.

### [wdb](https://github.com/Tecnativa/doodba#wdb)

To put breakpoints in your code, put this in your python scrict:

    import wdb
    wdb.set_trace()

To acces wdb, browse http://localhost:1984

### odoo-shell

To start the odoo-shell run

    docker-compose run --rm odoo odoo shell


### [Testing](https://github.com/Tecnativa/doodba#testing)
