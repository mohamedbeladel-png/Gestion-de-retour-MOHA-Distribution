# 💧 Moha Distribution — CRM Gestion des Retours Client

> Application web de gestion des retours produits pour **Moha Distribution**, spécialiste en production et distribution d'eau minérale au Maroc.

---

## 🌊 À propos du projet

**Moha Distribution CRM** est une application web métier conçue pour digitaliser et simplifier le processus de gestion des retours client. Elle permet aux clients de soumettre leurs demandes de retour en ligne, aux agents de les traiter efficacement, et aux managers de piloter la performance du service via des tableaux de bord dédiés.

Le projet couvre la gamme complète des produits Moha Distribution :

| Format | Référence | Conditionnement |
|--------|-----------|-----------------|
| 0.33 L | MD-033 | Carton de 24 unités |
| 0.5 L  | MD-050 | Carton de 12 unités |
| 1.5 L  | MD-150 | Carton de 6 unités  |
| 5 L    | MD-005 | Bidon               |
| 20 L   | MD-020 | Bonbonne rechargeable |
| 100 L  | MD-100 | Citerne / usage professionnel |

---

## ✨ Fonctionnalités principales

### Pour le client
- **Connexion sécurisée** avec identifiant email ou N° client
- **Saisie des retours** : tableau interactif par format de bouteille avec motif par ligne
- **Génération automatique** d'un bon de retour numéroté (BR-AAAA-XXXX)
- **Impression et export PDF** du bon de retour (format A4)
- **QR code de suivi** sur chaque bon pour consultation en ligne
- **Feedback étoiles** : évaluation du service en 3 critères après clôture du retour

### Pour l'agent
- Tableau de bord des demandes à traiter
- Mise à jour du statut des bons de retour
- Historique des activités par client

### Pour le manager
- KPIs globaux : volume de retours, taux de traitement, délais
- Dashboard des feedbacks avec alertes automatiques (note ≤ 2 étoiles)
- Rapports mensuels par produit et par commercial

---

## 🔄 Workflow d'un retour

```
Client connecté
    → Saisie des quantités par produit + motif
    → Validation et confirmation
    → Bon de retour BR-XXXX généré automatiquement
    → Impression PDF + envoi email
    → Traitement par l'agent (EN_ATTENTE → EN_COURS → VALIDÉ)
    → Feedback étoiles du client (si retour clôturé)
```

**Statuts du bon de retour :**
`EN_ATTENTE` → `EN_COURS` → `VALIDÉ` → `CLÔTURÉ`  
`EN_COURS` → `REJETÉ`

---

## 🛠️ Stack technique

| Couche | Technologie |
|--------|------------|
| Frontend | React.js + Tailwind CSS |
| Backend | Node.js + Express.js |
| Base de données | PostgreSQL |
| Authentification | JWT + bcrypt |
| Génération PDF | jsPDF |
| QR code | qrcode.js |
| Envoi email | Nodemailer |
| Upload fichiers | Multer |
| Déploiement front | Vercel |
| Déploiement back | Railway |

---

## 🗂️ Structure du projet

```
moha-crm/
├── src/
│   ├── pages/
│   │   ├── LoginPage.jsx
│   │   ├── client/
│   │   │   └── DashboardPage.jsx
│   │   └── manager/
│   │       └── FeedbackDashboard.jsx
│   ├── components/
│   │   ├── auth/
│   │   │   ├── LoginForm.jsx
│   │   │   └── BrandPanel.jsx
│   │   ├── client/
│   │   │   ├── WelcomeBanner.jsx
│   │   │   ├── RetourForm.jsx
│   │   │   ├── RetourTableau.jsx
│   │   │   ├── RecapStickyBar.jsx
│   │   │   ├── ConfirmModal.jsx
│   │   │   ├── FeedbackWidget.jsx
│   │   │   ├── StarRating.jsx
│   │   │   └── FeedbackTags.jsx
│   │   └── shared/
│   │       ├── BonRetour.jsx
│   │       └── BonRetourPrint.css
│   ├── hooks/
│   │   ├── useAuth.js
│   │   ├── useRetourForm.js
│   │   └── useFeedback.js
│   ├── api/
│   │   ├── authApi.js
│   │   ├── retourApi.js
│   │   └── feedbackApi.js
│   └── i18n/
│       ├── fr.json
│       └── ar.json
├── backend/
│   ├── routes/
│   │   ├── auth.js
│   │   ├── retours.js
│   │   └── feedbacks.js
│   ├── controllers/
│   │   ├── authController.js
│   │   ├── retourController.js
│   │   └── feedbackController.js
│   ├── services/
│   │   ├── pdfService.js
│   │   ├── emailService.js
│   │   └── qrService.js
│   └── middleware/
│       └── rateLimiter.js
└── migrations/
    ├── 001_create_users.sql
    ├── 002_create_clients.sql
    ├── 003_create_produits.sql
    ├── 004_create_retours.sql
    ├── 005_create_retours_lignes.sql
    ├── 006_create_retours_photos.sql
    ├── 007_create_feedbacks.sql
    └── seed_data.sql
```

---

## 🚀 Installation et démarrage

### Prérequis

- Node.js ≥ 18
- PostgreSQL ≥ 14
- npm ou yarn

### 1. Cloner le dépôt

```bash
git clone https://github.com/mohadistribution/crm-retours.git
cd crm-retours
```

### 2. Installer les dépendances

```bash
# Frontend
npm install

# Backend
cd backend && npm install
```

### 3. Configurer les variables d'environnement

Créer un fichier `.env` à la racine du backend :

```env
# Base de données
DATABASE_URL=postgresql://user:password@localhost:5432/moha_crm

# JWT
JWT_SECRET=votre_secret_jwt
JWT_REFRESH_SECRET=votre_refresh_secret
JWT_EXPIRES_IN=15m
JWT_REFRESH_EXPIRES_IN=30d

# Email
SMTP_HOST=smtp.votre-fournisseur.ma
SMTP_PORT=587
SMTP_USER=support@mohadistribution.ma
SMTP_PASS=votre_mot_de_passe

# Application
PORT=3001
CLIENT_URL=http://localhost:3000
NODE_ENV=development
```

### 4. Initialiser la base de données

```bash
# Exécuter les migrations dans l'ordre
psql -U postgres -d moha_crm -f migrations/001_create_users.sql
psql -U postgres -d moha_crm -f migrations/002_create_clients.sql
psql -U postgres -d moha_crm -f migrations/003_create_produits.sql
psql -U postgres -d moha_crm -f migrations/004_create_retours.sql
psql -U postgres -d moha_crm -f migrations/005_create_retours_lignes.sql
psql -U postgres -d moha_crm -f migrations/006_create_retours_photos.sql
psql -U postgres -d moha_crm -f migrations/007_create_feedbacks.sql

# Charger les données de démonstration (optionnel)
psql -U postgres -d moha_crm -f migrations/seed_data.sql
```

### 5. Lancer l'application

```bash
# Terminal 1 — Backend (port 3001)
cd backend && npm run dev

# Terminal 2 — Frontend (port 3000)
npm run dev
```

L'application est accessible sur `http://localhost:3000`

---

## 👥 Comptes de démonstration

| Rôle | Email | Mot de passe |
|------|-------|-------------|
| Client | client@demo.ma | Demo@2025 |
| Agent | agent@mohadistribution.ma | Demo@2025 |
| Manager | manager@mohadistribution.ma | Demo@2025 |
| Admin | admin@mohadistribution.ma | Demo@2025 |

---

## 🌐 API — Endpoints principaux

### Authentification
```
POST   /api/auth/login              Connexion
POST   /api/auth/refresh            Renouvellement du token
POST   /api/auth/forgot-password    Réinitialisation mot de passe
```

### Retours
```
GET    /api/client/profile          Profil client connecté
GET    /api/client/commandes        Commandes du client
GET    /api/client/retours          Historique des retours
POST   /api/retours/creer           Soumettre une demande
GET    /api/retours/:id/pdf         Télécharger le bon PDF
POST   /api/retours/:id/email       Envoyer le bon par email
```

### Feedbacks
```
POST   /api/feedbacks/soumettre     Soumettre une évaluation
GET    /api/feedbacks/stats         Statistiques (manager)
```

---

## 🎨 Charte graphique

| Élément | Couleur |
|---------|---------|
| Couleur principale | `#0077B6` — bleu eau minérale |
| Couleur secondaire | `#00B4D8` — bleu clair |
| Accent | `#90E0EF` — bleu très clair |
| Fond général | `#F0F9FF` — blanc bleuté |
| Texte principal | `#03045E` — bleu marine |
| Police (latin) | Inter — Google Fonts |
| Police (arabe) | Cairo — Google Fonts |

---

## ♿ Accessibilité & qualité

- Conformité **WCAG AA** : contraste, focus visible, aria-labels
- **Responsive** : 375 px · 768 px · 1280 px
- **Bilingue** : Français (défaut) + Arabe avec support RTL (`dir="rtl"`)
- **Skeleton loaders** sur tous les appels API
- **Sauvegarde automatique** du formulaire en localStorage (toutes les 30 s)
- Chargement initial **< 2 secondes**
- Aucune dépendance à licence payante

---

## 🗺️ Roadmap

- [x] Phase 1 — Prompts structurés (login, accueil, feedback)
- [ ] Phase 2 — Génération du code (Google AI Builder)
- [ ] Phase 3 — Interface agent back-office
- [ ] Phase 4 — Dashboard manager (KPIs, rapports)
- [ ] Phase 5 — Déploiement & tests utilisateurs

---

## 📄 Licence

Usage interne — © 2025 Moha Distribution. Tous droits réservés.

---

*Développé avec ❤️ pour Moha Distribution — "L'eau pure, le service excellence"*
