# tp-dockeroptimiser
    

| Image | Taille (Disk Usage) | Taille (Content) | Réduction vs Original | Analyse |
|-------|---------------------|------------------|----------------------|---------|
| `app:old` (Original) | 1.73 GB | 436 MB | Baseline | Image initiale non optimisée |
| `app:step1-fixed` | 214 MB | 51.2 MB | -87.6% |  Optimisation réussie |
| `app:step2` | 237 MB | 55.9 MB | -86.3% |   Problème identifié |


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
Supprimer: RUN apt-get update && apt-get install -y build-essential ca-certificates locales && echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && locale-gen

### Explication: 
- **Problème :** Alpine Linux utilise `apk`, pas `apt-get`
- **Solution :** Suppression des paquets systèmes non nécessaires
- **Bénéfice :** Correction d'erreur de build + réduction de taille
### Test:
docker build -t app:etape1-fixe .
### Résultat :  
<img width="1607" height="634" alt="image" src="https://github.com/user-attachments/assets/8c1089d2-cf0d-422f-aa0f-29e04a85bb67" />  
  
## Étape 4  
### Commande: 
docker images app:step1-fixed
### Explication: 
Vérification de la taille de l'image optimisée après la première étape d'amélioration.   
### Résultat :  
<img width="922" height="78" alt="image" src="https://github.com/user-attachments/assets/d7f7f9ab-226c-4cbb-8d06-5cf2b98977a6" />  

## Étape 5  
### Modifications :  
- `COPY node_modules ./node_modules` → `COPY package*.json ./`
- `COPY . /app` → `COPY . .`
- `EXPOSE 3000 4000 5000` → `EXPOSE 3000`
- Ajout du fichier `.dockerignore`

### Explication: 
**Explication :**
- **Objectif :** Optimiser l'utilisation du cache Docker
- **Principe :** Copier d'abord `package.json`, installer les dépendances, puis copier le reste
- **Avantage :** Si `package.json` ne change pas, Docker réutilise le cache de `npm install`
- **.dockerignore :** Exclure les fichiers inutiles du contexte de build

### Test:
docker build -t app:step2 .
### Résultat :  
<img width="1557" height="627" alt="image" src="https://github.com/user-attachments/assets/3713ba4b-9136-4cf0-8fc7-d09c07748d8e" />  

## Étape 6    
### Commande: 
docker images app:step2
### Explication: 
Comparaison de la taille après l'optimisation du cache  
### Résultat :  
<img width="1180" height="96" alt="image" src="https://github.com/user-attachments/assets/5502f86d-5aab-4bdb-a95e-72fc24d3e286" />  
### Analyse :
Augmentation inattendue de 23 MB par rapport à l'étape 1  

## Étape 7  
### Commande: 
docker images
### Explication: 
Vue d'ensemble de toutes les images construites    
### Résultat :  
<img width="1404" height="125" alt="image" src="https://github.com/user-attachments/assets/ad584fb7-0727-4e9f-a993-4499ab5ad51d" />  

### Problème rencontré:
- La taille de l'image a augmenté à cause de node_modules présent localement
- .dockerignore n'a pas fonctionné comme prévu
