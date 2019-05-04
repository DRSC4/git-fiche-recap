# Git & GitHub

### Qu'est-ce-que Git ?

[Git](https://git-scm.com/) est un outil collaboratif de [gestion de version](https://fr.wikipedia.org/wiki/Gestion_de_versions) (_versioning_). La commande `git` est un outil à utiliser en ligne de commande, donc à installer en local sur sa machine.

Git permet de faire de la gestion de versions dans tout type de projet : pour gérer du code, des documents PDF, des images, etc. Concrètement, il devient possible de conserver un historique des versions successives des fichiers, de les comparer, d'y revenir en cas de pépin, etc.

Par ailleurs, Git permet de faciliter la collaboration entre plusieurs utilisateurs qui travaillent sur une même base de fichiers (par exemple, du code source), sans risquer d'écraser ou de perdre son travail ou celui des collègues.

### Comment ça marche ?

On enregistre (_commit_) les changements d'une version du projet à l'autre. 1 commit représente la différence entre 2 versions successives du projet.

Si on travaille avec d'autres personnes sur un projet géré par Git, on peut ensuite envisager de fusionner les commits (ajouts/modifications/suppressions de fichiers) de tous les utilisateurs, toujours grâce, et sous le contrôle, de Git.

### Git par rapport à GitHub ?

Git est l'outil de _versioning_, tandis que GitHub est un outil social : une plateforme en ligne permettant de sauvegarder son code dans un dépôt Git central et partagé, de communiquer sur de code par des commentaires, des _issues_, etc.

> 1 _repo_(_sitory_) = 1 dépôt Git = 1 projet géré avec Git & hébergée sur 1 serveur spécifique

GitHub est probablement le « réseau social » de développeurs utilisant Git le plus populaire ! Il existe également Gitlab, Bitbucket, etc.

![](img/github-users.png)

## Créer une clé SSH

Avant toute chose, pour utiliser Git et GitHub à leur pleins potentiels, on va créer une clé dite _SSH_. Cette clé est comme une carte d'identité, nous permettant de nous authentifier auprès de GitHub pour accéder aux repos privés, marquer nos commits, etc.

```shell
ssh-keygen -t rsa
```

Il vous sera demandé une _passphrase_, c'est-à-dire un mot de passe qui peut carrément être une phrase, avec des espaces, etc. Cette _passphrase_ n'est pas obligatoire (elle peut être vide), mais il est fortement recommandé d'en choisir une. Par contre, il faut la retenir par cœur !

À l'issue de la commande, dans le téléporteur :

- une clé _privée_ sera enregistrée sous /home/mint/.ssh/id_rsa
- une clé _publique_ sera enregistrée sous /home/mint/.ssh/id_rsa.pub

C'est la clé publique qu'on va devoir copier sur GitHub.

> Les échanges entre la machine et GitHub seront encryptés par la clé privée, et décryptés par la clé publique. La clé publique seule ne permet pas d'encoder, uniquement de décoder, on peut (on doit !) donc la transmettre aux différents services qui auront besoin de vérifier notre identité, par exemple GitHub — mais la clé SSH est réutilisable partout.

Vous pouvez regarder le contenu de la clé publique, par curiosité :

```shell
# Pour récupérer le contenu de notre clé publique
cat ~/.ssh/id_rsa.pub
```

Ajoutez cette clé publique dans votre compte GitHub :

```
Settings > SSH and GPG keys > New SSH key > Coller le contenu de la clé et valider
```

> Pour en savoir plus : https://help.github.com/articles/connecting-to-github-with-ssh/
>
> Il est important de noter que pour assurer l'authentification automatique (via la clé SSH) des commandes `git clone` et `git remote add ...`, il faut utiliser des URLs avec le protocole `SSH`  plutôt que `HTTPS`. À nouveau, pour en savoir plus : https://help.github.com/articles/why-is-git-always-asking-for-my-password/

### Configuration locale de Git

Git peut être [configuré très précisement](https://git-scm.com/docs/git-config). Voici quelques réglages utiles à mettre en place :

Le nom affiché dans les commits :

```
git config --global user.name "Bozo Le Clown"
```

L'email associé au commit (conseil : le même que celui du compte GitHub) :

```
git config --global user.email "bozo.leclown@oclock.io"
```

Le [mode de `push`](https://git-scm.com/docs/git-config#git-config-pushdefault) (simple = seule la branche de travail est pushée)

```
git config --global push.default simple
```

Activation des couleurs dans le résultat des commandes Git :

```
git config --global color.ui true
```

## Concepts centraux dans Git  

> Comme pour beaucoup d'outils informatiques, l'anglais est la langue de prédilection, et certains termes ne sont pas traduits, car utilisés tels quels au quotidien, y compris par les développeurs francophones.

Pour un dossier de travail donné, Git manipule différents « espaces virtuels » :

- **_Workspace_** : espace stockant les modifications en cours, **qui ne sont pas (encore) prises en compte** par Git.
- **_Index (ou stage)_** : espace stockant les modifications en cours, **qui seront prises en compte par Git pour le prochain commit (mais pas encore commitées)**.
- **_Local repository_** : espace stockant les modifications **déjà prises en compte par Git**.
- **_Remote repository_** : désigne le dépôt distant (_remote_, sur GitHub par exemple), dépôt auquel est relié votre dépôt local. **Les commits du _local repository_ doivent y être pushés pour mettre le dépôt distant à jour** et collaborer avec d'autres personnes.

![](img/git-overview.png)

## Mémo de commandes Git

### Obtenir de l'aide

- `git --help` : renvoie la liste des commandes disponibles
- `git [commande] -h` : idem pour une commande précise

### Cloner ou initier un repo

- `git clone {url} [nom-local]` : récupère un repo distant (_remote_ sur GitHub par exemple) en local, dans un dossier créé à la volée qu'il est possible de renommer (par défaut : nom du repo sur le _remote_)
- `git init` : crée un nouveau projet Git local à partir d'un dossier courant. Si on veut ensuite le partager sur GitHub, il faudra alors paramétrer au moins un _remote_

### Vérifier l'état courant du repo local

- `git status` : récapitule l'état local (workspace et index) des fichiers du projet géré avec Git
  - En rouge : modifié mais non pris en compte (= en workspace)  
  - En vert : modifié et pris en compte (= ajouté à l'index)

### Consulter l'historique du repo local

- `git log` : voir l'historique de tous les commits de la branche actuelle
- `git log --oneline` : idem en version synthétique
- `git log --graph` : idem en version branches

### Consulter ou se déplacer dans les commits

- `git show hashDuCommit` : montre les modifs dans un commit précis - hashDuCommit est les 7 premiers caractères de son identifiant (ex : 1fd88ar)
- `git checkout hashDuCommit` : permet de consulter la version hashDuCommit du projet /!\ si on fait des modifs en version antérieure, elles seront perdues !
- `git checkout master` : revient sur la dernière version en date du projet sur cette branche !  
master = bout de la branche (le + récent) /!\ avant un pull, l'origin/master n'est pas le même que notre master local si d'autres contributeurs ont ajouté des commits depuis notre dernier pull !
- `git checkout HEAD --nomDuFichier` : annule localement tous les changements sur le fichier depuis le dernier commit  
HEAD = pointeur dans la branche (commit qu'on est en train de consulter : identique ou antérieur à master)
- Annuler un add + commit _non pushé_ : `git reset HEAD^`
* Supprimer un commit **:warning: cela va supprimer vos modifications en local également**:
  * Revenir en local sur le dernier commit correcte : `git reset --hard hashDuCommit`
  * Forcer cette version comme étant la dernière : `git push -f`

### Branches

- `git branch nomDeLaBranche` : crée une nouvelle branche (nouvelle version du projet) identique à la version HEAD - permet de tester des développements (réparer un bug, expérimenter une nouvelle fonctionnalité...) et commiter sans affecter la branche principale
- `git checkout nomDeLaBranche` : bascule sur la branche nomDeLaBranche
- :bulb: `git checkout -b nomDeLaBranche` : permet de réaliser les deux opérations du dessus (création de la branche & migration sur celle-ci)
- `git merge nomDeLaBranche` : rattache ma branche au master de la branche principale (master) ==> nécessite de résoudre les conflits !

### Gestion des fichiers

- `git add [filename]` : ajoute les modifications faites dans le fichier à l'index
- `git add .`, _alias, technique du bourrin_ : ajoute les modifs de tous les fichiers dans le dossier  
/!\ à n'utiliser si on est sûr de n'avoir fait QUE des modifs liées au commit en cours !
- `git reset HEAD` : annule les `add` déjà faits
- `git commit [-m "message de commit"]` : enregistre les modifs indexées dans le commit
- `git push` : envoie tous les commits locaux sur le repo  
- `git pull` : récupère en local un projet depuis le repo, pour un projet déjà en cours (au contraire de clone qu'on utiliser uniquement pour un nouveau projet)  
- `git rm --cached <file(s)>` : **supprimer des fichiers suivis du repo** qui auraient été ajoutés par erreur OU **ajoutés dans le .gitignore par la suite** (ne supprime pas le fichier lui-même en local, bien entendu, uniquement dans le dépôt).

### En cas de suppression de votre dossier .git (cloné depuis github)

```git
git init
git remote add origin ssh@le/lien/vers/ton/repo.git
git add .
git commit -m "on envoiiiie"
git push origin --force master
```

## Fichiers spéciaux

- `.gitignore` : permet de lister des fichiers qui doivent être ignorés lors du `add`
- `.keep` : est un fichier qui peut être placé à la racine d'un répertoire vide afin que git prenne ce dossier en compte même s'il ne contient pas de fichier
