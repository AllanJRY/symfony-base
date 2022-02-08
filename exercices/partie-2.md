### Implémentation des principales routes de notre front office

*Selon votre avancé sur l'exercice précèdent, vous devez déjà disposer des controllers PlayerController/UserController et GameController.*

Voici les controllers à implémenter dans notre application :
___
- [ ] HomeController
  - [ ] `/` - Page d'accueil.
___
- [ ] PlayerController - `/players` devant toutes routes.
  - [ ] `/` - Listing des joueurs
  - [ ] `/{id}` ou `/{slug}` - Page d'informations d'un joueur (via son id ou son slug)
___
- [ ] GameController - `/games` devant toutes routes.
    - [ ] `/` - Listing des jeux
    - [ ] `/{id}` ou `/{slug}` - Page d'informations d'un jeu (via son id ou son slug)
___
- [ ] PublisherController - `/publishers` devant toutes routes.
    - [ ] `/` - Listing des éditeurs
    - [ ] `/{id}` ou `/{slug}` - Page d'informations d'un éditeur (via son id ou son slug)
___
- [ ] GenreController - `/genres` devant toutes routes.
    - [ ] `/` - Listing des genres
    - [ ] `/{id}` ou `/{slug}` - Page d'informations d'un jeu (via son id ou son slug)

### Intégration des templates Twig des pages index et show des entités Game et Player

Réalisez l'intégration en suivant les maquettes qui vous sont mises à dispositions dans le dossier maquettes.

*Ou bien via la partage figma si possible*

Vous remarquerez des liens de tri en haut des pages index :
![](C:\Users\aj-al\dev\drosalys\formation\symfony-base\exercices\figurations\fig-1.png)

Pour le moment faites en simplement des liens qui envoient vers #, ils seront liés par la suite à de nouvelles routes utilisant des requêtes DQL personnalisées.

De plus, dans la page détails d'un jeu, vous rencontrerez une section commentaires avec deux formulaires :
![](C:\Users\aj-al\dev\drosalys\formation\symfony-base\exercices\figurations\fig-2.png)

![](C:\Users\aj-al\dev\drosalys\formation\symfony-base\exercices\figurations\fig-3.png)

La section suivante aborde la génération des formulaires, vous pouvez donc attendre avant de les intégrer.

### Génération de formulaires pour les commentaires.

Comme vue juste avant, la page détails d'un jeu contient une section commentaires avec deux types de formulaires, même si l'un est plus camouflé que l'autre (les up et down votes).

Dans cette partie on ne s'occupera pas du formulaire des up et down votes, on y reviendra plus tard.

Symfony dispose de FormType pour nous mâcher une grande partie du travail pénible qu'implique la gestion d'un formulaire.

Pour ce faire il suffit de générer un formulaire pour une certaine entité via la commande `symfony console make:form`

En suite, il vous suffit juste d'éditer le FormType généré pour qu'il dispose des types de champs souhaitez.

Notamment dans notre cas, il nous faudra au minimum un champ select pour choisir l'utilisateur qui est l'auteur du commentaire ainsi qu'un champ textarea pour le contenu.

Ces deux types fournis par Symfony peuvent vous intéresser :

- [EntityType](https://symfony.com/doc/current/reference/forms/types/entity.html)
- [TextareaType](https://symfony.com/doc/current/reference/forms/types/textarea.html)


Une fois le formulaire générer il suffit de l'utiliser dans votre action de controller qui s'occupe d'afficher les détails :

```php
    /**
    * @Route('/games/{slug}, name="show_game")
    */
    public function show(Request $request, EntityManagerInterface $em): Response
    {
        // just set up a fresh $task object (remove the example data)
        $comment = new Comment();

        $form = $this->createForm(CommentType::class, $comment);

        $form->handleRequest($request);
        if ($form->isSubmitted() && $form->isValid()) {
            $comment = $form->getData();

            $em->persist($comment);
            $em->flush();
        }

        return $this->renderForm('comments/show.html.twig', [
            'form' => $form->createView(),
        ]);
    }
```

Si besoin ne pas hésiter à s'aider de la [documentation de symfony](https://symfony.com/doc/current/forms.html)

### Les requêtes de repository personnalisées

L'objectif sera de prendre en main la définition d'une requête en DQL pour ajouter des méthodes à un repository qui de base, est assez limité.

***[COURS SUR LES DQL]***
