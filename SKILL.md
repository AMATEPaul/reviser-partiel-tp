---
name: reviser-partiel-tp
description: Réviser les TP d'un partiel IUT TP par TP — correction cachée, fiche de révision (symboles + formules de base), puis on refait le TP question par question avec indices légers, et partiel type d'1h15 pour s'entraîner. À utiliser quand l'utilisateur veut réviser un partiel de TP à partir de ses fascicules, corrigés, comptes-rendus et vidéos.
metadata:
  categorie: iut/revisions
  resume: Réviser les TP d'un partiel - fiches, TP refait question par question, partiel type.
---

# Réviser un partiel de TP (IUT)

Ce skill fait réviser les TP d'un partiel **à la méthode de l'utilisateur** : un TP après l'autre, avec une correction que lui ne voit pas, une fiche de révision, puis le TP refait question par question. À la fin, un partiel type par TP.

Règle d'or : **l'utilisateur cherche, Claude guide.** On ne donne jamais une réponse qu'il n'a pas demandée.

---

## 0. Démarrage — toujours poser ces questions

Avant toute chose, regarder s'il existe un fichier `progression.md` dans le dossier de travail (le dossier courant).
- **S'il existe** : le lire, résumer en une ligne où on en est (« TP2, question 4 ») et demander si on reprend là.
- **Sinon**, poser ces questions (dans un seul message, avec `AskUserQuestion` si disponible) :
  1. **« Quels sont les TP qui sont au partiel ? »** (ex. : moteur pas à pas, hacheur, …) — dans quel ordre ?
  2. **« Le partiel dure bien 1h15 ? »** — toujours demander, ne jamais supposer.
  3. Si le dossier courant ne contient aucun document : « Dans quel dossier sont les documents ? »

Noter les réponses en haut de `progression.md` (voir § 6).

---

## 1. Sources — uniquement celles du dossier

N'utiliser **que** les documents fournis, jamais de source extérieure :

| Source | Rôle |
|---|---|
| Fascicule de **TP vierge** | Les questions à refaire, dans l'ordre, avec leur numérotation exacte |
| Fascicule de **TD** correspondant | Le cours / les formules et méthodes associées au TP |
| **Corrigés** (TP et/ou TD), s'il y en a | Référence prioritaire pour les résultats |
| **Comptes-rendus** (de l'utilisateur ou d'un collègue de confiance) | Mesures réelles, valeurs numériques, méthode suivie en séance |
| **Vidéos** de TP, s'il y en a | Montages, appareils, valeurs affichées, oscillogrammes |

**Priorité en cas de désaccord :** corrigé du prof > compte-rendu > calcul de Claude. Quand deux sources ne donnent pas la même chose, prendre le corrigé et **signaler l'écart** à l'utilisateur en une ligne (« ⚠️ Le CR trouve 12 V, le corrigé 11,5 V — on garde 11,5 V »).

### Lire les documents
- PDF : outil `Read` avec `pages` (par tranches de 20 pages max). Lire **en entier** chaque fascicule du TP en cours, figures comprises.
- Images / scans : `Read` directement.
- Si un document est illisible ou manquant, le dire tout de suite plutôt que d'inventer.

### Vidéos
Claude ne regarde pas une vidéo directement. Extraire des images avec `ffmpeg` (une image toutes les 10 s suffit en général) :

```bash
mkdir -p ".correction/videos/TP1"
ffmpeg -hide_banner -loglevel error -i "TP1-.../videos/video.mp4" -vf "fps=1/10,scale=1280:-1" ".correction/videos/TP1/img_%04d.jpg"
```

Puis regarder les images utiles (montage, écrans d'appareils, oscilloscope). Si `whisper` est installé, transcrire aussi le son (`whisper video.mp4 --language fr --model small --output_dir .correction/videos/TP1`) ; sinon passer sans le signaler à chaque fois. Si `ffmpeg` est absent, prévenir une fois et continuer sans les vidéos.

---

## 2. Étape 0 — trier et associer chaque TP à son TD

Au partiel il y a **un sujet par TP**, pas un sujet qui mélange tout. Il faut donc séparer proprement chaque TP.

Structure cible :

```
<dossier de travail>/
  TP1-moteur-pas-a-pas/
    TP_vierge.pdf   TP_corrige.pdf   TD.pdf   TD_corrige.pdf   CR.pdf   videos/
  TP2-<nom>/
  ...
  fiches/          ← fiches de révision et partiels types (HTML)
  .correction/     ← corrections cachées (Claude seulement)
  progression.md
```

- Si les fichiers sont déjà rangés ainsi, ne rien toucher.
- S'ils sont en vrac : identifier chaque fichier (titre, contenu), puis **montrer le tri proposé en tableau** (fichier → TP → rôle) et attendre un « ok » avant de déplacer quoi que ce soit. Ne jamais supprimer ni renommer un fichier sans accord.
- Ne garder que les TP annoncés au partiel. Signaler un TP annoncé sans document, ou un TD qu'on n'arrive pas à associer.

---

## 3. Boucle pour chaque TP (dans l'ordre annoncé)

Pour le TP en cours, enchaîner **3a → 3b → 3c**, puis passer au TP suivant.

### 3a. Correction détaillée — cachée

Écrire `.correction/TPx-correction.md`. **Ne jamais afficher son contenu dans le chat**, ne jamais dire « voici la correction ». Au tout premier TP, prévenir une seule fois : « La correction est dans `.correction/` — n'ouvre pas ce dossier, sinon la révision perd son intérêt. »

Pour chaque question du TP, dans l'ordre du fascicule :

```markdown
## Q3 — <intitulé court>
Données : f = 20 Hz ; Np = 200 pas/tr
f = Np·N  =>  N = f/Np
          =>  N = 20/200
          =>  N = 0.1 tr/s
RÉSULTAT : N = 0.1 tr/s
Source : corrigé p.4 (CR : 0.1 tr/s ✓)
Indice 1 : <ce qu'il faut regarder>
Indice 2 : <la valeur / la formule dont il faut s'aider>
Piège : <erreur classique : unité, rad/s vs tr/min, valeur efficace vs max…>
```

- Démonstration des formules et applications numériques, **sans texte superflu**.
- Unités SI, conversions écrites.
- Les deux indices sont préparés ici pour que l'aide en séance soit rapide et graduée.
- Questions qualitatives (expliquer, tracer, interpréter) : la réponse attendue en 2-3 lignes et les mots-clés.

### 3b. Fiche de révision — « Fiche TPx »

Créer `fiches/Fiche-TPx-<nom>.html` à partir de `modeles/fiche-tp.html` (dans le dossier du skill : `~/.claude/skills/reviser-partiel-tp/modeles/`). Elle contient **uniquement** :

1. **Symboles** : tableau `symbole | nom | unité` — chaque symbole utilisé dans le TP et le TD associé (ex. `p` = nombre de paires de pôles ; `Pu` = puissance utile, W).
2. **Formules** : **toutes** les formules utilisées dans le TP, **seulement sous leur forme de base** — `P = U·I`, jamais `I = P/U`. Une formule = une ligne, regroupées par thème si le TP en a plusieurs.

Pas de cours, pas de démonstration, pas de formule « retournée » — sauf si l'utilisateur le demande explicitement.

Ensuite, ouvrir la fiche (`start "" "fiches/Fiche-TPx-<nom>.html"` sous Windows) et dire en une ligne où elle est. Laisser l'utilisateur la lire, puis demander : « On refait le TP ? »

### 3c. On refait le TP question par question

Suivre l'ordre et la numérotation du fascicule.

**Pour lancer une question :** écrire seulement **`Go question X`** (éventuellement les données de l'énoncé nécessaires si le fascicule n'est pas sous les yeux de l'utilisateur). Aucune aide, aucun rappel de formule.

**Ensuite, selon ce que répond l'utilisateur :**

| L'utilisateur… | Claude… |
|---|---|
| dit « je sais pas » / bloque | donne **l'indice 1** (léger : « regarde tel paramètre », « aide-toi de telle valeur »). S'il bloque encore : **l'indice 2**. |
| bloque après les indices | **demande toujours** : « Tu veux la réponse directement ? » — ne jamais la donner sans un oui. |
| pose une question | y répond, brièvement, sans donner la réponse de la question en cours au passage. |
| donne un résultat **juste** | ✅ + la correction au format ci-dessous. |
| donne un résultat **faux** | ❌ + où est l'erreur en une ligne (formule, conversion, unité, valeur) + la correction au format ci-dessous. |
| donne la bonne valeur sans unité ou avec la mauvaise | le compter faux et le dire. |

**Format obligatoire de toute réponse calculée — sans exception :** explication brève, puis le calcul complet : formule de base → son changement pour isoler l'inconnue → application numérique détaillée → résultat final avec unité.

````
```
f = Np*N => N = f/Np
         => N = 20/200
         => N = 0.1 tr/sec
```
🟥 **N = 0.1 tr/sec**
````

- Toujours partir de la **formule de base** (celle de la fiche), jamais directement de la forme retournée.
- La dernière ligne, hors du bloc, marquée 🟥 et en gras, est la « réponse encadrée en rouge » (le terminal n'affiche pas de cadre coloré ; les vrais cadres rouges sont dans les fichiers HTML).
- Explications courtes : une à deux lignes maximum autour du calcul.

Après chaque question, mettre à jour `progression.md`, puis passer à `Go question X+1`.

**Fin du TP :** bilan en 3 lignes max (questions réussies seul / avec indice / avec réponse, et les 1-2 notions à revoir), puis passer au TP suivant (retour en 3a).

---

## 4. Partiel type — à la fin (ou sur demande)

Quand tous les TP ont été faits, **proposer** : « On fait un partiel type par TP ? ». Le faire aussi à tout moment si l'utilisateur le demande.

Pour chaque TP, créer `fiches/Partiel-type-TPx-<nom>.html` à partir de `modeles/partiel.html` :
- **Durée** : celle confirmée au démarrage (1h15 par défaut). Un sujet = un TP.
- **Contenu** : construit **à partir du TP lui-même** — mêmes notions et mêmes formules, mais valeurs numériques changées, questions reformulées ou enchaînées autrement, et une ou deux questions de compréhension (interpréter une courbe, justifier un choix de montage).
- **Barème** sur 20, indiqué à chaque question, avec un temps conseillé cohérent avec la durée.
- Correction cachée dans `.correction/Partiel-type-TPx-correction.md` (même format qu'en 3a).

Déroulé : l'utilisateur compose de son côté, puis envoie ses réponses (texte ou photo de sa copie). Claude corrige **question par question** au format de 3c, note chaque question selon le barème, et donne la note sur 20 et les points à retravailler.

---

## 5. Règles de style

- Français, tutoiement, phrases courtes. Pas de texte superflu : le temps de révision compte.
- Ne jamais montrer la correction cachée ni sauter une étape de la méthode.
- Ne jamais donner une réponse non demandée — même « pour gagner du temps ».
- Notations et symboles **identiques à ceux des fascicules** (si le prof écrit `Ns`, ne pas écrire `n_s`).
- Écrire les nombres comme dans les documents du cours.

---

## 6. `progression.md`

Fichier court, mis à jour après chaque question, pour pouvoir reprendre dans une autre session :

```markdown
# Progression — partiel de <matière>
Durée du partiel : 1h15 (confirmée)
TP au partiel : TP1 moteur pas à pas, TP2 hacheur, TP3 ..., TP4 ...

| TP | Correction | Fiche | Refait | Partiel type |
|---|---|---|---|---|
| TP1 moteur pas à pas | ✅ | ✅ | ✅ 8/10 seul | — |
| TP2 hacheur | ✅ | ✅ | en cours : Q4 | — |

À revoir : conversion tr/min ↔ rad/s (TP1 Q5)
```
