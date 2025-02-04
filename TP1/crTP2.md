# Compte Rendu TP2 GITHub

  

## Q2-1 What are testcontainers?

  

Testcontainers est une bibliothèque qui permet d’exécuter des tests d’intégration en utilisant des conteneurs Docker pour simuler des services externes (bases de données, queus, API, etc.).

  

On note que les librairies sont dans le scope "test" et ne seront donc pas incluses dans l'applicaiton finale.

  

## Q2-2 Document your Github Actions configurations.

```

# Nom du workflow GitHub Actions

name: CI devops 2025

  

# Déclenchement du workflow

on:

push:

# Ce workflow s'exécute lorsqu'un push est effectué sur les branches "main" et "develop"

branches:

- main

- develop

# Il s'exécute également lorsqu'une pull request est créée ou mise à jour

pull_request:

  

# Définition des jobs à exécuter

jobs:

test-backend:

Le job s'exécute sur un environnement Ubuntu 22.04

runs-on: ubuntu-22.04

  

steps:

# Étape 1 : Récupération du code source depuis le repository GitHub

- name: Checkout repository

uses: actions/checkout@v4

  

# Étape 2 : Installation et configuration de Java 21 (distribution Corretto)

- name: Set up JDK 21

uses: actions/setup-java@v4

with:

distribution: 'corretto' # Distribution d'Amazon Corretto (alternative à Temurin, OpenJDK)

java-version: '21' # Version de Java installée

  

# Étape 3 : Nettoyage, compilation et exécution des tests avec Maven

- name: Build and test with Maven

run: mvn clean verify # "clean" supprime les fichiers précédents, "verify" exécute les tests

working-directory: TP1/backend-api/simple-api-student-main # Spécifie le répertoire où exécuter la commande

```

  

## Q2-3 For what purpose do we need to push docker images?

  

Nous devons pousser les images Docker afin de faciliter le déploiement et l'exécution de notre application dans différents environnements.

  

### **Pourquoi pousser des images Docker ?**

1.  **Déploiement continu (CD)**

- En stockant nos images sur **Docker Hub** (ou un autre registre), nous pouvons facilement les récupérer et les exécuter sur **n'importe quel serveur** ou **orchestrateur** (comme Kubernetes).

  

2.  **Reproductibilité & Cohérence**

- Une image Docker contient **tout le nécessaire** pour exécuter l’application (code, dépendances, configuration, OS).

- Cela garantit que l’application tourne **de la même manière** en local, en test et en production.

  

3.  **Faciliter la collaboration**

- Plusieurs développeurs ou équipes peuvent **partager et utiliser** la même image sans avoir à reconfigurer l’environnement.

  

4.  **Automatisation & Scalabilité**

- Lorsqu’une image est poussée, elle peut être automatiquement déployée sur un serveur ou un **cluster** (ex: Kubernetes, AWS ECS).

- Cela permet d’automatiser le processus de **mise à jour et de mise à l’échelle** de l’application.

  

### **Conclusion**

Pousser une image Docker est **essentiel** pour un workflow DevOps efficace, assurant **déploiement rapide, cohérence et automatisation** du cycle de vie de l’application.

  

## Q2-4 2-4 Document your quality gate configuration.

  

Voici  toutes les étapes nécessaires pour faire fonctionner la Quality Gate avec SonarQube dans notre projet CI/CD sur GitHub Actions. 
  

## **1. Configurer SonarCloud**

Avant de pouvoir utiliser SonarQube dans ton workflow GitHub Actions, tu dois :

1)  **Créer un compte SonarCloud** 

2)  **Ajouter le projet sur SonarCloud**

3)  **Générer un token et l’ajouter aux GitHub Secrets**
 

## **2. Modifier le workflow GitHub Actions**

Ajouter un workflow GitHub Actions qui :

-  **Vérifie et télécharge le code**

-  **Configure Java et Maven**

-  **Effectue une analyse SonarQube**

-  **Vérifie la Quality Gate**

  

### **Fichier complet : dans .github/workflows
   

## **3. Configurer la Quality Gate sur SonarCloud**

1)  Aller sur **[SonarCloud](https://sonarcloud.io/)**

2) Sélectionner ton projet

3)  Aller dans Quality Gates

4)  Modifier ou créer une Quality Gate, le but est d'avoir:

-  **Bugs** → 0 bloquant

-  **Vulnérabilités** → 0 critique

-  **Coverage** → Min 80%

-  **Code Smells** → Max toléré


## **4. Tester et Valider**

- Faire un commit et observer le workflow sur GitHub Actions

- Si la Quality Gate échoue, le workflow s’arrête (`exit 1`)

- Si elle passe, le pipeline continue normalement
