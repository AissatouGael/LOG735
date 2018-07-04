# ÉTS : LOG735-01 Systèmes distribués (Été 2018)

## Pré-requis

### Testés sur :
- Édition  : Windows 10 Pro x64
- Version  : 1803
- OS Build : 17134.112

### Instructions :
1.  Installer Docker CE en suivant les instructions, du site officiel, spécifiques à votre système d'exploitation : https://docs.docker.com/install/

2.  Installer l'image Docker suivante : https://github.com/sequenceiq/hadoop-docker
    1.  Télécharger et installer l'image :
        ```console
        docker pull sequenceiq/hadoop-docker:2.7.1
        ```
    2.  Démarrer le conteneur :
        ```console
        docker run -it sequenceiq/hadoop-docker:2.7.1 /etc/bootstrap.sh -bash
        ```

3.  TODO

## [P]roblème rencontrés, et [S]olutions trouvés

1.  P : Hyper-V est activé, mais non reconnu par Docker.
    S : Dans la section « Turn Windows features on or off », Désactiver Hyper-V, redémarrer, Réactiver Hyper-V et redémarrer encore. Docker devrait fonctionner.

2.  P : Durant l'installation, une erreur concernant `virt` peut survenir.
    S : Il faut exécuter la commande suivante pour y remédier :
        ```console
        sudo apt-wget install virtualbox
        ```
