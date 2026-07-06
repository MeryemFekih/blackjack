# Rapport de bug — 
# Page /user/profile (côté front)

## Erreur : 
TypeError: Cannot read properties of null (reading 'username')

## Problème: 

Une variable const user = null était déclarée mais jamais réassignée. Le bloc {#await getUser() then} ne capturait pas la valeur résolue ({:then} au lieu de {:then user}), donc le template lisait toujours cette constante null. De plus, getUser() faisait le fetch mais ne parsait/retournait jamais le JSON reçu.

## Correction


getUser() retourne désormais await res.json().
{:then user} capture la valeur résolue de la promesse.
Ajout d'une garde {#if user} pour gérer le cas null (token absent/expiré).
bind:value → value sur les inputs disabled (Svelte interdit le binding sur une variable issue d'un {#await}).
Ajout de {:catch error} pour éviter un crash silencieux en cas d'échec réseau.


## Résultat

La page charge correctement les données (username, email, wallet) et gère proprement les erreurs.








# Rapport de bug — Redirection après inscription (404 sur `/login`) (côté front)

**Erreur :** `404 Not Found` sur `GET /login` après inscription réussie (backend renvoie bien `201 POST /user`).

## Problème
Le composant `signup/+page.svelte` redirigeait vers `goto('/login')` et contenait un lien `<a href="/login">`. Or, dans l'arborescence SvelteKit, la page de connexion se trouve dans un groupe de route `(login)` — les parenthèses n'ajoutent **aucun segment d'URL**. La route `/login` n'existe donc pas réellement ; la page de login correspond en fait à `/`.

## Correction
- `goto('/login')` → `goto('/')`
- `<a href="/login">` → `<a href="/">`

## Résultat
Après inscription, l'utilisateur est correctement redirigé vers la page de connexion existante.

## À vérifier
Rechercher d'éventuelles autres références à `/login` dans le projet pour éviter le même problème ailleurs :
```powershell
Select-String -Path "src\routes\**\*.svelte" -Pattern "/login"
```