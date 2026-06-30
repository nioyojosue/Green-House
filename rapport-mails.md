# Rapport : Gestion des mails dans le projet

## 1) Où les “mails” sont gérés
Dans ce projet, l’envoi de mail est implémenté via un formulaire de contact sur la page **`contact.html`**, qui envoie les données à un endpoint PHP **`mail/contact.php`**.

- Frontend (formulaire) : `contact.html`
- JavaScript AJAX : `mail/contact.js`
- Backend mail : `mail/contact.php`

## 2) Flux complet (front -> backend -> envoi mail)

### 2.1 Page `contact.html`
Le formulaire est défini comme :
- `form#contactForm`
- champs :
  - `input#name` (name)
  - `input#email` (email)
  - `input#subject` (subject)
  - `textarea#message` (message)
- Le bouton : `button#sendMessageButton`

En bas de la page, le template charge :
- `mail/jqBootstrapValidation.min.js`
- `mail/contact.js`

### 2.2 Script `mail/contact.js`
Le script intercepte le submit du formulaire (via `jqBootstrapValidation`) et déclenche un **AJAX POST** :

- `url: "contact.php"`
- `type: "POST"`
- `data:`
  - `name`
  - `email`
  - `subject`
  - `message`

Gestion UI :
- Pendant l’envoi : désactive le bouton via `prop("disabled", true)`.
- En cas de succès : affiche un message dans `#success`, puis reset du formulaire.
- En cas d’erreur : affiche un message d’erreur générique.

> Remarque : le endpoint est relatif à la page courante. Depuis `contact.html`, `contact.php` correspond bien à `mail/contact.php` (car le script est dans `mail/` et est chargé depuis `mail/contact.js`).

### 2.3 Endpoint PHP `mail/contact.php`
Le script PHP reçoit `$_POST` et envoie un email via la fonction PHP native `mail()`.

#### Validation d’entrée
Le code vérifie :
- `name` non vide
- `subject` non vide
- `message` non vide
- `email` valide (`filter_var(..., FILTER_VALIDATE_EMAIL)`)

Si invalides :
- `http_response_code(500);`
- `exit();`

#### Nettoyage
- Tous les champs sont passés par :
  - `htmlspecialchars`
  - `strip_tags`

#### Destinataire & contenu
- Destinataire fixe : `"mirabeaumbang98@gmail.com"`
- Subject : `"$m_subject:  $name"`
- Body : texte multi-lignes contenant :
  - nom
  - email
  - sujet
  - message

#### Headers
- `From: $email`
- `Reply-To: $email`

#### Envoi
- `if(!mail($to, $subject, $body, $header))` alors `500`.
- Sinon : aucun code de succès explicite (le script finit après `?>`).

## 3) Points importants / limites actuelles

### 3.1 Dépendance à `mail()` (serveur SMTP)
Le projet dépend entièrement de la configuration du serveur pour que `mail()` fonctionne.
- Sur un serveur non configuré (ou en mode hébergement sans support mail), l’envoi échouera.

### 3.2 Destinataire “hardcodé”
L’adresse destinataire est codée en dur :
- `mirabeaumbang98@gmail.com`

Pour passer en multi-sites/environnements (dev/prod), il faudrait externaliser ça (variables d’environnement, fichier config, etc.).

### 3.3 Pas de protection anti-spam / rate limiting
Aucune protection n’est visible :
- pas de captcha
- pas de rate limiting
- pas de token CSRF

### 3.4 Headers minimalistes
Les headers sont minimalistes (From / Reply-To).
- pas de champ `Content-Type` (ex. `text/plain; charset=utf-8`).
- pas de `X-Mailer`.

## 4) Comment modifier/implémenter correctement (guide rapide)

### Objectif : changer le destinataire
Modifier `mail/contact.php` :
- changer la variable `$to`.

### Objectif : améliorer la robustesse
Changements typiques à implémenter :
1. Ajouter un `Content-Type` UTF-8 dans `$header`.
2. Gérer explicitement un JSON réponse succès/échec (et le consommer côté JS).
3. Ajouter anti-spam : reCAPTCHA / hCaptcha ou au minimum rate limiting.
4. Externaliser les paramètres sensibles (destinataire, éventuellement API keys si vous migrez vers un service SMTP/API).

---

## Résumé en 3 lignes
- `contact.html` envoie les données du formulaire.
- `mail/contact.js` fait un POST AJAX vers `contact.php`.
- `mail/contact.php` valide, nettoie, puis envoie via `mail()` vers `mirabeaumbang98@gmail.com`.

