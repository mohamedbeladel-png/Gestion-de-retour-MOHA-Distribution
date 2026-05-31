# Compte rendu de projet — CRM Moha Distribution

**Application web de gestion des retours client · Eau minérale**  
Date : 31 mai 2025 · Rédigé avec Claude AI  
Statut : **Prompts validés ✓**

---

## Résumé exécutif

| Indicateur | Valeur |
|---|---|
| Prompts Google AI Builder générés | 3 |
| Fichiers à générer (code) | 27 |
| Formats de produits couverts | 6 (0.33 L → 100 L) |
| Rôles utilisateurs définis | 4 |
| Langues supportées | Français + Arabe (RTL) |

---

## 1. Présentation du projet

### Contexte

**Société :** Moha Distribution  
**Secteur :** Production et distribution d'eau minérale  
**Marché :** Maroc — région Casablanca-Settat  
**Objectif :** Créer une application web CRM permettant aux clients de soumettre des demandes de retour produit, aux agents de les traiter, et aux managers de suivre les performances.

### Gamme de produits concernés

| Référence | Désignation | Conditionnement |
|---|---|---|
| MD-033 | Eau minérale 0.33 L | Carton de 24 unités |
| MD-050 | Eau minérale 0.5 L | Carton de 12 unités |
| MD-150 | Eau minérale 1.5 L | Carton de 6 unités |
| MD-005 | Eau minérale 5 L | Bidon |
| MD-020 | Eau minérale 20 L | Bonbonne rechargeable |
| MD-100 | Eau minérale 100 L | Citerne / usage professionnel |

### Rôles utilisateurs

- **CLIENT** — soumet et suit ses demandes de retour
- **AGENT** — traite et met à jour les réclamations assignées
- **MANAGER** — vue globale, rapports, gestion de l'équipe
- **SUPER_ADMIN** — configuration complète de l'application

### Motifs de retour prédéfinis

1. Produit défectueux / bouteille endommagée
2. Date de péremption dépassée
3. Erreur de commande (mauvais format livré)
4. Surstock / excédent de commande
5. Non-conformité qualité (goût, couleur, odeur)
6. Problème de scellage / bouchon
7. Autre (champ libre obligatoire)

---

## 2. Modules développés

### Module 1 — Page de connexion (`/login`)

**Prompt :** fourni et validé  
**Layout :** deux colonnes desktop — panneau de marque (40%) + formulaire (60%)

**Panneau de marque (gauche) :**
- Fond bleu #0077B6 avec motif vagues
- Logo Moha Distribution + slogan "L'eau pure, le service excellence"
- Affichage de la gamme : 0.33 L · 0.5 L · 1.5 L · 5 L · 20 L · 100 L
- Footer © 2025 Moha Distribution

**Formulaire (droite) :**
- Champ email / N° client avec validation regex
- Champ mot de passe avec bouton afficher/masquer
- Checkbox "Se souvenir de moi" (30 jours)
- Lien "Mot de passe oublié ?" → `/forgot-password`
- Bouton "Se connecter" avec état de chargement
- Lien activation premier accès → `/activate-account`
- Switcher langue [FR] [AR]

**Sécurité :**
- Rate limiting : 5 tentatives / 15 min par IP
- Verrouillage après 10 échecs (email de déblocage)
- Stockage JWT : httpOnly cookie (refresh) + localStorage (access)
- Pages annexes : `/forgot-password`, `/reset-password`, `/activate-account`

---

### Module 2 — Page d'accueil client (`/client/dashboard`)

**Prompt :** fourni et validé

**Zone A — Bandeau de bienvenue :**
- Message personnalisé "Bonjour [Prénom]"
- 3 métriques : retours soumis / en traitement / validés
- Badge statut compte + numéro client CLI-XXXX

**Zone B — Formulaire de saisie retour :**

*Étape 1 — Informations de la demande :*
- N° de commande de référence (format CMD-AAAA-XXXX)
- Date de livraison (date picker)
- Canal de livraison (Livraison directe / Point relais / Enlèvement)
- Priorité (Normale / Urgente)

*Étape 2 — Tableau de saisie des quantités :*

| Produit | Référence | Qté commandée | Qté à retourner | Motif |
|---|---|---|---|---|
| 0.33 L | MD-033 | auto | input number | select |
| 0.5 L | MD-050 | auto | input number | select |
| 1.5 L | MD-150 | auto | input number | select |
| 5 L | MD-005 | auto | input number | select |
| 20 L | MD-020 | auto | input number | select |
| 100 L | MD-100 | auto | input number | select |

Règles de validation :
- Quantité à retourner ≤ quantité commandée (erreur inline rouge sinon)
- Au moins une ligne avec qté > 0 obligatoire
- Si motif = "Autre" → champ texte libre obligatoire
- Lignes grisées si qté commandée = 0

*Étape 3 — Observations :*
- Textarea commentaire libre (max 500 caractères, compteur visible)
- Upload photos (optionnel) : 3 fichiers max, 5 Mo chacun, drag & drop

**Barre sticky de validation :**
- Total articles à retourner
- Bouton "Réinitialiser" (outline, rouge)
- Bouton "Valider la demande" (#0077B6, 48 px)
- Modal de confirmation avant soumission
- Toast succès ou erreur après réponse API

**Sauvegarde automatique :** brouillon en localStorage toutes les 30 secondes

---

### Module 3 — Bon de retour (impression / PDF)

**Prompt :** fourni et validé  
**N° auto-généré :** BR-AAAA-XXXX  
**Format :** A4 portrait, marges 20 mm

**Structure du document :**

1. En-tête : Logo Moha + N° bon + date + statut
2. Informations client : nom/raison sociale, N° client, adresse, téléphone, email
3. Commande de référence : N° CMD + date livraison + canal
4. Tableau des articles : Référence · Désignation · Qté retour · Motif
5. Total articles à retourner
6. Observations (commentaire libre)
7. Zone signatures : Client / Agent Moha / Cachet / Date
8. QR code de suivi + footer société

**Actions disponibles après génération :**
- **Imprimer** → `window.print()` + CSS `@media print`
- **Télécharger PDF** → bibliothèque jsPDF
- **Envoyer par email** → nodemailer (client + agent en copie)
- **Partager** → copie du lien de consultation en ligne

**Règle CSS `@media print` :** masquer header, navigation, boutons d'action, footer site — afficher uniquement le bloc bon de retour.

---

### Module 4 — Feedback avec étoiles

**Prompt :** fourni et validé  
**Affichage :** uniquement si le client a au moins 1 retour clôturé (statut VALIDÉ ou CLÔTURÉ) et n'a pas encore donné son avis.

**3 critères d'évaluation (1 à 5 étoiles chacun) :**

| Critère | Labels (1 → 5 étoiles) |
|---|---|
| Rapidité de traitement | Très lent · Lent · Correct · Rapide · Excellent ! |
| Qualité de la prise en charge | Très insatisfait · Insatisfait · Satisfait · Très satisfait · Excellent ! |
| Facilité d'utilisation | Très difficile · Difficile · Acceptable · Facile · Très facile |

**Tags de sentiment** (affichés si note globale ≤ 3) :
- Délai trop long · Réponse insuffisante · Formulaire compliqué
- Manque de communication · Problème non résolu · Autre

**Options :**
- Textarea commentaire libre (max 300 caractères)
- Checkbox "Soumettre anonymement"
- Bouton désactivé tant qu'aucune étoile n'est sélectionnée

**État après envoi :** confirmation visuelle + étoiles en lecture seule + bouton "Modifier mon avis" (modifiable 48 h)

**Règle métier :** si note globale ≤ 2 → alerte email automatique au manager.

**Sur le bon de retour imprimé :** ajout d'un QR code vers `/feedback/BR-XXXX` et d'une grille de notation papier (5 étoiles vides par critère à entourer).

---

## 3. Stack technique

### Architecture générale

```
Client (navigateur)
    ↓ HTTPS
React.js + Tailwind CSS  (Vercel)
    ↓ API REST / JSON
Node.js + Express.js     (Railway / Render)
    ↓ SQL
PostgreSQL               (Railway / Supabase)
```

### Technologies retenues

| Couche | Technologie | Rôle |
|---|---|---|
| Frontend | React.js + Tailwind CSS | UI responsive, mobile-first |
| Backend | Node.js + Express.js | API REST |
| Base de données | PostgreSQL | Persistance des données |
| Authentification | JWT + bcrypt | Sécurité sessions |
| PDF | jsPDF | Génération bon de retour |
| QR code | qrcode.js | Lien de suivi |
| Email | Nodemailer | Envoi bons + alertes |
| Upload | Multer | Photos réclamations |
| Hébergement front | Vercel | Déploiement continu |
| Hébergement back | Railway | API + PostgreSQL |

### Charte graphique

| Élément | Valeur |
|---|---|
| Couleur principale | #0077B6 (bleu eau minérale) |
| Couleur secondaire | #00B4D8 (bleu clair) |
| Accent | #90E0EF (bleu très clair) |
| Fond général | #F0F9FF (blanc bleuté) |
| Texte principal | #03045E (bleu marine) |
| Police latin | Inter (Google Fonts) |
| Police arabe | Cairo (Google Fonts, RTL) |

---

## 4. Base de données — tables SQL

| Migration | Table | Description |
|---|---|---|
| 001 | `users` | Comptes utilisateurs (tous rôles) |
| 002 | `clients` | Profils clients avec N° CLI-XXXX |
| 003 | `produits` | Catalogue 6 références MD-xxx |
| 004 | `retours` | En-têtes des bons de retour |
| 005 | `retours_lignes` | Lignes détail (produit, qté, motif) |
| 006 | `retours_photos` | Photos jointes aux réclamations |
| 007 | `feedbacks` | Évaluations étoiles par retour |
| — | `login_logs` | Journal des connexions (IP, résultat) |

### Workflow statuts du bon de retour

```
EN_ATTENTE → EN_COURS → VALIDÉ → CLÔTURÉ
                      ↘ REJETÉ
```

---

## 5. API REST — endpoints principaux

### Authentification
```
POST /api/auth/login           Connexion client/agent/manager
POST /api/auth/refresh         Renouvellement du token
POST /api/auth/forgot-password Envoi lien de réinitialisation
POST /api/auth/reset-password  Nouveau mot de passe
```

### Retours
```
GET  /api/client/profile                  Profil du client connecté
GET  /api/client/commandes?client_id=X    Commandes (pour pré-remplissage)
GET  /api/client/retours?client_id=X      Historique des bons de retour
POST /api/retours/creer                   Soumettre une demande
GET  /api/retours/:id/pdf                 Télécharger le PDF
POST /api/retours/:id/email               Envoyer par email
```

### Feedbacks
```
POST /api/feedbacks/soumettre   Soumettre un avis étoiles
GET  /api/feedbacks/stats       Statistiques agrégées (manager)
```

---

## 6. Inventaire des fichiers à livrer

### Front-end — `src/`

**Pages**
- `pages/LoginPage.jsx`
- `pages/client/DashboardPage.jsx`
- `pages/manager/FeedbackDashboard.jsx`

**Composants auth**
- `components/auth/LoginForm.jsx`
- `components/auth/BrandPanel.jsx`

**Composants client**
- `components/client/WelcomeBanner.jsx`
- `components/client/RetourForm.jsx`
- `components/client/RetourTableau.jsx`
- `components/client/RecapStickyBar.jsx`
- `components/client/ConfirmModal.jsx`
- `components/client/FeedbackWidget.jsx`
- `components/client/StarRating.jsx`
- `components/client/FeedbackTags.jsx`

**Composants partagés**
- `components/shared/BonRetour.jsx`
- `components/shared/BonRetourPrint.css`

**Hooks**
- `hooks/useAuth.js`
- `hooks/useRetourForm.js`
- `hooks/useFeedback.js`

**API clients**
- `api/authApi.js`
- `api/retourApi.js`
- `api/feedbackApi.js`

**Internationalisation**
- `i18n/fr.json`
- `i18n/ar.json`

### Back-end — `backend/`

- `routes/auth.js`
- `routes/retours.js`
- `routes/feedbacks.js`
- `controllers/authController.js`
- `controllers/retourController.js`
- `controllers/feedbackController.js`
- `services/pdfService.js`
- `services/emailService.js`
- `services/qrService.js`
- `middleware/rateLimiter.js`

### Migrations SQL — `migrations/`

- `001_create_users.sql`
- `002_create_clients.sql`
- `003_create_produits.sql`
- `004_create_retours.sql`
- `005_create_retours_lignes.sql`
- `006_create_retours_photos.sql`
- `007_create_feedbacks.sql`
- `seed_data.sql`

**Total : 27 fichiers de code + 8 migrations SQL**

---

## 7. Contraintes techniques transversales

- Code entièrement commenté en français
- Responsive : 375 px (mobile) · 768 px (tablette) · 1280 px (desktop)
- Accessibilité WCAG AA : aria-labels, focus visible, contraste suffisant
- Support bilingue FR / AR avec attribut `dir="rtl"` pour l'arabe
- Skeleton loaders pendant les appels API
- Sauvegarde automatique du brouillon en localStorage (toutes les 30 s)
- Aucune dépendance à licence payante
- Chargement initial < 2 secondes

---

## 8. Roadmap du projet

| Phase | Description | Statut |
|---|---|---|
| **Phase 1** | Prompts structurés (login, accueil, feedback) | ✅ Terminé |
| **Phase 2** | Génération du code via Google AI Builder | ⏳ Prochaine étape |
| **Phase 3** | Interface agent back-office (traitement retours) | 🔲 À faire |
| **Phase 4** | Dashboard manager (KPIs, rapports, alertes) | 🔲 À faire |
| **Phase 5** | Déploiement, tests utilisateurs, support RTL | 🔲 À faire |

### Prochaine action recommandée

Fournir les 3 prompts validés à **Google AI Builder** pour générer les 27 fichiers, puis passer à la **Phase 3 : interface agent** de traitement des bons de retour (liste des demandes, assignation, changement de statut, vue calendrier des activités).

---

*Compte rendu généré avec Claude AI (Anthropic) · Projet CRM Moha Distribution © 2025*
