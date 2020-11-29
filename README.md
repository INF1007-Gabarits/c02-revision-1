[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod-redirect-0.herokuapp.com/)

# Chatbot pour Twitch! (révision)

<!-- Avant de commencer. Consulter les instructions à suivre dans [instructions.md](instructions.md) -->

Nous allons écrire un chatbot, c'est-à-dire un programme qui lit le texte dans le clavardage d'un stream Twitch et qui répond à des commandes en utilisant un compte Twitch.

## Avant tout, il faut un compte pour le chatbot

Les sessions de clavardage associées aux streams de Twitch utilisent le protocole IRC (*Internet Relay Chat*) pour communiquer. C'est un vieux protocole (début années 90) très populaire et utilisé pour beaucoup de choses. Pour que votre chatbot puisse se connecter au IRC de Twitch, il lui faut un compte Twitch valide avec lequel se connecter. Vous ne pouvez pas simplement entrer votre nom d'utilisateur et votre mot de passe, il vous faut un jeton d'identification. Ce jeton vous sert essentiellement de mot de passe pour vous connecter au IRC, mais sans réellement utiliser votre mot de passe.

Vous pouvez utiliser votre propre compte pour le chatbot, ce qui fait que celui-ci va parler pour vous dans le *chat*. Pour générer facilement un jeton, connectez-vous à votre compte Twitch dans votre fureteur puis allez sur https://twitchapps.com/tmi/. On vous demandera la première fois de connecter l'application de génération de jetons à votre compte (vous approuvez), puis on vous donnera un jeton sous la forme `oauth:séquence-de-lettres-et-de-chiffres`.

<img src="doc/assets/oauth_token_gen.png">

C'est ce jeton (incluant le `oauth:`) que vous utilisez comme mot de passe IRC. Écrivez-le en quelque part et ayez-le à portée de main pour faire les exercices.

## Présentation du code fourni

<img src="doc/assets/chatbot_classes.png">

### *irc.py*

Ce fichier contient des classes qui implémentent un client IRC. Vous n'avez pas besoin de vous en servir directement pour aujourd'hui. la Classe `irc.Client` est utilisée par les autres modules pour faire la connection au serveur puis l'envoi et la réception des messages IRC.

### *chatbot.py*

Cette classe représente la base d'un chatbot IRC générique (pas forcément Twitch) qui permet de reconnaître des commandes (messages qui commencent par un certain caractère donné) et d'y associer des fonctions de rappel (*callback*). Vous n'avez pas à vous servir directement de la classe `Chatbot`, mais vous aurez à appeler certaines de ses méthodes à travers l'héritage de la classe `TwitchBot`. La classe `Chatbot.Command` sera utile dans le dernier exercice.

### *twitch_bot.py*

Cette classe représente un chatbot fonctionnel spécifiquement fait pour Twitch. Il se connecte en SSL (connexion sécurisée, comme HTTPS) au serveur IRC de Twitch et reconnait les commandes précédées d'un point d'exclamation, par exemple `!hello` (la convention sur Twitch). Ce chatbot enregistre tous les messages qu'il reçoit dans un fichier et peut afficher les messages en temps réel (argument `log_to_console` de `TwitchBot.__init__()`)

On construit un `TwitchBot` en lui donnant un dossier dans lequel mettre les journaux. Chaque session (appel de `run()`) génère son propre fichier dont le nom est la date et l'heure de connexion. On se connecte au serveur en appelant `TwitchBot.connect_and_join()` à laquelle on donne le mot de passe (le jeton incluant le `oauth:`), le surnom (nom du compte Twitch à utiliser) et le nom de la chaîne (la chaîne Twitch dans laquelle clavarder). On part ensuite la réception et le traitement des command avec `TwitchBot.run()`.

Exemple:
```python
bot = TwitchBot("mes_journaux")
bot.register_command(
    "ma_commande",
    mon_callback
)
bot.connect_and_join(
    le_jeton_oauth,
    le_nom_du_compte_twitch,
    le_nom_du_channel
)
bot.run()
```

## Révision chapitre 7 (fonctions)

### Répondre avec une salutation

On veut que le chatbot réponde à la commande `!say_hi` avec un certain message. Il faut donc envoyer un message au serveur. La méthode `send_privmsg()` de `TwitchBot` permet de faire cela. Pour que la commande soie reconnue, il faut l'enregistrer avec `TwitchBot.register_command()`, à laquelle on passe le nom de la commande (sans le `!`) et un callback qui doit prendre un seul paramètre qui est le message qui l'a déclenché. On ne va pas se servir du paramètre pour tout de suite.

Il nous faut donc créer une fonction de rappel qui envoie un message dans le chat à l'aide du bot connecté. Toutefois, on ne veut pas *hardcoder* le bot et le message directement dans la fonction. On va plutôt faire une fonction qui crée le callback à en lui passant le bot et le message. Le callback retourné est ensuite enregistré avec `register_command()`.

Le code à compléter est dans *ch7.py*

## Révision chapitre 8 (format de fichiers)

Nous avons vu au chapitre 8 plusieurs formats de fichier, tels que WAV, INI, CSV et JSON.

### Charger les données de connection d'un fichier INI

Dans l'exercice précédent, nous avons écrit directement dans le code source le nom du compte, le jeton d'identification et le channel auquel se connecter. Ce n'est clairement pas une bonne pratique. Nous allons plus charger ces données à partir d'un fichier INI ([data/config.ini](data/config.ini)). Il vous faut donc aller mettre votre nom de compte et votre jeton dans le fichier (sous la section `[login]`). Le nom du channel auquel se connecter est dans la section `[chat]`. C'est évidemment *chosson* pour aujourd'hui si vous voulez que votre bot soit visible à la classe.

Le code à compléter est dans *ch8.py*

### Répondre avec une citation aléatoire

Dans le fichier [data/quotes.json](data/quotes.json) on a quelques citations de jeux vidéos, catégorisées selon le jeu. On voudrait avoir une commande `!quote` qui retourne une citation aléatoire dans celles présentes dans le fichier. On charge d'abord le fichier JSON (fonction `load_quotes()`). Ensuite, dans `build_quotes_callback()` on crée un callback qui choisit aléatoirement une catégorie, puis une citation aléatoire dans cette catégorie. Notez comment le fichier est construit, c'est-à-dire un dictionnaire dont chaque clé est une catégorie (le nom d'un jeu) et la valeur est une liste de citations.

## Révision chapitre 9 (bonnes pratiques)

### Passer des arguments au script

TODO: Rappel sur `argparse`

## Révision chapitre 11 (orientée-objet)

### Matière additionnelle

TODO: `dataclasses`

TODO: Décorateurs

### Créer une classe de chatbot qui met tout le reste ensemble

TODO: Utilisation attendue de la classe `TwitchBot`; Diagramme de classe de la librairie.

TODO: Donner une citation aléatoire dans une catégorie ou dans tout.

