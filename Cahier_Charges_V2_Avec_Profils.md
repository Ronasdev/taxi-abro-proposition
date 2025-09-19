# Cahier des Charges - TAXI ABRO Version 2 (Avec Profils Utilisateur)

## 📋 Informations Générales

### Contexte du Projet
- **Client** : TAXI ABRO
- **Localisation** : L'Assomption, Québec, Canada
- **Type de service** : Transport taxi longue distance (jusqu'à 650 km)
- **Zone de couverture** : Canada et États-Unis
- **Version** : 2 - Site avec authentification et profils utilisateur

### Objectifs du Projet
- Refonte complète du site web existant (https://taxiabro.ca/)
- Création d'une interface moderne inspirée de Yango
- Système d'authentification et profils utilisateur
- Historique des réservations et gestion personnalisée
- Fidélisation client avec programme de points

## 🎨 Design et Interface Utilisateur

### Inspiration Design
- **Référence principale** : Yango (https://yango.com/fr_ma/)
- **Style** : Interface propre, moderne et professionnelle
- **Couleurs** : Palette basée sur les couleurs actuelles de TaxiAbro avec amélioration
- **Typographie** : Polices modernes et lisibles (similaires à Yango)

### Charte Graphique
- **Couleurs principales** : 
  - Bleu professionnel (couleur de confiance)
  - Jaune/Orange (couleur taxi traditionnelle)
  - Blanc et gris pour les éléments neutres
  - Vert pour les éléments de validation
- **Polices** : 
  - Titre : Police moderne sans-serif
  - Texte : Police lisible pour le contenu
- **Iconographie** : Icônes cohérentes et professionnelles
- **Images** : Photos de qualité professionnelle des véhicules et services

## 👤 Système d'Authentification

### Inscription Utilisateur
- **Méthodes d'inscription** :
  - Email + mot de passe
  - Inscription via Google
  - Inscription via Facebook
- **Informations requises** :
  - Nom complet
  - Email
  - Téléphone
  - Mot de passe sécurisé
- **Validation** :
  - Confirmation par email
  - Vérification du numéro de téléphone (SMS)

### Connexion Utilisateur
- **Méthodes de connexion** :
  - Email/téléphone + mot de passe
  - Connexion via Google
  - Connexion via Facebook
  - Option "Se souvenir de moi"
- **Récupération de mot de passe** :
  - Réinitialisation par email
  - Réinitialisation par SMS
  - Questions de sécurité

### Sécurité
- **Chiffrement** des mots de passe (bcrypt)
- **Authentification à deux facteurs** (optionnelle)
- **Sessions sécurisées** avec timeout
- **Protection contre** les attaques par force brute

## 📱 Structure du Site

### 1. Page d'Accueil
#### Hero Section
- **Titre principal** : "TAXI ABRO - Transport Longue Distance"
- **Promotion visible** : "PROMOTION : TARIF FIXE !! Taxes incluses" (mise en évidence)
- **Sous-titre** : "Vos navettes pour l'aéroport et les longues distances"
- **CTA principal** : Bouton "Réserver maintenant" bien visible
- **Boutons d'authentification** : Connexion/Inscription (coin supérieur droit)

#### Sections principales
1. **Services en vedette**
2. **Pourquoi nous choisir**
3. **Zones desservies**
4. **Témoignages clients**
5. **Contact rapide**

### 2. Espace Utilisateur Connecté

#### Menu Utilisateur
- **Avatar** et nom d'utilisateur
- **Menu déroulant** :
  - Mon profil
  - Mes réservations
  - Mes points fidélité
  - Paramètres
  - Déconnexion

#### Dashboard Utilisateur
- **Résumé du compte** :
  - Nombre de trajets effectués
  - Points de fidélité accumulés
  - Prochaine réservation
- **Réservations récentes** (3 dernières)
- **Raccourcis** vers actions fréquentes

### 3. Page Profil Utilisateur

#### Informations Personnelles
- **Données de base** :
  - Photo de profil (upload)
  - Nom complet
  - Email
  - Téléphone
  - Date de naissance
  - Adresse principale
- **Préférences** :
  - Type de véhicule préféré
  - Notifications (email/SMS)
  - Langue d'interface
  - Fuseau horaire

#### Adresses Favorites
- **Domicile** : Adresse principale
- **Travail** : Adresse professionnelle
- **Autres** : Jusqu'à 5 adresses personnalisées
- **Géolocalisation** automatique pour chaque adresse

#### Moyens de Paiement
- **Cartes enregistrées** :
  - Visa, MasterCard, American Express
  - Masquage des numéros (****1234)
  - Date d'expiration
- **PayPal** : Compte lié
- **Méthode par défaut** sélectionnable

### 4. Page Mes Réservations

#### Historique Complet
- **Filtres** :
  - Par date (derniers 30 jours, 3 mois, année)
  - Par statut (confirmée, en cours, terminée, annulée)
  - Par type de service
- **Informations par réservation** :
  - Numéro de réservation
  - Date et heure
  - Trajet (départ → destination)
  - Chauffeur assigné
  - Véhicule
  - Prix payé
  - Statut

#### Actions Disponibles
- **Réserver à nouveau** (trajet identique)
- **Télécharger facture** (PDF)
- **Évaluer le service** (si non fait)
- **Signaler un problème**
- **Annuler** (selon conditions)

#### Réservations Futures
- **Détails complets** de la réservation
- **Suivi en temps réel** le jour J
- **Modification** possible (selon conditions)
- **Rappels automatiques** (24h, 2h avant)

### 5. Programme de Fidélité

#### Système de Points
- **Gain de points** :
  - 1 point par dollar dépensé
  - Bonus pour trajets longue distance
  - Points bonus anniversaire
  - Parrainage d'amis
- **Utilisation des points** :
  - Réduction sur prochaine course
  - Surclassement gratuit
  - Services additionnels gratuits

#### Niveaux de Fidélité
- **Bronze** (0-499 points) : Avantages de base
- **Argent** (500-1499 points) : 5% de réduction
- **Or** (1500+ points) : 10% de réduction + priorité

### 6. Page Réservation (Version Connectée)

#### Formulaire Pré-rempli
- **Informations personnelles** automatiquement remplies
- **Adresses favorites** en sélection rapide
- **Méthode de paiement** par défaut sélectionnée
- **Préférences** appliquées automatiquement

#### Fonctionnalités Avancées
- **Sauvegarde automatique** du formulaire
- **Réservation récurrente** (trajets réguliers)
- **Partage de trajet** avec d'autres utilisateurs
- **Demandes spéciales** sauvegardées

#### Confirmation Améliorée
- **Sauvegarde automatique** dans l'historique
- **Notifications** personnalisées
- **Ajout au calendrier** personnel
- **Partage** avec contacts d'urgence

## 🔧 Fonctionnalités Techniques Avancées

### Notifications Personnalisées
- **Email** :
  - Confirmation de réservation
  - Rappels personnalisés
  - Offres spéciales ciblées
  - Newsletter mensuelle
- **SMS** :
  - Confirmation immédiate
  - Rappel 24h avant
  - Arrivée du chauffeur
  - Code de sécurité
- **Notifications push** (si app mobile future)

### Géolocalisation Avancée
- **Localisation automatique** pour adresses
- **Historique des lieux** fréquents
- **Suggestions intelligentes** basées sur l'historique
- **Calcul optimisé** des trajets

### Système de Recommandation
- **Trajets suggérés** basés sur l'historique
- **Horaires optimaux** selon les habitudes
- **Services recommandés** selon le profil
- **Offres personnalisées**

### API et Intégrations
- **Calendrier** (Google Calendar, Outlook)
- **Contacts** pour partage de trajet
- **Réseaux sociaux** pour partage d'expérience
- **Applications tierces** (voyage, business)

## 📊 Analytics et Suivi Utilisateur

### Données Collectées
- **Comportement de navigation**
- **Préférences de réservation**
- **Fréquence d'utilisation**
- **Satisfaction client** (enquêtes)

### Rapports Utilisateur
- **Statistiques personnelles** :
  - Nombre de trajets par mois
  - Distance totale parcourue
  - Économies réalisées
  - Empreinte carbone
- **Comparaisons** avec moyennes
- **Objectifs personnels** (trajets, économies)

## 🎯 Fonctionnalités Spécifiques Version 2

### Réservation Express
- **Un clic** pour trajets favoris
- **Réservation récurrente** automatique
- **Modification rapide** des détails
- **Confirmation instantanée**

### Partage de Trajet
- **Invitation** d'autres utilisateurs
- **Partage des coûts** automatique
- **Coordination** des horaires
- **Chat intégré** entre passagers

### Support Client Personnalisé
- **Historique des conversations**
- **Contexte automatique** (dernières réservations)
- **Priorité** selon niveau de fidélité
- **Chat en direct** avec historique

### Offres Personnalisées
- **Promotions ciblées** selon profil
- **Réductions anniversaire**
- **Offres de parrainage**
- **Tarifs préférentiels** pour fidèles

## 📱 Interface Mobile Optimisée

### Fonctionnalités Mobile Spécifiques
- **Touch ID/Face ID** pour connexion rapide
- **Géolocalisation** en temps réel
- **Notifications push** natives
- **Mode hors ligne** pour consultation historique

### Progressive Web App (PWA)
- **Installation** sur écran d'accueil
- **Fonctionnement hors ligne** partiel
- **Synchronisation** automatique
- **Performance** native

## 💰 Estimation Budgétaire Version 2

### Coûts de Développement
- **Design et UX/UI** : 20 000$ - 25 000$ CAD
- **Système d'authentification** : 8 000$ - 12 000$ CAD
- **Profils utilisateur** : 10 000$ - 15 000$ CAD
- **Programme fidélité** : 6 000$ - 10 000$ CAD
- **Développement frontend/backend** : 35 000$ - 45 000$ CAD
- **Intégrations avancées** : 12 000$ - 18 000$ CAD
- **Tests et déploiement** : 8 000$ - 12 000$ CAD
- **TOTAL** : 99 000$ - 137 000$ CAD

### Coûts Récurrents (Annuels)
- **Hébergement et base de données** : 3 000$ - 5 000$ CAD
- **Services tiers** (email, SMS) : 2 000$ - 4 000$ CAD
- **Maintenance et mises à jour** : 12 000$ - 18 000$ CAD
- **Support client** : 6 000$ - 10 000$ CAD
- **TOTAL ANNUEL** : 23 000$ - 37 000$ CAD

## ⏱️ Planning Estimé Version 2

### Phase 1 : Design et Architecture (4-5 semaines)
- Analyse des besoins utilisateur
- Architecture système d'authentification
- Maquettes avec espaces utilisateur
- Validation client

### Phase 2 : Développement Core (8-10 semaines)
- Système d'authentification
- Profils utilisateur
- Interface utilisateur connectée
- Base de données utilisateurs

### Phase 3 : Fonctionnalités Avancées (6-8 semaines)
- Programme de fidélité
- Notifications personnalisées
- Historique et analytics
- Intégrations tierces

### Phase 4 : Tests et Déploiement (2-3 semaines)
- Tests utilisateur
- Tests de charge
- Formation client
- Mise en production

## 🎯 Critères de Réussite Version 2

### Métriques Techniques
- **Performance** : Score Lighthouse > 85
- **Sécurité** : Audit sécurité sans faille critique
- **Disponibilité** : Uptime > 99.5%
- **Temps de réponse** : < 2 secondes

### Métriques Utilisateur
- **Taux d'inscription** : > 60% des visiteurs
- **Taux de rétention** : > 70% à 30 jours
- **Satisfaction** : NPS > 60
- **Utilisation des fonctionnalités** : > 80% utilisent le profil

### Objectifs Business
- **Fidélisation** : +150% de clients récurrents
- **Panier moyen** : +25% grâce aux profils
- **Réservations** : +400% par rapport au site actuel
- **Réduction coûts support** : -30% grâce à l'automatisation

---

*Ce cahier des charges Version 2 ajoute une dimension personnalisée et fidélisante au service, créant une relation durable avec les clients tout en optimisant l'expérience utilisateur.*
