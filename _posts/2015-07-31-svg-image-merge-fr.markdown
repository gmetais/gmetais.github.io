---
layout: post
title:  "[French] Le SVG pour optimiser des images non-vectorielles"
date:   2015-07-31 12:17:00
categories: image-optim
excerpt: Une technique pour les tarés de l'optimisation
comments: true
---

*Une technique pour les tarés de l'optimisation.*


Mettons-nous en situation. Ce matin, vous devez ajouter à un site web une image qui contient à la fois une photographie et du texte. Facile à faire avec votre éditeur d'image préféré.

Et voilà !

![Just created image](/assets/optimized-90.jpg)

Vous la sauvegardez en JPEG et regardez la taille sur votre disque : **511Ko!**

Il est temps de l'optimiser. Et là commencent les problèmes... Si vous optimisez l'image en JPEG en dessous de la qualité 80%, vous parvenez à avoir un poids léger, mais la qualité du texte en souffre. Des approximations et des artefacts sont ajoutés par l'algorithme de compression. C'est dommage, parce que votre photographie d'arrière-plan, elle, est toujours bien même avec une compression à 65%...

![JPEG encoding artefacts](/assets/not-so-sharp.png)

Alors vous essayez en PNG. La qualité est parfaite, mais l'image est encore plus grosse que la JPEG originale ! Alors vous allez chercher le meilleur compromis entre poids et qualité et finir par choisir une qualité de 90%.

--

| JPEG 100%:                        | 511Ko    |
| PNG:                              | 587Ko    |
| **JPEG 90%:**                     | **84Ko** |

--

Attendez... j'ai une petite idée...


### Garder l'arrière-plan et le texte dans deux images différentes

Enregistrez l'arrière plan en JPEG et optimisez-le autant que vous voulez.

![optimized background](/assets/background-65.jpg)

Enregistrez le texte en PNG et optimisez-le avec un outil d'optimisation sans perte ([ImageOptim][ImageOptim] sur Mac ou [Kraken.io][Kraken.io] online).

![optimized text](/assets/text-alone.png)


### ... et faites rentrer les deux images dans un SVG

Vous allez me dire que pourriez insérer ces deux images dans l'HTML et positionner l'une au dessus de l'autre en CSS. Oui, c'est l'idéal ! Encore mieux, écrivez le texte en HTML.

**Mais ça ne conviendra pas si vous avez VRAIMENT besoin que ce soit une et une seule image**, par exemple pour votre Wordpress, pour un carousel...

Une fonctionnalité méconnue en SVG vous permet d'insérer une image non-SVG encodée en base64 dans un SVG. Ouvrez votre éditeur de texte et créez un SVG ainsi:

```xml
<svg width="800" height="600">
    <image x="0" y="0" width="800" height="600" xlink:href="data:image/jpg;base64,{{base64-encoded-background}}" />
    <image x="0" y="0" width="800" height="600" xlink:href="data:image/png;base64,{{base64-encoded-top}}" />
</svg>
```

Sauvegardez, puis comparez avec vos tests en JPEG. Car le résultat n'est pas toujours meilleur que JPEG. Il dépend de la capacité d'optimisation des deux images prises séparément.

Mais attendez! Comparez les poids des images en compressant (gzip ou zip) votre SVG. Car à l'inverse de la plupart des autres formats, le SVG est un format non compressé.

D'ailleurs, vérifiez que votre serveur envoie bien les fichiers SVG gzippés. C'est une erreur très fréquente de configuration.

![check gzip compression](/assets/gzip.png)


### Resultat

![result svg file](/assets/result.svg)

--

| Arrière-plan JPEG 65% seul:       | 32Ko     |
| Texte PNG seul:                   | 17Ko     |
| Resultat SVG:                     | 64Ko     |
| **Resultat SVG gzippé:**          | **47Ko** |

--

*Le texte pourrait même être directement écrit en SVG. Mais bien sûr, dans ce cas, ne l'encodez pas en base64.*


### Compatibilité navigateur

Désolé mon vieux IE8, tu n'es pas capable de comprendre le SVG...


### Un petit outil pour simplifier cela

J'ai créé [cet outil][svg-image-merge] en ligne de commande, qui fera pour vous la partie technique : encodage en base64 des images et injection dans le SVG.



[svg-image-merge]:      https://github.com/gmetais/svg-image-merge
[ImageOptim]:           https://imageoptim.com/fr.html
[Kraken.io]:            https://kraken.io/
