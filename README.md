# reviser-partiel-tp

Un skill [Claude Code](https://code.claude.com/docs/en/skills) pour réviser les TP d'un partiel d'IUT, TP par TP, à partir de tes propres documents.

## Ce qu'il fait

1. **Te demande** quels TP sont au partiel et si l'épreuve dure bien 1h15.
2. **Trie tes documents** : chaque TP avec son TD, son corrigé, son compte-rendu et ses vidéos (un sujet par TP au partiel).
3. Pour chaque TP, dans l'ordre :
   - **Correction détaillée cachée** (dans `.correction/`), qui sert à Claude pour te guider.
   - **Fiche de révision** en HTML : chaque symbole avec son nom et son unité, et toutes les formules du TP sous leur **forme de base** uniquement (`P = U·I`, jamais `I = P/U`).
   - **On refait le TP question par question** : Claude dit seulement `Go question X`. Si tu bloques, tu as un indice léger, puis un deuxième. La réponse ne vient que si tu la demandes. Chaque correction suit le même format :
     ```
     f = Np*N => N = f/Np
              => N = 20/200
              => N = 0.1 tr/sec
     ```
     🟥 **N = 0.1 tr/sec**
4. **Partiel type** d'1h15 pour chaque TP, avec barème sur 20, puis correction de ta copie.

La progression est enregistrée dans `progression.md` : tu peux arrêter et reprendre dans une autre session.

## Installation

```bash
git clone https://github.com/AMATEPaul/reviser-partiel-tp.git ~/.claude/skills/reviser-partiel-tp
```

Puis, dans Claude Code, tape `/reload-skills` (ou redémarre la session).

## Utilisation

Mets tes documents dans un dossier (rangés ou en vrac) et ouvre Claude Code dedans :

```
mon-partiel/
  TP1-moteur-pas-a-pas/
    TP_vierge.pdf   TP_corrige.pdf   TD.pdf   CR.pdf   videos/
  TP2-.../
```

Lance `/reviser-partiel-tp`.

Sources utilisées, **et uniquement celles-là** : fascicules de TP et de TD (vierges et corrigés si tu les as), comptes-rendus (les tiens ou ceux d'un collègue de confiance), vidéos de TP. En cas de désaccord, le corrigé du prof l'emporte et l'écart t'est signalé.

> ⚠️ N'ouvre pas le dossier `.correction/` : c'est la correction que Claude garde pour te guider.

## Prérequis

- **Vidéos (optionnel)** : [`ffmpeg`](https://ffmpeg.org/) pour en extraire des images. Si [`whisper`](https://github.com/openai/whisper) est installé, le son est aussi transcrit.

## Contenu

| Fichier | Rôle |
|---|---|
| `SKILL.md` | La méthode que suit Claude |
| `modeles/fiche-tp.html` | Modèle des fiches de révision |
| `modeles/partiel.html` | Modèle des partiels types |
