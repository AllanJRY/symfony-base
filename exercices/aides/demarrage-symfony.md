# Rappel démarrage d'un nouveau projet Symfony 

1. Utilisez l'une de ces commandes pour créer un nouveau projet :
```shell
composer create-project symfony/skeleton:"^5.4" my_project_directory 
cd my_project_directory
composer require webapp
```
ou 
```shell
symfony new my_project_directory --webapp --version=5.4
```
2. Une fois le projet créé, se placer dedans et lancer la commande `composer install` pour être sûr que les dépendances soient installées.
3. Si webpack-encore n'est pas présent sur le projet, il faut l'ajouter :
```shell
composer require symfony/webpack-encore-bundle
yarn add @symfony/webpack-encore --dev
yarn add sass-loader@^12.0.0 sass --dev
yarn add typescript ts-loader@^9.0.0 --dev
yarn add bootstrap
yarn install
```
4. Dans le fichier webpack.config.js séparer les entrées pour les styles et les scripts :
```javascript
.addEntry('styles', './assets/styles/main.scss')
.addEntry('scripts', './assets/styles/main.ts')
```

N'oubliez pas de créer ou renommer les fichiers dans le dossier assets !

5. Activer Sass et Typescript en dé-commentant ces deux lignes toujours dans le webpack.config.js
```javascript
// .enableSassLoader()
    
// .enableTypeScriptLoader()
```
6. Créer un fichier tsconfig.json à la racine du projet et mettez lui en content un object json vide :
```json
{}
```
7. Et enfin, dans le dossier des templates, dans la vue base.html.twig mettez à jour les fichiers de styles et scripts importés dans la page :
```twig
{% block stylesheets %}
    {{ encore_entry_link_tags('styles') }}
{% endblock %}

{% block javascripts %}
    {{ encore_entry_script_tags('scripts') }}
{% endblock %}
```

8. Faites un copier collé de votre fichier .env se trouvant à la racine du projet et nommez la copie .env.local
9. Dans le .env.local changer vos informations de base de données :
```dotenv
DATABASE_URL=mysql://db_user:db_password@127.0.0.1:3306/db_name?serverVersion=5.7
```
Attention bien prendre la ligne avec le driver **mysql**

10. Si vous souhaitez créer votre base de données tout de suite vous pouvez lancer la commande :
```shell
symfony console doctrine:database:create
```
11. vous pouvez désormais faire un `yarn watch` pour générer les assets et un `symfony serve` pour lancer le serveur Symfony !


