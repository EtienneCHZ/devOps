# Compte Rendu TP3 Ansible

  

## Q3-1 Document your inventory and base commands 

L'inventaire Ansible est un fichier YAML qui définit les hôtes et leurs configurations. 

Notre inventory contient des configuration comme le nom de domaine ou l'emplacement de notre clé pour y accéder.


**Commandes de base Ansible**  

### **1. Tester la connexion aux hôtes**
```bash
ansible all -i inventories/setup.yml -m ping
```
**Vérifie si Ansible peut se connecter aux hôtes via SSH**.  

### **2. Exécuter une commande sur tous les hôtes**
```bash
ansible all -i inventories/setup.yml -m command -a "uptime"
```
**Affiche depuis chaque hôte le temps d'activité (`uptime`)**.  

### **3. Exécuter un playbook**
```bash
ansible-playbook -i inventories/setup.yml playbook.yml
```
**Exécute un playbook Ansible sur les hôtes définis dans `setup.yml`**.  

## Q3-2 Document your playbook

### Role Ansible : Docker Installation

Le rôle installe Docker sur un serveur Debian.

```
 roles:
    - docker  # Appelle la role `docker` pour gérer l'installation
```

### Packages installés
- `docker-ce`
- `docker-ce-cli`
- `containerd.io`
- `apt-transport-https`
- `ca-certificates`
- `curl`
- `gnupg`
- `lsb-release`
- `python3-venv`

### Utilisation
```bash
ansible-playbook -i inventories/setup.yml playbook.yml
```

## Q3-3 Document your docker_container tasks configuration.

### **Rôle des tasks dans les rôles Ansible pour la gestion de conteneurs Docker**  

En **Ansible**, les **tasks** sont les actions exécutées sur les hôtes cibles. Lorsqu’on utilise des **rôles**, ces tâches sont organisées dans des répertoires structurés pour une meilleure réutilisabilité et modularité.  

---

## **Explication des rôles**
Dans le playbook :
```yaml
- hosts: all
  gather_facts: true
  become: true

  roles:
    - install_docker
    - init_env
    - create_network
    - launch_database
    - launch_app
    - launch_proxy
    - launch_front
```
Chaque rôle contient des **tasks** spécifiques pour configurer un environnement Docker.

| **Rôle**               | **Description** |
|------------------------|----------------|
| `install_docker`       | Installe Docker sur la machine cible |
| `init_env`             | Initialise l’environnement (création de répertoires, variables d’environnement, etc.) |
| `create_network`       | Crée un réseau Docker (`docker_network`) pour que les conteneurs puissent communiquer |
| `launch_database`      | Lance un conteneur de base de données (`docker_container`) |
| `launch_app`           | Déploie l’application backend dans un conteneur |
| `launch_proxy`         | Lance un serveur proxy (comme **NGINX** ou **Traefik**) |
| `launch_front`         | Lance l’application frontend dans un conteneur |

---


### ** Pourquoi utiliser des rôles avec Ansible ?**
**Modularité** : Chaque composant (DB, frontend, backend, proxy) est géré séparément.  
**Réutilisabilité** : On peut réutiliser les rôles dans plusieurs projets.  
**Facilité de maintenance** : Modification d’un composant sans impacter le reste.  
**Automatisation** : On peut tout déployer en une seule commande :  
```sh
ansible-playbook -i inventory playbook.yml
```

---

### ** Conclusion**
Avec **Ansible et Docker**, on peut structurer proprement le déploiement d’une application conteneurisée en utilisant des **rôles**, où chaque rôle gère un aspect du système.