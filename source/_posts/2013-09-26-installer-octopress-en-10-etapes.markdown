---
layout: post
title: "Créer un blog avec Octopress 100% gratuit, pour les nuls"
date: 2013-08-07 00:50
comments: true
categories: Dev
---

## Introduction

Octopress est un moteur de blog statique. Qui dit statique, dit plusieurs avantages :

1. Léger
2. Pas d'appels à une base de donnée
3. Pas de script côté serveur
4. Du coup c'est super rapide !!

Tu ne connais pas Ruby, XCode mais tu aimerais bien avoir un blog sympa et rapide. 
Tu as trouvé Octopress après des recherches sur le net et tu t'es dit que tu voulais essayer. Cependant voilà, tu comprends pas bien l'anglais et quand tu suis les étapes sur le site officiel tu remarques qu'il manque plein de trucs sur ton Mac. Cette article est pour toi.
Dans 15 étapes, tu auras ton blog statique prêt à l'emploi pour pas un rond.

## Installation des outils

1.  Ouvrir le terminal
    Ctrl+espace, puis "Terminal" dans l'invite de recherche.

2.  Installer Xcode.
    Dans l'appStore, chercher xcode. Cliquer sur le bouton "gratuit" (oui c'est gratuit :).

3.  Installer l'outil ligne de commande Xcode
    Dans le menu de Xcode, ```Xcode > Préférences > Downloads```, puis cliquer sur le bouton ```install``` en face de la ligne ```Command Line Tool```

5.  Installer RVM.
    Dans le Terminal, lancer la commande suivante :

    ~~~
    curl -L https://get.rvm.io | bash -s stable --ruby
    ~~~
    {:lang="sh"}

    Puis suivre les instructions proposées

7.  Installer Ruby 1.9.3
    Dans le terminal, lancer les commandes suivantes :

    ~~~
    rvm install 1.9.3
    rvm use 1.9.3
    rvm rubygems latest
    ~~~
    {:lang="sh"}

8.  Vérifier la version de Ruby

    ~~~
    ruby --version
    ~~~
    {:lang="sh"}

    Le résultat doit contenir ```ruby 1.9.3```

## Installation d'Octopress

9.  Se placer dans ton dossier de travail.

    ~~~
    cd /chemin/vers/votre/dossier/de/travail
    ~~~
    {:lang="sh"}

    Personnellement je l'ai placé avec tous mes autres projets dans le dossier ```/Applications/MAMP/htdocs```

9.  Récupérer Octopress

    ~~~
    git clone git://github.com/imathis/octopress.git octopress
    cd octopress    # Vous utilisez RVM, On vous demandera de faire confiance au fichier .rvmrc (dites oui en tappant y).
    ruby --version  # Ruby 1.9.3 devrait s'afficher
    ~~~
    {:lang="sh"}

10. Installer les dépendances

    Saisir les commandes suivantes dans le Terminal

    ~~~
    gem install bundler
    rbenv rehash    # Si tu utilies rbenv, rehash afin de pouvoir exécuter la commande bundle
    bundle install
    ~~~
    {:lang="sh"}

11. Installer le thème d'Octopress par défaut
    
    ~~~
    rake install
    ~~~
    {:lang="sh"}

## Deployer Octopress sur Internet gratuitement

12. Créer un compte GitHub
    Se rendre sur [le site de GitHub](http://www.github.com/) et créer un compte gratuitement.

13. Créer un nouveau repository sur GitHub.
    Sur GitHub, se rendre sur l'onglet ```Repositories``` et cliquer sur le bouton vert ```new```.
    Le nom que tu dois saisir doit être de la forme : ```nom_du_blog.github.io```.

14. Dire à Octopress de que tu veux mettre ton blog sur les pages GitHub
    
    ~~~
    rake setup_github_pages
    ~~~
    {:lang="sh"} 

    On te demandera de saisir un truc. Copie-colle ```git@github.com:ton_pseudo_github/nom_du_blog.github.io.git```.

15. Génère ton blog et publie-le ! Enfin.

    ~~~
    rake generate
    rake deploy
    ~~~
    {:lang="sh"} 

## Conclusion

Ca y est ! Tu as ton blog maintenant disponible sur Internet et ça t'a pas coûté un kopeck. La vie est belle n'est-ce pas ?
Tu peux aller maintenant sur ```http://nom_du_blog.github.io``` et admirer ton beau travail.


