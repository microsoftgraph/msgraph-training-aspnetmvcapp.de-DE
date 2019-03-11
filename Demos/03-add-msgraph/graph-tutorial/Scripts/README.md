<!-- IGNORE THE HTML BLOCK BELOW, THE INTERESTING PART IS AFTER IT -->

<h1 align="center">Popper. js</h1>

<p align="center">
    <strong>Eine Bibliothek zur Positionierung von Poppers in Webanwendungen.</strong>
</p>

<p align="center">
    <a href="https://travis-ci.org/FezVrasta/popper.js/branches" target="_blank"><img src="https://travis-ci.org/FezVrasta/popper.js.svg?branch=master" alt="Build Status"/></a>
    <img src="http://img.badgesize.io/https://unpkg.com/popper.js/dist/popper.min.js?compression=gzip" alt="Stable Release Size"/>
    <a href="https://www.bithound.io/github/FezVrasta/popper.js"><img src="https://www.bithound.io/github/FezVrasta/popper.js/badges/score.svg" alt="bitHound Overall Score"></a>
    <a href="https://codeclimate.com/github/FezVrasta/popper.js/coverage"><img src="https://codeclimate.com/github/FezVrasta/popper.js/badges/coverage.svg" alt="Istanbul Code Coverage"/></a>
    <a href="https://gitter.im/FezVrasta/popper.js" target="_blank"><img src="https://img.shields.io/gitter/room/nwjs/nw.js.svg" alt="Get support or discuss"/></a>
    <br />
    <a href="https://saucelabs.com/u/popperjs" target="_blank"><img src="https://badges.herokuapp.com/browsers?labels=none&googlechrome=latest&firefox=latest&microsoftedge=latest&iexplore=11,10&safari=latest&iphone=latest" alt="SauceLabs Reports"/></a>
</p>

<img src="https://raw.githubusercontent.com/FezVrasta/popper.js/master/popperjs.png" align="right" width=250 />

<!-- 🚨 HEY! HERE BEGINS THE INTERESTING STUFF 🚨 -->

## <a name="wut-poppers"></a>Wut? Poppers?

Ein Popper ist ein Element auf dem Bildschirm, das aus dem natürlichen Fluss der Anwendung ausgeht.  
Häufige Beispiele für Poppers sind QuickInfos, popovers und Dropdowns.


## <a name="so-yet-another-tooltip-library"></a>Eine weitere ToolTip-Bibliothek?

Nun, im Grunde genommen **Nein**.  
Popper. js ist ein **Positioniermodul**, dessen Zweck es ist, die Position eines Elements zu berechnen, um es in der Nähe eines bestimmten Reference-Elements positionieren zu können.  

Das Modul ist vollständig modular aufgebaut, und die meisten seiner Features werden als **Modifizierer** implementiert (ähnlich wie bei Middleware oder Plugins).  
Die gesamte CodeBase ist in ES2015 geschrieben, und ihre Features werden dank [SauceLabs](https://saucelabs.com/) und [TravisCI](https://travis-ci.org/)automatisch auf echten Browsern getestet.

Popper. js hat keine Abhängigkeiten. Keine jQuery, keine LoDash, nichts.  
Sie wird von großen Unternehmen wie [Twitter in Bootstrap V4](https://getbootstrap.com/), [Microsoft in webclipper](https://github.com/OneNoteDev/WebClipper) und [Atlassian in AtlasKit](https://aui-cdn.atlassian.com/atlaskit/registry/)verwendet.

### <a name="popperjs"></a>Popper. js

Hierbei handelt es sich um das Modul, die Bibliothek, die die Formatvorlagen für die Poppers berechnet und optional anwendet.

Einige der wichtigsten Punkte sind:

- Positions Elemente, die Sie in Ihrem ursprünglichen DOM-Kontext halten (nicht mit Ihrem DOM!);
- Ermöglicht den Export der berechneten Informationen, um Sie in Reaktions-und andere Ansichts Bibliotheken zu integrieren;
- Unterstützt Schatten-DOM-Elemente;
- Vollständig anpassbar dank der Modifiers-basierten Struktur;

Besuchen Sie unsere [Projektseite](https://fezvrasta.github.io/popper.js) , um eine Vielzahl von Beispielen dazu zu erhalten, was Sie mit Popper. js tun können!

Hier finden Sie [die Dokumentation](/docs/_includes/popper-documentation.md).


### <a name="tooltipjs"></a>ToolTip. js

Da viele Benutzer einfach eine einfache Möglichkeit benötigen, leistungsstarke QuickInfos in Ihre Projekte zu integrieren, haben wir **ToolTip. js**erstellt.  
Es handelt sich um eine kleine Bibliothek, die die automatische Erstellung von QuickInfos mit AS Engine Popper. js ermöglicht.  
Die API ist fast identisch mit dem bekannten ToolTip-System von Bootstrap, sodass es einfach ist, es in Ihre Projekte zu integrieren.  
Die QuickInfos, die von ToolTip. js generiert werden, sind `aria` Dank der Tags zugänglich.

Hier finden Sie [die Dokumentation](/docs/_includes/tooltip-documentation.md).


## <a name="installation"></a>Installation
Popper. js ist auf den folgenden Paketmanagern und CDNs verfügbar:

| Quelle |                                                                                  |
|:-------|:---------------------------------------------------------------------------------|
| npm    | `npm install popper.js --save`                                                   |
| Garn   | `yarn add popper.js`                                                             |
| NuGet  | `PM> Install-Package popper.js`                                                  |
| Bower  | `bower install popper.js --save`                     |
| unpkg  | [`https://unpkg.com/popper.js`](https://unpkg.com/popper.js)                     |
| cdnjs  | [`https://cdnjs.com/libraries/popper.js`](https://cdnjs.com/libraries/popper.js) |

ToolTip. js:

| Quelle |                                                                                  |
|:-------|:---------------------------------------------------------------------------------|
| npm    | `npm install tooltip.js --save`                                                  |
| Garn   | `yarn add tooltip.js`                                                            |
| Bower | `bower install tooltip.js=https://unpkg.com/tooltip.js --save`                   |
| unpkg  | [`https://unpkg.com/tooltip.js`](https://unpkg.com/tooltip.js)                   |
| cdnjs  | [`https://cdnjs.com/libraries/popper.js`](https://cdnjs.com/libraries/popper.js) |

\*: Bower wird nicht offiziell unterstützt, es kann verwendet werden, um ToolTip. js nur über das unpkg.com CDN zu installieren. Diese Methode hat die Einschränkung, dass keine bestimmte Version der Bibliothek definiert werden kann. Bower und Popper. js schlagen vor, NPM oder Garn für Ihre Projekte zu verwenden.  
Weitere Informationen finden [Sie im zugehörigen Thema](https://github.com/FezVrasta/popper.js/issues/390).

### <a name="dist-targets"></a>Dist-Ziele

Popper. js ist derzeit mit 3 Zielen im Kopf: UMD, ESM und ESNext ausgeliefert.

- UMD-Universal Modul Definition: AMD, RequireJS und Globals;
- ESM-ES-Module: für WebPack/Rollup oder Browser, der die Spezifikation unterstützt;
- ESNext: verfügbar in `dist/`, kann mit WebPack verwendet werden und `babel-preset-env`;

Stellen Sie sicher, dass Sie die richtige für Ihre Anforderungen verwenden. Wenn Sie es mit einem `<script>` Tag importieren möchten, verwenden Sie UMD.

## <a name="usage"></a>Verwendung

Wenn ein Popper-DOM-Knoten vorhanden ist, bitten Sie Popper. js, ihn in der Nähe seiner Schaltfläche zu positionieren.

```js
var reference = document.querySelector('.my-button');
var popper = document.querySelector('.my-popper');
var anotherPopper = new Popper(
    reference,
    popper,
    {
        // popper options here
    }
);
```

### <a name="callbacks"></a>Rückrufe

Popper. js unterstützt zwei Arten von Rückrufen, `onCreate` der Rückruf wird aufgerufen, nachdem der Popper initalized wurde. Die `onUpdate` eine wird bei jeder nachfolgenden Aktualisierung aufgerufen.

```js
const reference = document.querySelector('.my-button');
const popper = document.querySelector('.my-popper');
new Popper(reference, popper, {
    onCreate: (data) => {
        // data is an object containing all the informations computed
        // by Popper.js and used to style the popper and its arrow
        // The complete description is available in Popper.js documentation
    },
    onUpdate: (data) => {
        // same as `onCreate` but called on subsequent updates
    }
});
```

### <a name="writing-your-own-modifiers"></a>Schreiben eigener Modifizierer

Popper. js basiert auf einer "Plugin-artigen" Architektur, die meisten seiner Features sind vollständig gekapselte "Modifiers".  
Ein Modifizierer ist eine Funktion, die aufgerufen wird, wenn Popper. js die Position des Popper berechnen muss. Aus diesem Grund sollten Modifikatoren sehr leistungsfähig sein, um Engpässe zu vermeiden.  

Weitere Informationen zum Erstellen eines Modifizierers finden [Sie in der Dokumentation](docs/_includes/popper-documentation.md#modifiers--object) zur Modifiers.


### <a name="react-vuejs-angular-angularjs-emberjs-etc-integration"></a>ReAgiere, vue. js, eckig, AngularJS, glut. js (etc...) Integration

Die Integration von Drittanbieterbibliotheken in reagieren oder anderen Bibliotheken kann schmerzhaft sein, da Sie in der Regel das DOM ändern und die Bibliotheken verrückt fahren.  
Popper. js schränkt alle seine DOM-Modifikationen `applyStyle` innerhalb des Modifizierers ein, Sie können es einfach deaktivieren und die Popper-Koordinaten mithilfe Ihrer bevorzugten Bibliothek manuell anwenden.  

Eine umfassende Liste der Bibliotheken, mit denen Sie Popper. js in vorhandene Frameworks verwenden können, finden [](/MENTIONS.md) Sie auf der Seite Erwähnungen.

Alternativ können Sie Ihre eigenen Benutzer mit Ihrem `applyStyles` eigenen überschreiben und Popper. js selbst integrieren!

```js
function applyReactStyle(data) {
    // export data in your framework and use its content to apply the style to your popper
};

const reference = document.querySelector('.my-button');
const popper = document.querySelector('.my-popper');
new Popper(reference, popper, {
    modifiers: {
        applyStyle: { enabled: false },
        applyReactStyle: {
            enabled: true,
            fn: applyReactStyle,
            order: 800,
        },
    },
});

```

### <a name="migration-from-popperjs-v0"></a>Migration von Popper. js v0

Da sich die API geändert hat, haben wir einige Migrations Anweisungen vorbereitet, um das Upgrade auf Popper. js V1 zu vereinfachen.  

https://github.com/FezVrasta/popper.js/issues/62

Wenn Sie Fragen haben, können Sie das Problem in diesem Fall kommentieren.

### <a name="performances"></a>Leistungen

Popper. js ist sehr leistungsfähig. In der Regel dauert es 0,5 Millisekunden, bis die Position eines Popper berechnet wird (auf einem iMac mit Intel Core i5 mit 3,5 G GHz).  
Dies führt dazu, dass keine [Jank](https://www.chromium.org/developers/how-tos/trace-event-profiling-tool/anatomy-of-jank)verursacht wird, was zu einer reibungslosen Benutzerfreundlichkeit führen kann.

## <a name="notes"></a>Hinweise

### <a name="libraries-using-popperjs"></a>Bibliotheken mit Popper. js

Das Ziel von Popper. js ist es, ein stabiles und leistungsstarkes Positioniermodul bereitzustellen, das in Bibliotheken von Drittanbietern verwendet werden kann.  

Besuchen Sie [](/MENTIONS.md) die Seite Erwähnungen für eine aktualisierte Liste der Projekte.


### <a name="credits"></a>Mitwirkende
Ich möchte einigen Freunden und Projekten für die Arbeit danken, die Sie getan haben:

- [@AndreaScn](https://github.com/AndreaScn) für seine Arbeit auf der GitHub-Seite und die manuellen Tests, die er während der Entwicklung durchgeführt hat;
- [@vampolo](https://github.com/vampolo) für die ursprüngliche Idee und für den Namen der Bibliothek;
- [Sysdig](https://github.com/Draios) für all die tollen Dinge, die ich in diesen Jahren gelernt habe, die es mir ermöglicht haben, diese Bibliothek zu schreiben;
- [Tether. js](http://github.hubspot.com/tether/) , dass Sie mich dazu inspiriert haben, eine Positionierungs Bibliothek für die reale Welt zu schreiben.
- [Die Mitwirkenden](https://github.com/FezVrasta/popper.js/graphs/contributors) für Ihre sehr geschätzten Pull-Anforderungen und Fehlerberichte;
- **Sie** für den Stern, den Sie diesem Projekt geben werden, und dafür, dass Sie so genial sind, um diesem Projekt einen Versuch zu geben🙂

### <a name="copyright-and-license"></a>Urheberrecht und Lizenz
Code und Dokumentation Copyright 2016 **Federico Zivolo**. Code, der unter der [mit-Lizenz](LICENSE.md)veröffentlicht wurde. Dokumente, die unter Creative Commons veröffentlicht wurden.
