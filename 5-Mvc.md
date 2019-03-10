Les fichiers d'exemple : <https://github.com/OlivierToussaint/tp-3il/tree/mvc>

D'autres exemples et vision différentes du MVC : 
<https://openclassrooms.com/fr/courses/4670706-adoptez-une-architecture-mvc-en-php>
<https://fr.wikibooks.org/wiki/Programmation_PHP/Exemples/MVC>

## MVC

Modèle - Vue - Controller

- Le modèle contientra la partie donnée
- La vue représentera tout la partie graphique
- Le controlleur fera l'intermédière entre le modèle et la vue

Pourquoi ?

Le fait de découper votre code va permettre plus de modularité et de séparer les roles (on tentera plus tard : un fichier = un role).

Il permettra de dégager la partie Front du reste. Un developpeur Front pourra faire son travail sur les vues, alors que le developpeur Backend pourra avancer sur le traitement des données.

Nous allons partir sur quelques choses de simple pour que les choses soit clair, dans un second temps nous refactererons notre compte pour mettre en place un autoloader de class + les namespaces.

### Partie Modèle

```php
class Article
{
    protected $id;

    protected $title;

    protected $content;

    public static function load($id, $title, $content)
    {
        $article = new self();
        $article->setId($id);
        $article->setTitle($title);
        $article->setContent($content);
        return $article;
    }

    public function getId(): int
    {
        return $this->id;
    }

    private function setId(int $id): self
    {
        $this->id = $id;
        return $this;
    }

    public function getTitle()
    {
        return $this->title;
    }

    public function setTitle(string $title): self
    {
        $this->title = $title;
        return $this;
    }

    public function getContent()
    {
        return $this->content;
    }

    public function setContent(string $content): self
    {
        $this->content = $content;
        return $this;
    }

}
```

Notre modèle est un object simple que nous allons hydrater avec le fichier associer `ArticleRepository`

```php
require_once __DIR__ . '/../model/Article.php';
require_once __DIR__ . '/../model/RepositoryInterface.php';
require_once __DIR__ . '/../model/Repository.php';

class ArticleRepository implements RepositoryInterface
{

    public static function find(int $id): ?Article
    {
        $baseManager = Repository::connect();

        $response = $baseManager->fetch('SELECT * FROM Article WHERE id= :id', [
            'id' => $id
        ]);
        $article = Article::load($response['id'], $response['title'], $response['content']);
        return $article;
    }
    public static function findAll(): array
    {
        $baseManager = Repository::connect();

        $listOfArticle = [];
        $responses =$baseManager->fetchAll('SELECT * FROM Article');
        foreach($responses as $response) {
            $listOfArticle[] = Article::load($response['id'], $response['title'], $response['content']);
        }
        return $listOfArticle;
    }
}
```

La fonction `find` va nous permettre de charger un Article en base et de d'hydrater avec la fonction load l'object `Article`

C'est dans notre controller que nous allons appeler cette fonction

### Partie Controller

Dans notre controller nous allons faire le liens entre la partie Modèle et la partie Vue

```php
require __DIR__.'/../model/Article.php';
require __DIR__.'/../model/ArticleRepository.php';

class ArticleController {
    public static function index() {
        $articles = ArticleRepository::findAll();
        require __DIR__.'/../vue/article_list.php';
    }
    public static function show($id) {
        $article = ArticleRepository::find($id);
        require __DIR__.'/../vue/article_show.php';
    }
    public static function add() {
        if (isset($_POST['title']) && isset($_POST['content'])) {
            $article = new Article();
            $article->setTitle($_POST['title']);
            $article->setContent($_POST['content']);
            ArticleRepository::add($article);
            $message = 'Votre article à bien été ajouter';
        }
        require __DIR__.'/../vue/article_add.php';
    }
}
```
Ici trois fonctions, nous allons nous interessés à `show()`

Dans cette partie la fonction show recois un $id et grace à cette $id nous allons l'afficher dans la vue article_show

```php
require __DIR__.'/common/header.php';
?>
<h1><?= $article->getTitle() ?></h1>
<p><?= $article->getContent() ?></p>
<a href="/index.php" class="btn btn-primary">Retour à la liste</a>

<?php
require __DIR__.'/common/footer.php';
```

Ici notre fonction show nous montre juste le titre de notre article avec son contenu.


### La partie public

Pour appeler nos différentes fonctions du controller, nous allons configurer notre index.php.

```php
require_once __DIR__ . '/../controler/ArticleController.php';

// route the request internally
$uri = parse_url($_SERVER['REQUEST_URI'], PHP_URL_PATH);
if ('/index.php' === $uri) {
    ArticleController::index();
} elseif ('/index.php/show' === $uri && isset($_GET['id'])) {
    ArticleController::show($_GET['id']);
} elseif ('/index.php/add' === $uri) {
    ArticleController::add();
} else {
header('HTTP/1.1 404 Not Found');
echo '<html><body><h1>Page Not Found</h1></body></html>';
}
```

Ici nous allons vérifier nos différentes routes et en fonction de celle ci, renvoyer vers la fonction du controller associé.