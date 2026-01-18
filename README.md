# tp-dockeroptimiser
    
  
## Étape 1   
### Commande: 
docker build -t app:old .  
### Explication: 
Construction de l'image Docker originale sans optimisation.
### Résultat :  
<img width="1423" height="1092" alt="image" src="https://github.com/user-attachments/assets/e5a5b275-0ebb-438b-940c-be4713a60951" />  

## Étape 2  
### Commande: 
docker images app:old 
### Explication: 
Vérification de la taille de l'image originale.
### Résultat :  
<img width="806" height="84" alt="image" src="https://github.com/user-attachments/assets/20120e9e-675c-4352-99be-e5b757e19a09" />  
  
## Étape 3  
### Commande:  
FROM node:latest -> FROM node:18-alpine
Supprimer RUN apt-get update && apt-get install -y build-essential ca-certificates locales && echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && locale-gen

### Explication: 
Problème:
- L'image Alpine Linux n'a pas 'apt-get'
- Utilise 'apk' comme gestionnaire de paquets

Solution:
- Suppression de la ligne inutile d'installation de paquets
- Les paquets (build-essential, locales) ne sont pas nécessaires pour l'application Node.js

Bénéfice:
- Correction de l'erreur de build
- Réduction supplémentaire de la taille
- Simplification du Dockerfile

### Test:
docker build -t app:etape1-fixe ."  
### Résultat :  
<img width="1607" height="634" alt="image" src="https://github.com/user-attachments/assets/8c1089d2-cf0d-422f-aa0f-29e04a85bb67" />  
  
## Étape 4  
### Commande: 
docker images app:step1-fixed
### Explication: 
Vérification de la taille de l'image optimisée après la première étape d'amélioration.   
### Résultat :  
<img width="922" height="78" alt="image" src="https://github.com/user-attachments/assets/d7f7f9ab-226c-4cbb-8d06-5cf2b98977a6" />  
