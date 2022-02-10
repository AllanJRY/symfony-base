# Ajout de fixtures

Installez les dépendances suivantes :
```shell
composer require --dev orm-fixtures
composer require cocur/slugify
composer composer require fzaninotto/faker
```

***Attention à ne pas utiliser Faker dans vos projets personnels, cette librairie n'est plus maintenue !***

Une fois les installations faites, dans le dossier src, créez un dossier DataFixtures et copiez la classe suivante :

**Attention quelques ajustements seront à prévoir selon votre base de données/vos entités.**

```php
<?php

namespace App\DataFixtures;

use App\Entity\Game;
use App\Entity\GameLibData;
use App\Entity\Genre;
use App\Entity\Library;
use App\Entity\Publisher;
use App\Entity\User;
use Cocur\Slugify\Slugify;
use Doctrine\Bundle\FixturesBundle\Fixture;
use Doctrine\Persistence\ObjectManager;
use Faker\Factory;
use Faker\Generator;

class BaseFixture extends Fixture
{
    private Generator $faker;
    private Slugify $slugify;
    private ObjectManager $manager;

    private static $PUBLISHERS = [
        'Rockstar Games',
        'Riot',
        'Naughty Dog',
        'Electronic Arts',
        'Activision',
    ];

    private static $GENRES = [
        'Horreur',
        'Aventure',
        'Action',
        'mmorpg',
        'Survie',
    ];

    private static $GAMES = [
        'Red Dead Redemption 2',
        'The Last of Us',
        'Counter Strike : Source',
        'NHL 22',
        'League of Legend',
        'Halo',
        'Minecraft',
    ];


    public function load(ObjectManager $manager)
    {
        $this->manager = $manager;
        $this->faker = Factory::create();
        $this->slugify = new Slugify();

        $players = $this->generatePlayers();
        $publishers = $this->generatePublishers();
        $genres = $this->generateGenres();
        $games = $this->generateGames($publishers, $genres);
        $this->addGameToPlayers($players, $games);

        $this->manager->flush();
    }

    private function generatePlayers()
    {
        $generatedPlayers = [];
        for($i = 0; $i < 20; $i++) {
            $username = $this->faker->userName;
            $player = (new User())
                ->setUsername($username)
                ->setSlug($this->slugify->slugify($username))
                ->setEmail($this->faker->email)
                ->setLibrary(new Library())
            ;
            $this->manager->persist($player);
            $generatedPlayers[] = $player;
        }

        return $generatedPlayers;
    }

    private function generatePublishers()
    {
        $generatedPublishers = [];
        foreach(self::$PUBLISHERS as $publisherName) {
            $publisher = (new Publisher())
                ->setName($publisherName)
                ->setSlug($this->slugify->slugify($publisherName))
                ->setCreatedAt($this->faker->dateTime)
            ;
            $this->manager->persist($publisher);
            $generatedPublishers[] = $publisher;
        }

        return $generatedPublishers;
    }

    private function generateGenres()
    {
        $generatedGenres = [];
        foreach(self::$GENRES as $genreName) {
            $genre = (new Genre())
                ->setName($genreName)
                ->setSlug($this->slugify->slugify($genreName))
            ;
            $this->manager->persist($genre);
            $generatedGenres[] = $genre;
        }

        return $generatedGenres;
    }

    private function generateGames(array $publishers, array $genres)
    {
        $generatedGames = [];
        foreach(self::$GAMES as $gameName) {
            $game = (new Game())
                ->setName($gameName)
                ->setSlug($this->slugify->slugify($gameName))
                ->setDescription('Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident.')
                ->setPrice($this->faker->randomFloat(2, 1, 1000))
                ->setPublishedAt($this->faker->dateTime)
                ->setPublisher(
                    $publishers[array_rand($publishers)]
                )
            ;

            $gameGenres = [];
            for($i = 0; $i < rand(0, count($genres)-1); $i++) {
                $genre = $genres[array_rand($genres)];
                if (!in_array($genre, $gameGenres)) {
                    $gameGenres[] = $genre;
                }
            }

            foreach ($gameGenres as $genre) {
                $game->addGenre($genre);
            }

            $this->manager->persist($game);
            $generatedGames[] = $game;
        }

        return $generatedGames;
    }

    private function addGameToPlayers(array $players, array $games)
    {
        /**
         * @var User $player
         */
        foreach($players as $player) {
            if (null !== $player->getLibrary()) {
                for($i = 0; $i < rand(0, count($games)-1); $i++) {
                    $player->getLibrary()->addGame(
                        (new GameLibData())
                            ->setInstalled($this->faker->boolean())
                            ->setGame($games[array_rand($games)])
                        )
                    ;
                }
            }
        }
    }
}
```