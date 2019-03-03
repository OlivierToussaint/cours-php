## Installation 

Avant de commercer les tp, il faut avoir php d'installer sur sa machine, pour tester cela nous allons demander la version de php sur notre machine.

```bash
# php -v
PHP 7.3.2 (cli) (built: Feb 14 2019 10:08:45) ( NTS )
Copyright (c) 1997-2018 The PHP Group
Zend Engine v3.3.2, Copyright (c) 1998-2018 Zend Technologies
    with Zend OPcache v7.3.2, Copyright (c) 1999-2018, by Zend Technologies
```

Si jamais vous n'avez aucune version de php installer sur votre machine, voici différent conseil.

Sur mac l'installer avec brew : https://brew.sh/index_fr

```bash
# brew install php
```

Sur windows suivez le guide : http://php.net/manual/fr/install.windows.php

Vous pouvez aussi utilise xampp.


## Lancer le server interne

Nous allons créer un repertoire tp puis à l'intérieur de celui ci nous allons crer un fichier nommer `index.php`.

```php
<?php
  echo "Hello World!";
?>
```

Pour interpréter le php nous allons utiliser le server interne de php à l'intérieur de notre dossier nous allons lancer la ligne de commande

```bash
# php -S localhost:8080
```

Puis lancer votre navigateur (Firefox ou Chrome) et rentrer cette adresse localhost:8080 

Sur votre navigateur nous avons un :

```
Hello World!
```

Vous venez de faire votre premier fichier php et vous l'avez éxécuté.

Si nous voulons l'intégrer dans du html il suffira de faire

```php
<!DOCTYPE html>
<html>
    <head>
        <title>Welcome</title>
    </head>
    <body>
      <?php echo "Hello World!"; ?>
    </body>
</html>
```

Voici notre première page web !