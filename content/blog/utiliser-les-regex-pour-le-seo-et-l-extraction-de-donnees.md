---
title: "Comment utiliser les REGEX pour le SEO et l’extraction de données"
date: 2020-05-29T10:07:47+06:00
draft: false

# post thumb
image: "images/post/regex-seo-growth-hacking-experiments.jpg"

# meta description
description: "Utilise les regex est un bon moyen d'améliorer son SEO, voici un exemple concret avec Screaming frog"

# taxonomies
categories: 
  - "SEO"
tags:
  - "REGEX"
  - "Python"
  - "Screaming Frog"

# post type
type: "post"
---

Le marketing digital dispose d’une pléthore d’outils pour rechercher et extraire des données. L’un des plus puissants d’entre eux mais qui reste trop souvent ignoré est l’utilisation des REGEX.

Consultants SEO, Data analystes, Chercheurs,Community managers et spécialistes du marketing numérique en tout genre négligent parfois l’étendue de la puissance des expressions régulières.

Cet article définit ce que sont les Regex et leurs utilités lorsqu’elles sont combinées avec certains web crawlers, au travers de cinq cas pratiques.

## Les REGEX, qu’est ce que c’est?

Les Regex (expressions régulières) , sont un outil de pattern matching. 

Elles sont un élément incontournable pour les moteurs de recherche, les logiciels de search & replace, et sont une composante native ou complémentaire de nombreux langages de programmation.

Combiner la méthode "find" avec un web crawler est un atout puissant tant pour identifier les erreurs que pour extraire des données.

Cette fonctionnalité est encore relativement nouvelle dans les outils pour SEO sur le marché.

L'extraction personnalisée en utilisant des regex ( Xpath ou CSSpath), n'a été ajoutée à Screaming Frog SEO Spider que dans la version de juillet 2015. 

Jusqu'à cette date, SEOTools pour Excel était peut-être l'outil le plus accessible pour l'extraction par regex - mais seulement lorsqu'il était combiné avec un crawler comme Screaming Frog pour collecter d'abord les URL à analyser.

Google Sheets a aussi depuis longtemps la possibilité d'utiliser l'extraction par Regex mais, comme SEOTools pour Excel, il faut d'abord parcourir le site avec un web crawler.

Après que Screaming Frog ait déployé sa fonction d'extraction personnalisée par Regex en 2015, d’autres outils de référencement de niveau "entreprise" tel que BrightEdge et Conductor ont réagi en ajoutant des fonctions similaires.

Malgré la présence désormais généralisée de l’extraction de données par Regex, la puissance de cette fonctionnalité est encore largement ignorée par de nombreux professionnels du web.

## Comment apprendre les REGEX :

Plongeons dans le vif du sujet avec une combinaison de Screaming Frog et d'outils comme :

-   [RegExr](https://regexr.com/)
    
-   [Regular Expressions 101](https://regex101.com/)
    
-   [Txt2re](https://txt2re.com/)
    
-   [Build RegEx](http://buildregex.com/)
    

Vous pouvez reprendre les cas d'utilisation ci-dessous, ou pratiquer sur vos propres projets.

Cas pratiques avec REGEX :

Ci dessous quelques exemples d’utilisation des Regex dont vous pouvez vous inspirer en ré-utilisant les formules.

### 1. Extraire des métadonnées (ou toute autre donnée) de n'importe quel site web

Regardons le site ESPN.

Prenons pour l’exemple [cette histoire](https://www.espn.com/nba/story/_/id/22258759/zach-lowe-blake-griffin-trade-future-la-clippers-detroit-pistons) sur la star de la NBA Blake Griffin.

En consultant le code source de cette page, nous localisons un type de métadonnées propice à analyser (des métadonnées similaires se trouvent sur la plupart des grands sites web) :

```json
{  
"omniture": {  
"columnist": "lowe_zach",  
"league": "nba",  
"countryRegion": "en-us",  
"hier1": "nba:story",  
"section": "nba",  
"source": "espn.com",  
"pageName": "nba:story",  
"storyInfo": "22258759+zach-lowe-blake-griffin-trade-future-la-clippers-detroit-pistons",  
"sections": "nba:story",  
"site": "espn",  
"premium": "premium-no",  
"convrSport": "basketball",  
"pageURL": "www.espn.com/nba/story/_/id/22258759/zach-lowe-blake-griffin-trade-future-la-clippers-detroit-pistons",  
"lang": "en_us",  
"prop35": "2018-01-30",  
"contentType": "story",  
"sport": "basketball",  
"account": "wdgespcom",  
"siteType": "full",  
"prop58": "isIndex=false"  
}
```
Le but étant d’identifier un modèle de données récurrent et intéressant à analyser. Une fois identifié, nous pouvons commencer à construire la regex pour extraire ces données.

Copions simplement le code ainsi que quelques lignes avant et après dans regexr.com pour commencer à travailler sur quelques formules :

{{< nofollow-image "https://lh6.googleusercontent.com/Vjvqr7FfL_y_z9jzadA6j4wAOCwnVfpU88SZjl4tO7eOD8DiTusD8Q-9NOOAU0pgsaXI6VjtFL67aD_6c2lFhMw70xXVXdZhIC7v2t4kjWnvYj6mIo83Ax_R0gtNELC2jXOsZkX1" "Regex for SEO" >}}

Supposons que j’audit le site d'ESPN et que je veux obtenir une liste de tous leurs articles avec auteurs et dates. Il me suffit de lancer le crawler avec ces 2 expressions régulières en les incluant dans le filtre d'extraction personnalisée de Screaming Frog:

```
"columnist":"(.*?)"
```

```
"prop35":"(.*?)"
```

{{< nofollow-image "https://lh3.googleusercontent.com/EdPkiqNtW5B_NsdNmODsHmeX2zJI209HQfjn3gf-hykUGFEcYtPRla7e5wqDE7ErtopEGpNs-lo3uUhPQIcuVAKTrqfDj5zFOQqUCxRml8CnvX_eJzb7UUZGNDrFv8kTpfIE_iTV" "Crawling ESPN.com" >}}

Et ça marche ! Je peux parcourir une liste d'URL d'ESPN.com, ou parcourir tout le site, et en extraire les informations qui seront essentielles pour mon audit de contenu.

### 2. Trademark Enforcement ( Droit des marques)

Le [symbole d’une marque déposée](https://en.wikipedia.org/wiki/Registered_trademark_symbol) (®) est un symbole qui doit généralement apparaître juste après le nom de la marque déposée, dès sa première utilisation sur une page web.

Prenons par exemple "[ITIL](https://www.bmc.com/blogs/itil-4/)®" - dont le sigle signifie IT Infrastructure Library, une marque déposée appartenant à [Axelos](https://www.axelos.com/).

De nombreux sites web qui parlent d'ITIL omettent d'inclure le symbole de la marque déposée lorsqu'ils font référence à ce concept.

L'un de ces sites, Cherwell.com, parle d'ITIL mais n'inclut pas toujours le symbole de la marque déposée.

En utilisant deux Regex basiques, nous pouvons facilement trouver les URL dans lesquelles Cherwell devrait envisager d'ajouter le symbole de marque déposée :

{{< nofollow-image "https://lh6.googleusercontent.com/vRWjNVFR8b4TCjUUr9X_X0yghv-pWYHkNAIkJ23UGWp6tFXmxOrqwpB60XOI7qD_490sMZ_JBsM794MFuakF27F-lDKPwA0GGVtE1EwsIh5qJeM2asqAPLfvyn27NUlV3HPPdCu8" "Crawling for Trademark" >}}

Dans ce cas, nous avons repéré quatre URL qui comportent le bon symbole - et beaucoup d'autres qui ne le comportent pas.

Cet exemple montre à quel point une simple chaîne de regex peut être puissante.

Ci dessous les Regex utilisées :
```
ITIL®
```
```
ITIL(?!®)
```

### 3. Changer le nom des produits ou rechercher une morphologie de caractères incorrecte

Par exemple en 2017, IBM a officiellement changé le nom de "DB2" en "Db2" 

Le caractère "b" est devenu minuscule.

Le nom DB2 a été utilisé pour la première fois en 1983, il y a donc probablement d'innombrables endroits sur le web qui gardent le nom incorrect de DB2.

Ne pas utiliser les regex et s'adapter à une morphologie changeante, c'est subir des erreurs fréquentes ou de ne pas trouver un mot ou ou le nom d'un produit qui change.

En utilisant l'expression régulière suivante, nous sommes capables d'identifier toutes les instances incorrectes de "DB2", "db2" ou "dB2" :

La Regex :
```
(db|DB|dB)2
```

{{< nofollow-image "https://lh3.googleusercontent.com/njeqc7_0oV-FlQP4qTAIbbXTG3DWZTohjA963zsOi9fPY4R5z9FL-BpF907cfCKDOcMO1y1NyQpqjtf-4eLH6sO1bWd2r-nx_trnNx8qNXFY8cW-zxwJKbWdVJGGyCQF5xzzpOnp" "Crawling for Capitalization and Casing" >}}

### 4. Trouver des fichiers téléchargeable sur les sites communautaires

Jive Software est peut-être la plate-forme communautaire N°1 pour les entreprises. Elle est utilisée par des sociétés comme Cisco, ServiceNow, Adobe, BMC Software, McAfe, Wiley et bien d'autres grandes entreprises.

Vous pouvez trouver d'autres exemples d'entreprises utilisant la plateforme Jive avec une requête Google, tel que :


[https://www.google.com/search?q=inurl:hosted.jivesoftware.com](https://www.google.com/search?q=inurl:hosted.jivesoftware.com)

Jive, tout comme WordPress ou d'autres plateformes de publication, présente certains patterns propre à ce genre de structure.

Par exemple tous les téléchargements vers la plate-forme, lorsqu'ils sont visualisés à partir du document ou du blog sur lequel ils sont téléchargés, comprennent le lien suivant :

```html
<a class="j-attachment-icon"
href="/servlet/JiveServlet/download/xxx/filename.ext">
```

Par conséquent, nous pouvons utiliser la Regex suivante pour extraire toutes les URL de pièces jointes de n'importe quel site communautaire hébergé sur Jive :

```
"j-attachment-icon" href="(.*?)"
```

Pour voir comment ça fonctionne, voici les résultats d'un bref scan du site  [https://community.servicenow.com](https://community.servicenow.com) :

{{< nofollow-image "https://lh6.googleusercontent.com/_4JY8eZugQAFYsUyCs7zQjySb6PXjE_dRhdfiP5_RIgcqRctlC40JQA0frMS9I_tvt4Qb_IN__zU8e8_dh4akDyJ6XxbaPROWLJ1DHDy0OCpVWK_0jorqTroiuv0J6EyuFZK7nyB" "Crawling for download links" >}}

### 5.  Contrôler l’orthographe

Fort heureusement, la plupart des éditeurs vérifient l'orthographe dans Word & autres avant de publier leurs contenu, mais les fautes d'orthographe les plus courantes passent encore inaperçues.

Pas d’inquiétudes :

Regex + Screaming Frog peut vous aider à identifier les mots couramment mal orthographiés. 

Le seul inconvénient est que vous devez connaître/produire la liste de ces mots.

Certains mots sont susceptible d’êtres mal orthographié dans n’importe quelle langue.

Il existe également des mots spécifiques à un secteur ou à une marque qui doivent être inclus dans la liste des fautes d'orthographe.

Voici une simple Regex pour faire matcher les mots anglais les plus couramment mal orthographiés aux États-Unis :

```
(?i)accomodate | accomodation | acheive | accross | agressive | agression | apparantly | appearence | arguement | assasination | basicly | begining | beleive | belive | bizzare | buisness | calender | Carribean | cemetary | chauffer | collegue | comming | commitee | completly | concious | curiousity | definately | dilemna | dissapear | dissapoint | ecstacy | embarass | enviroment | existance | Farenheit | familar | finaly | florescent | foriegn | forseeable | fourty | foward | freind | futher | jist | glamourous | goverment | gaurd | happend | harrass | harrassment | honourary | humourous | idiosyncracy | immediatly | incidently | independant | interupt | irresistable | knowlege | liase | liason | lollypop | millenium | millenia | Neandertal | neccessary | noticable | ocassion | occassion | occured | occuring | occurance | occurence | pavillion | persistant | pharoah | peice | politican | Portugese | posession | prefered | prefering | propoganda | publically | realy | recieve | refered | refering | religous | rember | remeber | resistence | sence | seperate | seige | succesful | supercede | suprise | tatoo | tendancy | therefor | threshhold | tommorow | tommorrow | tounge | truely | unforseen | unfortunatly | untill | wierd | whereever | wich
```

Utilisons Screaming Frog à nouveau et voyons si nous pouvons localiser une de ces erreurs sur CNN :

{{< nofollow-image "https://lh4.googleusercontent.com/W3svr7Murr0rVIRgt-ZBiXrf_sE57rtqrY9hvEHpfFWGFam9nIFQubifW1PhmgXftnjujyGGxFhlfSUfKS_xXUH8IRDJLBX_sBSpyTY7HtCT0yANqbMVPQDVSMNbBzyGGeXqYrf_" "Spell Check with Regex" >}}



En un rien de temps, nous avons déjà repéré une mauvaise orthographe du mot "Fahrenheit"

{{< nofollow-image "https://lh4.googleusercontent.com/OcXRbzYtyA2Jwjmmi2HwUmQDccaW3WjC27pfSI1tRoA5n1r5OBAs3hKn7KQ2N7santOVIIUgS7E4uOUd0KNky92TowAnTLBP2PPIydwj0tApH04_CamSb9YSBj6i73mSYOyX23tw" "Crawling CNN.com" >}}

## En résumé

On peut trouver des schémas de données partout sur le web. En utilisant les Regex, vous pouvez les localiser et en extraire les données associés.

Les REGEX ne sont un outil simple à appréhender , mais une fois maîtrisée, vous allez trouver des utilisations infinies pour cette technologie omniprésente sur le web. C'est dans cet esprit que Jamie Zawinski a déclaré :

“Certaines personnes, lorsqu'elles sont confrontées à un problème, pensent "Je sais, je vais utiliser des expressions régulières". Maintenant, ils ont deux problèmes.”