# Environnement INF3995 sur macOS

## 1. Installer les dépendences communes

### 1.1. Commandes système essentielles

Si ce n'est pas déjà fait, vous aurez besoin des outils Xcode en ligne de
commande. Cet ensemble de programmes regroupe les essentiels comme le
compilateur C/C++ Clang ou une version plus à jour de Git.

Pour déclencher la boîte de dialogue du système qui permet d'installer les
outils en ligne de commande, il suffit d'essayer d'utiliser l'une d'entre elles
dans un terminal comme `clang` ou `git`.

### 1.2. Paquets Homebrew

macOS ne propose pas de gestionnaire de paquets comme `apt`, `dnf` ou `pacman`.
À la place, il est possible d'installer [Homebrew](https://homebrew.sh) avant
d'installer les paquets requis avec la commande suivante:

```sh
brew install boost cmake cppzmq eigen freeimage gts jsoncpp libyaml libzip \
  protobuf@3 python pkgconfig tinyxml tinyxml2 yaml zeromq
```

Afin de rendre les paquets obsolètes installés par la commande ci-dessus,
ajoutez les variables d'environnement suivant à la configuration de votre
terminal (par exemple en ajoutant les lignes suivantes à votre fichier
`.bashrc` ou `.zshrc`):

```sh
export PKG_CONFIG_PATH=/opt/homebrew/opt/protobuf@3/lib/pkgconfig
```

**Attention**: Homebrew gère différemment l'architecture arm64 des ordinateurs
munis d'une puce Apple M1 ou M2. Le préfixe d'installation par défaut est
`/opt/homebrew` sur arm64 et `/usr/local` sur x86_64.

Le téléchargement et l'installation peuvent prendre un moment, car toutes les
dépendances du simulateur Gazebo et de l'environnement ROS sont récupérées.

## 2. Installer Gazebo

### 2.1. Installation des dépendences de Gazebo

Les fonctions de base du simulateur sont réparties dans différents paquets
utilitaires regroupés sous l'appellation "Ignition". Sur macOS, on doit
obligatoirement les compiler à partir des sources en suivant le [guide
d'installation des dépendences de Gazebo depuis les
sources](https://classic.gazebosim.org/tutorials?tut=install_dependencies_from_source&cat=install).

**Note**: Un script est disponible pour exécuter ces étapes de manière automatique.

### 2.2. Installation du simulateur Gazebo

Le moteur et l'interface du simulateur doivent également être installés depuis
les sources sur macOS, d'après le [guide d'installation de Gazebo depuis les
sources](https://classic.gazebosim.org/tutorials?tut=install_from_source&cat=install).

**Note**: Un script est disponible pour exécuter ces étapes de manière automatique.

## 3. Installer ROS

### 3.1. Récupérer les sources

Les fichiers sources de ROS sont répartis dans une collection de dépôts Git. Pour les récupérer tous en même temps, installer les outils de gestion de l'espace de travail ROS avec la commande, suivre le [guide d'installation depuis les sources pour ROS 1 Noetic](https://wiki.ros.org/noetic/Installation/Source)

```sh
python3 -m pip install catkin_pkg rosdep rosinstall_generator vcstool
```

```sh
brew install boost cmake console_bridge pkgconfig poco python pyqt@5 sip tinyxml tinyxml2
```

### 3.2. Créer les liaisons d'interface graphique Python pour Qt

Certains programmes ROS utilisent le cadriciel Qt pour afficher des fenêtres à
travers des liaisons Python au lieu de l'appeler directement en C++. Pour
générer manuellement les paquets Python nécessaires, on doit utiliser une
ancienne version de l'outil `sip-build` qui est normalement installé par
Homebrew plus haut.

```sh
python3 -m pip install PyQt-builder
```

1. Télécharger le paquet Python `PyQt5` disponible [ici]("https://pypi.python.org/packages/source/P/PyQt5/PyQt5-$pkgver.tar.gz")
2. Extraire les fichiers et exécuter la commande suivante dans un terminal:

```sh
export PATH="$PATH:/opt/homebrew/opt/qt@5/bin" # pour rendre disponible 'qmake'
sip-build --confirm-license --no-make --api-dir /usr/share/qt/qtsci/api/python --pep484-pyi
cd build
make # patienter pendant la compilation (~15 min)...
make install # par défaut installé dans /usr/local, on peut changer le préfixe avec INSTALL_ROOT="/chemin/exemple"
```

### 3.2. Compiler ROS depuis les sources

À présent que votre système dispose de toutes les ressources nécessaires, on
peut récupérer les sources de ROS et lancer la compilation puis l'installation
dans le répertoire de votre choix, par exemple `/usr/local/ros`.
