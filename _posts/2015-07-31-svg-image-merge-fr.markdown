---
layout: post
title:  "Le SVG pour optimiser des images non-vectorielles"
date:   2015-07-31 12:17:00
categories: image-optim
excerpt: French - Une technique pour les tarés de l'optimisation
comments: true
---

*Une technique pour les tarés de l'optimisation.*


Mettons-nous en situation. Ce matin, tu dois ajouter à un site web une image qui contient à la fois une photographie et du texte. Facile à faire avec ton éditeur d'image préféré.

Et voilà !

![Just created image](/assets/optimized-90.jpg)

Tu la sauvegardes en JPEG et regarde la taille sur ton disque : **511Ko!**

Il est temps de l'optimiser. Et là commencent les problèmes... Si tu optimises l'image en JPEG en dessous de la qualité 80%, tu parviens à avoir un poids léger, mais la qualité du texte en souffre. Des approximations et des artefacts sont ajoutés par l'algorithme de compression. C'est dommage, parce que ta photographie d'arrière-plan, elle, est toujours bien même avec une compression à 65%...

![JPEG encoding artefacts](/assets/not-so-sharp.png)

Tu essayes en PNG. La qualité est parfaite, mais l'image est encore plus grosse que la JPEG originale ! Alors tu cherches le meilleur compromis entre poids et qualité et tu finis par choisir une qualité de 90%.

--

| JPEG 100%:                        | 511Ko    |
| PNG:                              | 587Ko    |
| **JPEG 90%:**                     | **84Ko** |

--

Attends... j'ai une petite idée...


### Garder l'arrière-plan et le texte dans deux images différentes

Enregistre l'arrière plan en JPEG et optimise-le autant que tu veux.

![optimized background](/assets/background-65.jpg)

Enregistre le texte en PNG et optimise-le avec un outil d'optimisation sans perte ([ImageOptim][ImageOptim] sur Mac ou [Kraken.io][Kraken.io] online).

![optimized text](/assets/text-alone.png)


### ... et fais rentrer les deux images dans un SVG

Tu vas me dire que tu pourrais insérer ces deux images dans l'HTML et positionner l'une par dessus l'autre en CSS. Oui, c'est l'idéal ! Encore mieux, écris le texte en HTML.

**Mais ça ne conviendra pas si tu as VRAIMENT besoin que ce soit une et une seule image**, par exemple pour ton Wordpress, pour un carousel... Tu peux aussi avoir envie de garder une seule balise image dans l'HTML pour des raisons de sémantique.

Une fonctionnalité méconnue en SVG permet d'insérer une image non-SVG encodée en base64 dans un SVG. Ouvre ton éditeur de texte et crée un SVG ainsi :

```xml
<svg width="800" height="600">
    <image x="0" y="0" width="800" height="600" xlink:href="data:image/jpg;base64,{{base64-encoded-background}}" />
    <image x="0" y="0" width="800" height="600" xlink:href="data:image/png;base64,{{base64-encoded-top}}" />
</svg>
```

Sauvegarde, puis compare avec tes tests en JPEG. Car le résultat n'est pas toujours meilleur que JPEG. Il dépend de la capacité d'optimisation des deux images prises séparément.

Mais attends! **Compare les poids des images en compressant ton svg** (gzip ou zip). Car à l'inverse de la plupart des autres formats, le SVG est un format non compressé.

D'ailleurs, vérifie que ton serveur envoie bien les fichiers SVG gzippés. C'est une erreur très fréquente de configuration.

![check gzip compression](/assets/gzip.png)


### Resultat

![result svg file](/assets/result.svg)

--

| Arrière-plan JPEG 65% seul:       | 32Ko     |
| Texte PNG seul:                   | 17Ko     |
| Resultat SVG:                     | 64Ko     |
| **Resultat SVG gzippé:**          | **47Ko** |

--

Comme tu peux le voir, l'encodage en base64 n'est pas un problme : le fichier SVG gzippé est même plus petit que la somme des deux fichiers JPEG et PNG.

*Le texte pourrait même être directement écrit en SVG. Mais bien sûr, dans ce cas, ne l'encode pas en base64.*


### Compatibilité navigateur

**Attention !** Safari ne gère pas les images en base64 encodés dans des SVG, si le SVG est chargé en `<img>`. Ça fonctionne en `<object>`. Si quelqu'un peut tester avec Safari 9, ça m'intéresse.

Quant à IE8, il n'est pas capable de lire le SVG du tout.


### Un petit outil pour simplifier cela

J'ai créé [cet outil][svg-image-merge] en ligne de commande, qui fera pour toi la partie technique : encodage en base64 des images et injection dans le SVG.




[svg-image-merge]:      https://github.com/gmetais/svg-image-merge
[ImageOptim]:           https://imageoptim.com/fr.html
[Kraken.io]:            https://kraken.io/
