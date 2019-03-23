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

5. (optional) If you need to install any other custom modules, e.g. from the odoo community (OCA), then adjust the files `odoo/custom/src/repos.yaml` and `odoo/custom/src/addons.yaml`. Check out [the original doc](https://github.com/Tecnativa/doodba#optodoocustomsrcreposyaml) for more help and some examples.

6. Build your development setup.

       docker-compose -f setup-devel.yaml run --rm odoo

7. Create a link of the development docker-compose file `devel.yaml` to make it your default docker-compose file.

       ln -s devel.yaml docker-compose.yml

8. Clone the boxwise repos you need into `odoo/custom/src/private/`.

9. Start your development environment.

       docker-compose up
       
10. (optional) If you need to print / download pdfs you have to change the system parameter `web.base.url`. Docker is putting a network layer on top. Because of that odoo is assuming the wrong network adress of itself and cannot find the pdf converter when you want to download reports. To solve this problem do the following:

    10.1 Run the command (The command only works if the server is running and if you have not renamed the git repo of boxwise-doodba. In that case you have to adjust the name of the odoo docker container in the command.)

        echo http://$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' boxwise-doodba_odoo_1):8069

    10.2 Save the result in the system parameter `ẁeb.base.url` through the odoo interface. Therefore, go to the menu
    
        Settings / Technical / Parameters / System Parameters

## Default

### URL / Port for 11.0

    localhost:11069 or 127.0.0.1:11069

### Login credentials

    email: admin
    password: admin
    
## Troubleshooting

### access to database manager is not granted

Tecnativa [updated their odoo docker image recently which breaks the access to the database manager](https://github.com/Tecnativa/doodba/issues/67#issuecomment-467339077). A issue is filed and they are on it to solve it. Until then, you can create a new database by changing the fields `DOODBA_ENVIRONMENT` and `PGDATABASE` in `devel.yaml`:

        environment:
            DOODBA_ENVIRONMENT: "${DOODBA_ENVIRONMENT-<my new database>}"
            PTVSD_ENABLE: "${DOODBA_PTVSD_ENABLE:-0}"
            PGDATABASE: &dbname <my new database>

In your browser you can log into your new database by typing:

        localhost:11069/web?db=<my new database>

## Quick ref

Feel free to add useful commands you found in this section.

Some section titles are links, too.

### Restarting the servers

- If you changed CSS/JS press F5
- If you changed the contents of a preexisting view in a preexisting XML file, on Odoo v9+: F5
- If you changed some python code that doesn't involve database modifications: 

        docker-compose restart odoo odoo_proxy
- Otherwise (add/rm data to XML files, add/rm XML files, change model definitions): 

        docker-compose run --rm odoo addons update -w my_addon && docker-compose restart odoo odoo_proxy

### Install OCA addons

Repeat the steps 4.), 5.) and 6.) of the [installation](https://github.com/boxwise/boxwise-doodba#installation)!

### [wdb](https://github.com/Tecnativa/doodba#wdb)

To put breakpoints in your code, put this in your python scrict:

    import wdb
    wdb.set_trace()

To acces wdb, browse http://localhost:1984

### odoo-shell

To start the odoo-shell run

    docker-compose run --rm odoo odoo shell
    
If you want to start the odoo-shell hooked up to another database than the doodba default, run

    docker-compose run --rm odoo odoo shell -d <your_database>
    
### Create, duplicate, delete databases from odoo-shell

Open odoo shell and import the following 

        from openerp.service import db
        
Here, some useful commands

        #help(db)
        db.list_dbs()
        #Prove list of avalibale db
        db.exp_drop('dbname')
        #Drop an existing db
        db.exp_duplicate_database('olddb','newdb')
        #Duplicate an  existing db
        db.exp_create_database('dbname',None,'en_US','username','password')
        #Create a new  db

### [Testing](https://github.com/Tecnativa/doodba#testing)

### VSCode optimization

To optimize all VSCode run the setup script in .vscode!
