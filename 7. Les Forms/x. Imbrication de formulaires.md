# Imbrication de formulaires

Pour notre `Advert` nous avons besoind d'images. Créons une entité `Photo`. Pour le moment nous nous contenterons simplement d'un lien web vers une image.

```
/**
 * @ORM\Entity(repositoryClass=PhotoRepository::class)
 */
class Photo
{
    /**
     * @ORM\Id
     * @ORM\GeneratedValue
     * @ORM\Column(type="integer")
     */
    private $id;

    /**
     * @ORM\Column(type="string", length=255)
     */
    private $url;
// ...
```

```
// ...
/**
 * @ORM\Entity(repositoryClass=AdvertRepository::class)
 * @ORM\HasLifecycleCallbacks()
 */
class Advert
{
    /**
     * @ORM\Id
     * @ORM\GeneratedValue
     * @ORM\Column(type="integer")
     */
    private $id;

    /**
     * @ORM\Column(type="string", length=255)
     * @Assert\NotNull()
     * @Assert\Length(min="20")
     */
    private $title;

    /**
     * @ORM\Column(type="float")
     */
    private $price;

    /**
     * @ORM\Column(type="text")
     * @Assert\Length(min="50")
     */
    private $description;

    /**
     * @ORM\OneToOne(targetEntity=Photo::class)
     */
    private $photo;

    // ...
```

Pour cette entité `Photo` nous avons crée un `PhotoType`. Dans notre `AdvertType` nous pouvons simplement l'inclure comme n'importe quel autre `FormType` :

`->add('photo', PhotoType::class)`

## Imbrication d'une collection formulaires

C'est ici que ça se corse un peu. Imaginons que nous voulions pouvoir ajouter plusieurs images pour une annonce. La relation entre les deux entités changerait et on aurait plutôt un truc comme :

```
/**
 * @ORM\Entity(repositoryClass=PhotoRepository::class)
 */
class Photo
{
    /**
     * @ORM\Id
     * @ORM\GeneratedValue
     * @ORM\Column(type="integer")
     */
    private $id;

    /**
     * @ORM\Column(type="string", length=255)
     */
    private $url;

    /**
     * @ORM\ManyToOne(targetEntity=Advert::class, inversedBy="photos")
     */
    private $advert;
// ...
```

```
// ...
/**
 * @ORM\Entity(repositoryClass=AdvertRepository::class)
 * @ORM\HasLifecycleCallbacks()
 */
class Advert
{
    /**
     * @ORM\Id
     * @ORM\GeneratedValue
     * @ORM\Column(type="integer")
     */
    private $id;

    /**
     * @ORM\Column(type="string", length=255)
     * @Assert\NotNull()
     * @Assert\Length(min="20")
     */
    private $title;

    /**
     * @ORM\Column(type="float")
     */
    private $price;

    /**
     * @ORM\Column(type="text")
     * @Assert\Length(min="50")
     */
    private $description;

    /**
     * @ORM\OneToMany(targetEntity=Photo::class, mappedBy="advert")
     */
    private $photos;

    // ...
```

Comment savoir si il y aura zéro, une ou plusieurs photos attachées à notre entité `Advert` et comment gérer ça ?

Nous allons devoir utiliser le `CollectionType` comme ceci :

```
->add('photos', CollectionType::class, [
    'entry_type' => PhotoType::class,
    'entry_options' => ['label' => false],
    'allow_add' => true,
    'allow_delete' => true
    ])
```
Ici `entry_type` est l'option de `CollectionType` qui permet de définir quel `FormType` sera imbriqué. `allow_add` et `allow_delete` sont la pour dire qu'on peut en ajouter et en retirer (pour l'edit).

@TODO à déplacer après avoir appris les rendus, etc.




