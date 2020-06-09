---
title: "Envoyer un formulaire html directement à Google Sheets"
date: 2020-06-09T19:01:52+02:00
draft: false

# post thumb
image: "images/post/envoyer-un-formulaire-html-a-google-sheet.jpg"

# meta description
description: "Gagnez du temps en automatisant la recolte d'adresses emails"

# taxonomies
categories: 
  - "Growth Hacking"
tags:
  - "Google Apps"
  - "Javascript"

# post type
type: "post"
---

#### Comment créer un formulaire HTML qui stocke les données du formulaire soumis dans Google Sheets en utilisant le JavaScript (ES6), [Google Apps Script](https://developers.google.com/apps-script/), [Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API) and [FormData](https://developer.mozilla.org/en-US/docs/Web/API/FormData).

## 1. Créer une nouvelle fiche Google

- D'abord, allez sur [Google Sheets](https://docs.google.com/spreadsheets) et `lancez une nouvelle feuille de calcul` avec le modèle `vide`.
- Renommez-le `Email Subscribers`. Ou peu importe, ça n'a pas d'importance.
- Mettez les en-têtes suivants dans la première ligne :

|   |     A     |   B   | C | ... |
|---|:---------:|:-----:|:-:|:---:|
| 1 | timestamp | email |   |     |

> Pour apprendre comment ajouter des champs de saisie supplémentaires, [section 7 ci-dessous](#7-adding-additional-form-data).

## 2. Créer un script Google Apps

- Cliquez sur "Outils > Editeur de script...", ce qui devrait ouvrir un nouvel onglet.
- Renommez-le `Submit Form to Google Sheets`. 

> Assurez-vous d'attendre qu'il enregistre et mette à jour le titre avant d'éditer le script.

- Maintenant, supprimez le bloc `function myFunction() {}` dans l'onglet `Code.gs`.
- Collez le script suivant à sa place et "Fichier > Enregistrer" :

```js
var sheetName = 'Sheet1' //Attention au nom il est souvent traduit
var scriptProp = PropertiesService.getScriptProperties()

function intialSetup () {
  var activeSpreadsheet = SpreadsheetApp.getActiveSpreadsheet()
  scriptProp.setProperty('key', activeSpreadsheet.getId())
}

function doPost (e) {
  var lock = LockService.getScriptLock()
  lock.tryLock(10000)

  try {
    var doc = SpreadsheetApp.openById(scriptProp.getProperty('key'))
    var sheet = doc.getSheetByName(sheetName)

    var headers = sheet.getRange(1, 1, 1, sheet.getLastColumn()).getValues()[0]
    var nextRow = sheet.getLastRow() + 1

    var newRow = headers.map(function(header) {
      return header === 'timestamp' ? new Date() : e.parameter[header]
    })

    sheet.getRange(nextRow, 1, 1, newRow.length).setValues([newRow])

    return ContentService
      .createTextOutput(JSON.stringify({ 'result': 'success', 'row': nextRow }))
      .setMimeType(ContentService.MimeType.JSON)
  }

  catch (e) {
    return ContentService
      .createTextOutput(JSON.stringify({ 'result': 'error', 'error': e }))
      .setMimeType(ContentService.MimeType.JSON)
  }

  finally {
    lock.releaseLock()
  }
}
```

> Si vous voulez mieux comprendre ce que fait ce script, consultez le fichier dans le repo pour une explication détaillée. 

[`form-script-commented.js`](https://github.com/jamiewilson/form-to-google-sheets/blob/master/form-script-commented.js) 

## 3. Exécuter la fonction de configuration

- Ensuite, allez à ` Exécuter > Exécuter la fonction > Configuration initiale ` pour exécuter cette fonction.
- Dans la boîte de dialogue `Autorisation requise`, cliquez sur `Réviser les autorisations`.
- Connectez-vous ou choisissez le compte Google associé à ce projet.
- Vous devriez voir un dialogue qui dit `Hi {votre nom}`, `Soumettre le formulaire à Google Sheets veut`...
> Cliquez sur `Autoriser`.


## 4. Ajouter un nouveau déclencheur de projet 
- Cliquez sur `Editer > Déclencheurs du projet en cours`. 
- Dans la boîte de dialogue, cliquez sur `Aucun déclencheur n'est configuré`. Cliquez ici pour en ajouter un maintenant. 
- Dans les listes déroulantes, sélectionnez `doPost`.
- Définissez les champs d'événements à `De la feuille de calcul` et `Sur l'envoi du formulaire`.
- Cliquez ensuite sur `Sauvegarder`.

## 5. Publier le projet sous forme d'application web

- Cliquez sur `Publish > Deploy as web app...`.
- Définissez `Version du projet` sur `Nouveau` et mettez `version initiale` dans le champ de saisie ci-dessous.
- Laissez `Exécuter l'application en tant que:` défini sur `Me(your@address.com)`.
- Pour `Qui a accès à l'application:` sélectionnez `Anyone, even anonymous`.
- Cliquez sur `Déployer`.
- Dans le popup, copiez l'URL de l'application Web actuelle à partir de la boîte de dialogue.
- Et cliquez sur `OK`.

> **IMPORTANT!** Si vous avez un domaine personnalisé avec Gmail, vous devez peut-être cliquer sur "OK", rafraîchir la page, puis aller sur `Publier > Déployer en tant qu'application Web...` pour obtenir l'URL de l'application Web appropriée. Cela devrait ressembler à quelque chose comme `https://script.google.com/a/yourdomain.com/macros/s/XXXX…`.

## 6. Saisissez l'URL de votre application web

Ouvrez le fichier nommé `index.html`. Sur la ligne 12, remplacez `<SCRIPT URL>` par l'url de votre script :

```js
<form name="submit-to-google-sheet">
  <input name="email" type="email" placeholder="Email" required>
  <button type="submit">Send</button>
</form>

<script>
  const scriptURL = '<SCRIPT URL>'
  const form = document.forms['submit-to-google-sheet']

  form.addEventListener('submit', e => {
    e.preventDefault()
    fetch(scriptURL, { method: 'POST', body: new FormData(form)})
      .then(response => console.log('Success!', response))
      .catch(error => console.error('Error!', error.message))
  })
</script>
```

Comme vous pouvez le voir, ce script utilise l'API [Fetch] (https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API), un mécanisme assez nouveau basé sur des promesses pour effectuer des requêtes sur le web. Il effectue une requête "POST" sur l'URL de votre script et utilise [FormData](https://developer.mozilla.org/en-US/docs/Web/API/FormData) pour transmettre nos données comme paramètres d'URL.


Comme Fetch et FormData ne sont pas entièrement pris en charge, vous voudrez probablement inclure leurs polyfills respectifs. [Voir section #8](#8-Polyfills-connexes). 

> Les balises `<html>`, `<head>` et `body` font partie d'une poignée de balises optionnelles, mais comme les [règles relatives à l'analyse d'une page par le navigateur sont assez compliquées](https://www.w3.org/TR/2011/WD-html5-20110525/syntax.html#optional-tags), vous ne voudrez probablement pas les omettre sur les vrais sites web.

## 7. Ajout de données supplémentaires dans le formulaire
Pour saisir des données supplémentaires, il vous suffit de créer de nouvelles colonnes dont les titres correspondent exactement aux valeurs `nom` de vos entrées de formulaire. Par exemple, si vous voulez ajouter des entrées de prénom et de nom, vous leur donnerez des valeurs de `nom` comme ça :

```html
<form name="submit-to-google-sheet">
  <input name="email" type="email" placeholder="Email" required>
  <input name="firstName" type="text" placeholder="First Name">
  <input name="lastName" type="text" placeholder="Last Name">
  <button type="submit">Send</button>
</form>
```

Créez ensuite de nouveaux en-têtes avec les valeurs exactes, sensibles à la casse, des `noms` :

|   |     A     |   B   |     C     |     D    | ... |
|---|:---------:|:-----:|:---------:|:--------:|:---:|
| 1 | timestamp | email | firstName | lastName |     |

## 8. Polyfills connexes
Certains de ces éléments ne sont pas encore totalement pris en charge par les navigateurs ou ne fonctionnent pas sur les anciens. Voici quelques options de polyfill à utiliser pour une meilleure prise en charge.

- [Promise Polyfill](https://github.com/taylorhakes/promise-polyfill)
- [Fetch Polyfill](https://github.com/github/fetch)
- [FormData Polyfill](https://github.com/jimmywarting/FormData)

Étant donné que la polyfill FormData est publiée sous forme de paquet Node et doit être compilée pour que les navigateurs puissent fonctionner, une bonne option pour les inclure consiste à utiliser [Browserify's CDN appelé wzrd.in](https://wzrd.in/). Ce service compile, miniaturise et met à notre disposition la dernière version de ces scripts.

Vous devez vous assurer de leur chargement avant que le script principal ne traite la soumission du formulaire, par exemple


```html
<script src="https://wzrd.in/standalone/formdata-polyfill"></script>
<script src="https://wzrd.in/standalone/promise-polyfill@latest"></script>
<script src="https://wzrd.in/standalone/whatwg-fetch@latest"></script>

<script>
  const scriptURL = '<SCRIPT URL>'
  const form = document.forms['submit-to-google-sheet']
  ...
</script>
```

## Vous avez des commentaires, des demandes ou des problèmes ?

Veuillez créer une issue (https://github.com/jamiewilson/form-to-google-sheet/issues). Les relations publiques sont les bienvenues, mais n'hésitez pas à me faire part de vos idées avant de vous lancer dans un travail important. Je vous remercie !

#### Des aricles similaires
- [Google Spreadsheets as a Database – INSERT with Apps Script form POST/GET submit method](https://mashe.hawksey.info/2011/10/google-spreadsheets-as-a-database-insert-with-apps-script-form-postget-submit-method/)
- [Step by step setup to send form data to Google Sheets](http://railsrescue.com/blog/2015-05-28-step-by-step-setup-to-send-form-data-to-google-sheets/)
- [Google Sheet Form Post](https://gist.github.com/willpatera/ee41ae374d3c9839c2d6)
- [How to Submit an HTML Form to Google Sheets…without Google Forms](https://medium.com/@dmccoy/how-to-submit-an-html-form-to-google-sheets-without-google-forms-b833952cc175)
- [Send Email from a Static HTML Form using Google Apps Mail!](https://github.com/dwyl/html-form-send-email-via-google-script-without-server)