---
layout: post
title: "Créer un blog avec Octopress 100% gratuit, pour les nuls"
date: 2013-08-07 00:50
comments: true
categories: [Dev]
---

## Introduction


~~~
$ git clone git@github.com:imathis/octopress.git # fork octopress
~~~
{:lang="sh"}

Octopress est un moteur de blog statique. Qui dit statique, dit plusieurs avantages :

1. Léger
2. Pas d'appels à une base de donnée
3. Pas de script côté serveur
4. Du coup c'est super rapide !!

Si vous êtes débutant, cette page est pour vous. Dans 15 étapes, vous aurez votre blog statique prêt à l'emploi.

## Installation d'Octopress sous Mac OSX Lion

1. Ouvrir le terminal, puis saisir Terminal : Ctrl+espace, puis Terminal dans l'invite de recherche.

2. Installer Xcode. Dans l'appStore, chercher xcode. Cliquer sur le bouton "gratuit" (oui c'est gratuit :).

3. Une fois installé, lancer Xcode

4. Dans le menu, Xcode > Préférences > Downloads, puis cliquer sur le bouton "install" en face de la ligne "Command Line Tool"

5. Installer RVM. Dans le Terminal, lancer la commande suivante :

~~~
curl -L https://get.rvm.io | bash -s stable --ruby
~~~
{:lang="sh"}

6. Suivre les instructions proposées

7. Installer Ruby 1.9.3

~~~
rvm install 1.9.3
rvm use 1.9.3
rvm rubygems latest
~~~
{:lang="sh"}

8. Dans le terminal, verifier que 1.9.3 est bien la dernière version, saisissez

~~~
ruby --version
~~~
{:lang="sh"}

9. Installer Octopress, dans le Terminal, saisissez les lignes suivantes dans le Terminal:

~~~
git clone git://github.com/imathis/octopress.git octopress
cd octopress    # Vous utilisez RVM, On vous demandera de faire confiance au fichier .rvmrc (dites oui en tappant y).
ruby --version  # Ruby 1.9.3 devrait s'afficher
~~~
{:lang="sh"}

10. Installer les dépendances: saisir les commandes suivantes dans le Terminal

~~~
gem install bundler
rbenv rehash    # If you use rbenv, rehash to be able to run the bundle command
bundle install
~~~
{:lang="sh"}

11. Installer le thème d'Octopress par défaut
    
~~~
rake install
~~~
{:lang="sh"}
    
12. Installer Octopress avec les pages utilisateurs GitHub.  
Se rendre sur [le site de GitHub](http://www.github.com/) et créer un compte gratuitement. 


