# Wamarket.bet – Guide de déploiement & conversion APK

## 📁 Structure des fichiers

```
wamarket.bet/
├── index.html      → Application principale (SPA + PWA)
├── manifest.json   → Manifeste PWA (icônes, nom, couleurs)
├── sw.js           → Service Worker (cache offline)
└── README.md       → Ce fichier
```

---

## 🚀 Déploiement Web

### Option 1 – Netlify (recommandé, gratuit)
1. Créez un compte sur https://netlify.com
2. Glissez-déposez le dossier `wamarket.bet/` dans Netlify Drop
3. Votre site est en ligne instantanément
4. Configurez votre domaine `wamarket.bet` dans les paramètres

### Option 2 – Vercel
```bash
npm install -g vercel
cd wamarket.bet/
vercel --prod
```

### Option 3 – Hébergement cPanel
- Uploadez tous les fichiers dans `public_html/`
- Activez HTTPS (obligatoire pour le Service Worker)

---

## 📱 Conversion en APK Android

### Méthode 1 – PWABuilder (la plus simple)
1. Déployez d'abord le site sur HTTPS
2. Allez sur https://www.pwabuilder.com
3. Entrez votre URL (ex: https://wamarket.bet)
4. Cliquez "Package for stores" → Android
5. Téléchargez l'APK généré
6. Signez l'APK avec Android Studio ou `jarsigner`

### Méthode 2 – Trusted Web Activity (TWA) avec Bubblewrap
```bash
# Installer Bubblewrap
npm install -g @bubblewrap/cli

# Initialiser le projet TWA
bubblewrap init --manifest https://wamarket.bet/manifest.json

# Construire l'APK
bubblewrap build

# Signer l'APK
bubblewrap sign
```

### Méthode 3 – WebView App avec Android Studio
1. Créez un nouveau projet Android → Empty Activity
2. Dans `MainActivity.java` :
```java
WebView webView = new WebView(this);
webView.loadUrl("https://wamarket.bet");
webView.getSettings().setJavaScriptEnabled(true);
```
3. Compilez → Build APK

---

## 🔑 Système de Codes Premium

### Génération de codes en production
Les codes démo inclus (WA1000–WA8000, TEST01–TEST04) sont pour démonstration.

**En production**, remplacez la logique de validation par :
```javascript
// Appel API backend pour validation sécurisée
async function verifyCode() {
  const response = await fetch('/api/verify-code', {
    method: 'POST',
    body: JSON.stringify({ code, matchId: currentUnlockId })
  });
  const { valid } = await response.json();
  // ...
}
```

### Backend simple (Node.js / Express)
```javascript
const codes = new Map(); // code → matchId (base de données)

app.post('/api/verify-code', (req, res) => {
  const { code, matchId } = req.body;
  if (codes.get(code) === matchId) {
    codes.delete(code); // usage unique
    res.json({ valid: true });
  } else {
    res.json({ valid: false });
  }
});

// Générer un nouveau code
function generateCode(matchId) {
  const code = 'WA' + Math.random().toString(36).slice(2,7).toUpperCase();
  codes.set(code, matchId);
  return code; // → envoyer par WhatsApp
}
```

---

## 💰 Flux de Monétisation WhatsApp

1. Client clique **"Commander"** sur wamarket.bet
2. Redirection automatique vers WhatsApp (+0170292408)
3. Message pré-rempli : *"Bonjour, je veux acheter un pronostic premium wamarket.bet"*
4. Admin reçoit la demande, demande le match souhaité
5. Client paie 1 000 FCFA (Mobile Money / Wave)
6. Admin génère et envoie le code unique
7. Client entre le code → pronostic débloqué instantanément

---

## 🤖 API Anthropic (IA)

L'analyse IA est alimentée par Claude via l'API Anthropic.

- Endpoint : `https://api.anthropic.com/v1/messages`
- Modèle : `claude-sonnet-4-20250514`
- Coût estimé : < 0,01$ par analyse

**Note de sécurité** : En production, effectuez les appels API depuis votre backend pour ne pas exposer votre clé API.

---

## 📊 Personnalisation

### Ajouter un match
Dans `index.html`, ajoutez un objet dans le tableau `MATCHES` :
```javascript
{
  id: 'm9',
  sport: 'football',
  league: 'Premier League 🏴󠁧󠁢󠁥󠁮󠁧󠁿',
  home: 'Manchester United',
  homeIcon: '🔴',
  away: 'Tottenham',
  awayIcon: '⚪',
  time: '17:30',
  date: "Aujourd'hui",
  status: 'upcoming',
  conf: 71,
  pred_faible: '1X2',
  pred_faible_val: 'Victoire ManU',
  pred_moyen: 'Buts',
  pred_moyen_val: '+2.5 buts',
  level: 'premium',
  teams_hint: 'Manchester United vs Tottenham',
  score: null
}
```

### Changer le numéro WhatsApp
Recherchez `0170292408` et remplacez par votre numéro au format international.

---

## 🎨 Couleurs & Branding

```css
--bg: #0f172a      /* Fond principal (bleu nuit) */
--green: #22c55e   /* Vert principal (succès, CTA) */
--gold: #facc15    /* Or (premium, badges) */
--red: #ef4444     /* Rouge (live, perdu) */
```

---

## ✅ Checklist avant lancement

- [ ] Déploiement sur HTTPS (obligatoire PWA)
- [ ] Ajouter les vraies icônes PNG (192x192 et 512x512)
- [ ] Configurer le backend pour la validation des codes
- [ ] Tester le flow WhatsApp (numéro correct)
- [ ] Remplacer les matchs de démo par des données réelles (API sportive)
- [ ] Intégrer une vraie API sportive (SportsDB, API-Football, etc.)
- [ ] Soumettre sur Google Play via PWABuilder / Bubblewrap
