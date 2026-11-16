# Les Deux Anneaux — site & application

Salle dînatoire Les Deux Anneaux · @sallelesdeuxanneaux
Stack **MERN** : MongoDB · Express · React (Vite) · Node.js — d'après le cahier des charges v2.0 (05/09/2026).

---

## Démarrer (Windows, VS Code)

### 1. Installer une seule fois
- **Node.js 20 ou plus récent** (LTS) : https://nodejs.org
- **MongoDB** — au choix :
  - *Local* : MongoDB Community Server (https://www.mongodb.com/try/download/community), installé « as a service ». Rien d'autre à faire.
  - *En ligne* : un cluster gratuit MongoDB Atlas, puis collez son adresse dans `server/.env` (voir étape 3).

### 2. Ouvrir le projet
Dans VS Code : **Fichier → Ouvrir le dossier…** → `Documents\les-deux-anneaux`.
Puis ouvrez un terminal : **Terminal → Nouveau terminal**.

### 3. Configurer l'API
```powershell
copy server\.env.example server\.env
```
Les valeurs par défaut fonctionnent avec MongoDB en local. Sans SMTP ni SMS configurés, les e-mails et les codes OTP s'affichent dans le terminal.

### 4. Installer et lancer
```powershell
npm run install:all
npm run dev
```
Ouvrez **http://localhost:5173** — le site (port 5173) et l'API (port 5000) démarrent ensemble ; chaque modification enregistrée recharge la page.

> Sans MongoDB, le site s'affiche quand même ; les formulaires (contact, newsletter, connexion) répondent « service indisponible » jusqu'à ce que la base soit joignable.

### Mise en production
```powershell
npm run build   # compile le site dans client/dist
npm start       # Express sert le site ET l'API sur le port 5000
```
En production, définissez `NODE_ENV=production`, `JWT_SECRET`, `OTP_PEPPER` (longues chaînes aléatoires), `PUBLIC_URL`, SMTP et le fournisseur SMS.

---

## Organisation

```
les-deux-anneaux/
├─ shared/            Code commun client + serveur
│  ├─ catalog.js      Tarifs, packs, options, créneaux, types d'événements  ← À COMPLÉTER
│  └─ pricing.js      Moteur de prix unique (estimateur = serveur = pro forma)
├─ server/            API Express + MongoDB (Mongoose)
│  └─ src/
│     ├─ models/      Contact, Abonnés, Utilisateurs, OTP, Réservations, Verrous de créneau, Avis
│     ├─ routes/      public (devis, disponibilités, avis, suivi), forms (contact, newsletter), auth
│     └─ lib/         validation, e-mails (Nodemailer), SMS, jetons
└─ client/            Site React (Vite)
   ├─ public/brand/   Logos officiels (dark, light, favicon) + icônes
   └─ src/
      ├─ context/     LanguageContext, ThemeContext, CurrencyContext, CartContext, AuthContext
      ├─ i18n/        fr.js · ar.js (RTL) · en.js
      ├─ components/  En-tête, pied de page, estimateur, calendrier…
      └─ pages/       P-01 → P-14 et A-01 → A-04
```

## Phase 1 — livrée

| Code | Page | Adresse |
|---|---|---|
| P-01 | Accueil | `/fr` |
| P-02 | La salle | `/fr/la-salle` |
| P-03 | Services (ajout au panier) | `/fr/services` |
| P-04 | Packs & tarifs + estimateur | `/fr/packs-tarifs` |
| P-05 | Types d'événements | `/fr/evenements/[type]` |
| P-06 | Réservation : calendrier + étapes | `/fr/reservation` |
| P-07 | Galerie (filtres, lightbox, likes) | `/fr/galerie` |
| P-08 | Avis | `/fr/avis` |
| P-09 | Partenaires | `/fr/partenaires` |
| P-10 | Contact / rendez-vous → table Messages | `/fr/contact` |
| P-11 | Newsletter (double opt-in) → table Abonnés | `/fr/newsletter` |
| P-12 | FAQ | `/fr/faq` |
| P-13 | Suivi sans compte | `/fr/suivi/[token]` |
| P-14 | Légal | `/fr/legal/*` |
| A-01…A-04 | Connexion, inscription, OTP, mot de passe oublié | `/fr/connexion`… |

Chaque page existe en `/fr`, `/ar` (miroir droite-à-gauche) et `/en`.

Déjà en place côté serveur : verrou **unique date + créneau + espace** (impossible de réserver deux fois le même créneau), OTP SMS 6 chiffres (5 min, 3 essais, délai de renvoi 60 s), sessions en cookies httpOnly (compte 7 j, invité 30 j), limitation des tentatives (connexion, OTP, contact, newsletter), vérification serveur de chaque estimation.

## Données à remplacer avant la mise en ligne (CDC §9)

Tout est marqué `PLACEHOLDER` dans le code :
- `shared/catalog.js` : grilles de prix résidents et diaspora, packs, options, capacités, horaires, téléphone, WhatsApp, e-mail, adresse, taux EUR/USD
- `client/src/pages/Legal.jsx` : NIF / RC, politique d'annulation, hébergeur
- `client/src/i18n/ar.js` : relecture des textes arabes
- Photos HD (les visuels actuels sont provisoires)
- Accès SMTP et fournisseur SMS algérien (`server/.env`, `server/src/lib/sms.js`)

## Prochaines phases (CDC §8)

- **Phase 2** — funnel de réservation complet (OTP → hold 20 min → pro forma PDF), paiement CCP / BaridiMob + preuve, espace client U-01 → U-10
- **Phase 3** — espace client U-11 → U-20, administration AD-01 → AD-12
- **Phase 4** — super-admin SA-01 → SA-12, tracking, PWA, recette
