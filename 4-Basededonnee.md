## Base de donnée avec mysql/mariaDb

Vous pouvez rencontrer dans ancien projet le driver mysqli/
<http://php.net/manual/fr/book.mysqli.php>

Voici un exemple : 

```php
$mysqli = new mysqli("localhost", "user", "password", "database");
if ($mysqli->connect_errno) {
    echo "Echec lors de la connexion à MySQL : (" . $mysqli->connect_errno . ") " . $mysqli->connect_error;
}

if (!$mysqli->query("DROP TABLE IF EXISTS test") ||
    !$mysqli->query("CREATE TABLE test(id INT)") ||
    !$mysqli->query("INSERT INTO test(id) VALUES (1), (2), (3)")) {
    echo "Echec lors de la création de la table : (" . $mysqli->errno . ") " . $mysqli->error;
}
```

Pour la suite nous utiliserons PDO 
<http://php.net/manual/fr/book.pdo.php>

Pour se connecter à notre base de donnée nous allons utiliser plusieurs choses

```
$db = new PDO('mysql:host=localhost;dbname=test', $user, $pass);
```

- Le host : La plus part du temps se sera localhost car la base de donnée est souvent installer sur notre serveur php
- dbname : Le nom de notre base de donnée que l'on aura créer en amont (soit en ligne de commande soit par phpmyadmin avec xampp)
- user : L'utilisateur pour se connecter à la base de donnée, ce ne doit être en aucun cas root
- pass : Le mot de passe de l'utilisateur donnée juste au dessus

## Mise en place de notre base

Il y aura trois facons d'administrer notre base :

- En ligne de commande, pas besoin d'autre outils
- Avec phpmyadmin, installer par defaut avec xampp
- Avec le logiciel libre Sequel Pro

Une fois dans notre outils, nous allons créer une base, que l'on nommera `tp` puis créer un nouveau utilisateur qui aura tout les droits sur cette base. Dans notre cas, je vais créer un utilisateur qui se nommer `tp` avec un mot de passe complexe.

Une fois ces choses faites, je vais créer une table nommé `post` avec comme columns `title`, `content`

---

Je vais mettre toute ces informations dans un fichier nommer `configuration.php`

Dedans nous allons mettre un tableau avec nos différentes configurations :

```php
$database = [
	'host' => 'localhost',
	'base' => 'tp',
	'user' => 'tp',
	'password' => 'dfqsdsdqf234'
	];
	
$instance = new PDO("mysql:host=".$database['host'].";dbname=".$database['base'],$database['user'],$database['password']);

```
Grace à la variable $instance nous allons pouvoir faire des requêtes sur notre base de donnée

### Le SELECT

Pour un `SELECT` nous allons donc procédé de cette manière :

```php
$reponse = $instance->query('SELECT * FROM article');

// On affiche chaque entrée une à une
while ($donnees = $reponse->fetch())
{
	echo $donnees['title'] . '<br>' . $donnees['content'] . '<br>';
}
```

On pourra faire des recherches sur les données avec `WHERE`

```php
$title = $_GET['title'];
$reponse = $instance->query('SELECT * FROM article WHERE title=' . $title);

// On affiche chaque entrée une à une
while ($donnees = $reponse->fetch())
{
	echo $donnees['title'] . '<br>' . $donnees['content'] . '<br>';
}
```

L'inconvéniant de cette méthode et qu'elle permet les injections SQL, pour plus de renseignement je vous mets un article wikipédia.
<https://fr.wikipedia.org/wiki/Injection_SQL>

Pour éviter ce problème, nous allons passer au requete préparer.

```php
$reponse = $instance->prepare('SELECT * FROM article WHERE title = : title');
$reponse->execute(array('title' => $_GET['title']));
```

### L'INSERT

Pour insérer un article, on va utiliser la même chose.

```php
$reponse = $instance->prepare('INSERT INTO article (title, content) VALUES(:title, :content)');
$reponse->execute([
	'title' => $title,
	'content' => $content,
]);
```

### UPDATE

Et voici l'exemple pour l'update

```php
$reponse = $instance->prepare('UPDATE article SET title = :title, content = :content WHERE id = :id');
$req->execute([
	'title' => $title,
	'content' => $content,
	'id' => $id
]);
```


## Cas pratique avec le html

Nous allons insérer des données dans notre base

Pour ce faire nous allons créer un formulaire

```html
       <form method="post">
           <div class="row">
               <div class="col">
                   <label>Titre :</label>
                   <input type="text" name="title">
               </div>
           </div>
           <div class="row">
               <div class="col">
                   <label>Content</label>
                    <textarea name="content"></textarea>
               </div>
           </div>
           <button>Ajouter</button>
       </form>
```

Il y plusieurs choses importantes :

La balise form, elle déclare notre formulaire, puis nous lui définisons la méthode de transmission des données (ici POST, par défaut c'est GET).

On aura pu aussi rajouter une action, si nous ne spécifions rien, le formulaire valider va envoyer les données sur la même page.

Si on souhaite traiter les données dans une page différente, nous aurions écrit : ```<form action="response.php">```.

La deuxième chose importante sont les ```name``` de nos différents champs, ici un input et un textarea. C'est sont nos variables que nous allons récupérer en ```$_POST``` une fois le formulaire soumis.

Puis nous allons passer au traitement de nos données, nous allons les poussés dans notre table.

Ce traitement sera placé en haut de notre code

```php
if (isset($_POST['title']) && isset($_POST['content'])) {
    try {
        $instance = new PDO("mysql:host=localhost;dbname=tp", 'tp', 'secret');
        $instance->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);
    } catch (PDOException $e) {
        echo 'Échec lors de la connexion : ' . $e->getMessage();
    }
    $reponse = $instance->prepare('INSERT INTO article (title, content) VALUES(: title, :content)');
    $reponse->execute([
        'title' => $_POST['title'],
        'content' => $_POST['content'],
    ]);
    $message =  "Article bien ajouté";
}
```

Ici nous testons si nos deux valeurs (title, content) sont initialisés par le formulaire, si c'est le cas, on fait la connection à notre base de donnée et on insert nos données en base.

Voici le fichier final <https://github.com/OlivierToussaint/tp-3il/blob/premiercontactavecpdo/article_add.php>


