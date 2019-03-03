## Les fonctions

En php nous allons utiliser des fonctions des que nous avons un traitement à faire.

Pour le TP nous avons besoin de detecter si on est dans une page en cours.

Notre fonction :

```php
function isActive(array $menu)
{
    if (stripos($_SERVER["REQUEST_URI"],$menu['link']) ) {
        return true;
    }
    return false;
}
```
<http://php.net/manual/fr/reserved.variables.server.php> 

Nous allons utiliser la variable `$_SERVER` qui est un tableau contenant des informations sur les entêtes.

>'REQUEST_URI'
>L'URI qui a été fourni pour accéder à cette page. Par exemple : '/index.html'.

Vu que REQUEST_URI nous donne la variable avec le / devant, nous allons utiliser la function <http://php.net/manual/fr/function.stripos.php> pour detecter si on trouve la route qui se trouve dans le tableau.

Notre menu :

```php
<?php
$menus[] = ['link' =>'index.php', 'name' => 'Accueil'];
$menus[] = ['link' =>'presentation.php', 'name' => 'Présentation'];
?>

<nav>
    <ul class="menu">
        <?php foreach($menus as $menu) :?>
            <li <?php if(isActive($menu)) { echo 'class="active"'; } ?>>
                <a href="<?= $menu['link']; ?>"><?= $menu['name']; ?></a>
            </li>
        <?php endforeach ?>
    </ul>
</nav>
```




## Object

<http://php.net/manual/fr/language.types.object.php>


```php
class Article
{
    protected $id;
    protected $title;
    protected $content;

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