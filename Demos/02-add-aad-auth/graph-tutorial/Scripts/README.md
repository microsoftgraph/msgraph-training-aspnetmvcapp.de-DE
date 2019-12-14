<!-- IGNORE THE HTML BLOCK BELOW, THE INTERESTING PART IS AFTER IT -->

<h1 align="center">Popper. js</h1>

<p align="center">
    <strong>Eine Bibliothek, mit der Poppers in Webanwendungen positioniert werden.</strong>
</p>

<p align="center">
    <img src="http://badge-size.now.sh/https://unpkg.com/popper.js/dist/popper.min.js?compression=brotli" alt="Stable Release Size"/>
  <img src="http://badge-size.now.sh/https://unpkg.com/popper.js/dist/popper.min.js?compression=gzip" alt="Stable Release Size"/>
    <a href="https://codeclimate.com/github/FezVrasta/popper.js/coverage"><img src="https://codeclimate.com/github/FezVrasta/popper.js/badges/coverage.svg" alt="Istanbul Code Coverage"/></a>
    <a href="https://www.npmjs.com/browse/depended/popper.js"><img src="https://badgen.net/npm/dependents/popper.js" alt="Dependents packages" /></a>
    <a href="https://spectrum.chat/popper-js" target="_blank"><img src="https://img.shields.io/badge/chat-on_spectrum-6833F9.svg?logo=data%3Aimage%2Fsvg%2Bxml%3Bbase64%2CPHN2ZyBpZD0iTGl2ZWxsb18xIiBkYXRhLW5hbWU9IkxpdmVsbG8gMSIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIiB2aWV3Qm94PSIwIDAgMTAgOCI%2BPGRlZnM%2BPHN0eWxlPi5jbHMtMXtmaWxsOiNmZmY7fTwvc3R5bGU%2BPC9kZWZzPjx0aXRsZT5zcGVjdHJ1bTwvdGl0bGU%2BPHBhdGggY2xhc3M9ImNscy0xIiBkPSJNNSwwQy40MiwwLDAsLjYzLDAsMy4zNGMwLDEuODQuMTksMi43MiwxLjc0LDMuMWgwVjcuNThhLjQ0LjQ0LDAsMCwwLC42OC4zNUw0LjM1LDYuNjlINWM0LjU4LDAsNS0uNjMsNS0zLjM1UzkuNTgsMCw1LDBaTTIuODMsNC4xOGEuNjMuNjMsMCwxLDEsLjY1LS42M0EuNjQuNjQsMCwwLDEsMi44Myw0LjE4Wk01LDQuMThhLjYzLjYzLDAsMSwxLC42NS0uNjNBLjY0LjY0LDAsMCwxLDUsNC4xOFptMi4xNywwYS42My42MywwLDEsMSwuNjUtLjYzQS42NC42NCwwLDAsMSw3LjE3LDQuMThaIi8%2BPC9zdmc%2B" alt="Get support or discuss"/></a>
    <br />
    <a href="https://travis-ci.org/FezVrasta/popper.js/branches" target="_blank"><img src="https://travis-ci.org/FezVrasta/popper.js.svg?branch=master" alt="Build Status"/></a>
    <a href="https://saucelabs.com/u/popperjs" target="_blank"><img src="https://badges.herokuapp.com/browsers?labels=none&googlechrome=latest&firefox=latest&microsoftedge=latest&iexplore=11,10&safari=latest" alt="SauceLabs Reports"/></a>
</p>

<img src="https://raw.githubusercontent.com/FezVrasta/popper.js/master/popperjs.png" align="right" width=250 />

<!-- 🚨 HEY! HERE BEGINS THE INTERESTING STUFF 🚨 -->

## <a name="wut-poppers"></a>Wut? Poppers?

Ein Popper ist ein Element auf dem Bildschirm, das aus dem natürlichen Fluss ihrer Anwendung "springt".  
Häufige Beispiele für Poppers sind QuickInfos, popover und Dropdowns.


## <a name="so-yet-another-tooltip-library"></a>Und noch eine weitere ToolTip-Bibliothek?

Nun, im Grunde **Nein**.  
Popper. js ist ein **Positionierungs Modul**, dessen Zweck es ist, die Position eines Elements zu berechnen, damit es in der Nähe eines bestimmten Reference-Elements positioniert werden kann.  

Das Modul ist vollständig modular aufgebaut, und die meisten seiner Features werden als **Modifizierer** implementiert (ähnlich wie Middlewares oder Plugins).  
Die gesamte Codebasis ist in ES2015 geschrieben, und seine Features werden dank [SauceLabs](https://saucelabs.com/) und [TravisCI](https://travis-ci.org/)automatisch auf realen Browsern getestet.

Popper. js hat keine Abhängigkeiten. Kein jQuery, kein LoDash, nichts.  
Es wird von großen Unternehmen wie [Twitter in Bootstrap V4](https://getbootstrap.com/), [Microsoft in webclipper](https://github.com/OneNoteDev/WebClipper) und [Atlassian in AtlasKit](https://aui-cdn.atlassian.com/atlaskit/registry/)verwendet.

### <a name="popperjs"></a>Popper. js

Dies ist das Modul, die Bibliothek, die die Formatvorlagen berechnet und optional auf die Poppers anwendet.

Einige der wichtigsten Punkte sind:

- Positionierungs Elemente, die Sie im ursprünglichen Dom-Kontext halten (nicht mit Ihrem Dom durcheinander);
- Ermöglicht das Exportieren der berechneten Informationen in die Integration in Reaktions-und andere Ansichts Bibliotheken;
- Unterstützt Shadow DOM-Elemente;
- Vollständig anpassbar dank der Modifizierer-basierten Struktur;

Besuchen Sie unsere [Projektseite](https://fezvrasta.github.io/popper.js) , um zahlreiche Beispiele dafür zu finden, was Sie mit Popper. js tun können!

Hier finden Sie [die Dokumentation](/docs/_includes/popper-documentation.md).


### <a name="tooltipjs"></a>ToolTip. js

Da viele Benutzer nur eine einfache Möglichkeit benötigen, leistungsstarke Tooltips in Ihre Projekte zu integrieren, haben wir **ToolTip. js**erstellt.  
Es handelt sich um eine kleine Bibliothek, die das automatische Erstellen von QuickInfos mit AS Engine Popper. js vereinfacht.  
Die API ist fast identisch mit dem berühmten ToolTip-System von Bootstrap, auf diese Weise wird Sie einfach in Ihre Projekte integriert.  
Auf die von ToolTip. js generierten QuickInfos kann dank der `aria` Tags zugegriffen werden.

Hier finden Sie [die Dokumentation](/docs/_includes/tooltip-documentation.md).


## <a name="installation"></a>Installation
Popper. js steht in den folgenden Paket-Managern und CDNs zur Verfügung:

| Source |                                                                                  |
|:-------|:---------------------------------------------------------------------------------|
| npm    | `npm install popper.js --save`                                                   |
| Garn   | `yarn add popper.js`                                                             |
| NuGet  | `PM> Install-Package popper.js`                                                  |
| Bower  | `bower install popper.js --save`                     |
| unpkg  | [`https://unpkg.com/popper.js`](https://unpkg.com/popper.js)                     |
| cdnjs  | [`https://cdnjs.com/libraries/popper.js`](https://cdnjs.com/libraries/popper.js) |

ToolTip. js außerdem:

| Source |                                                                                  |
|:-------|:---------------------------------------------------------------------------------|
| npm    | `npm install tooltip.js --save`                                                  |
| Garn   | `yarn add tooltip.js`                                                            |
| Bower | `bower install tooltip.js=https://unpkg.com/tooltip.js --save`                   |
| unpkg  | [`https://unpkg.com/tooltip.js`](https://unpkg.com/tooltip.js)                   |
| cdnjs  | [`https://cdnjs.com/libraries/popper.js`](https://cdnjs.com/libraries/popper.js) |

\*: Bower wird nicht offiziell unterstützt, sondern kann zur Installation von ToolTip. js nur über das unpkg.com-CDN verwendet werden. Diese Methode hat die Einschränkung, dass keine bestimmte Version der Bibliothek definiert werden kann. Bower und Popper. js schlägt vor, NPM oder Garn für Ihre Projekte zu verwenden.  
Weitere Informationen finden Sie in diesem [Thema](https://github.com/FezVrasta/popper.js/issues/390).

### <a name="dist-targets"></a>Dist-Ziele

Popper. js wird derzeit mit drei Zielen im Hinterkopf ausgeliefert: UMD, ESM und ESNext.

- UMD-universelle Modul Definition: AMD, RequireJS und Globals;
- ESM-es-Module: für WebPack/Rollup oder Browser zur Unterstützung der Spezifikation;
- ESNext: verfügbar in `dist/`, kann mit WebPack verwendet werden und `babel-preset-env`;

Stellen Sie sicher, dass Sie die richtige für Ihre Anforderungen verwenden. Wenn Sie Sie mit einem `<script>` -Tag importieren möchten, verwenden Sie UMD.

## <a name="usage"></a>Verwendung

Wenn ein vorhandener Popper DOM-Knoten vorhanden ist, Fragen Sie Popper. js, um ihn in der Nähe seiner Schaltfläche zu positionieren.

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

Popper. js unterstützt zwei Arten von Rückrufen, `onCreate` der Rückruf wird aufgerufen, nachdem das Popper initialisiert wurde. Die `onUpdate` eine wird bei jeder nachfolgenden Aktualisierung aufgerufen.

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

Popper. js basiert auf einer "Plugin-artigen" Architektur, die meisten seiner Features sind vollständig gekapselte "Modifizierer".  
Ein Modifizierer ist eine Funktion, die jedes Mal aufgerufen wird, wenn Popper. js die Position von Popper berechnen muss. Aus diesem Grund sollten Modifizierer sehr leistungsfähig sein, um Engpässe zu vermeiden.  

Informationen zum Erstellen eines Modifikators finden [Sie in der Dokumentation zu Modifizierern](docs/_includes/popper-documentation.md#modifiers--object) .


### <a name="react-vuejs-angular-angularjs-emberjs-etc-integration"></a>"Reagiere", "Vue. js", "Kantig", "AngularJS", "Glut. js" (etc...)-Integration

Das Integrieren von Drittanbieterbibliotheken in Reaktions-oder anderen Bibliotheken kann ein Problem sein, da Sie das DOM normalerweise ändern und die Bibliotheken verrückt steuern.  
Popper. js schränkt alle DOM-Änderungen innerhalb des `applyStyle` Modifikators ein, Sie können ihn einfach deaktivieren und die Popper-Koordinaten mithilfe Ihrer Bibliothek von Choice manuell anwenden.  

Eine umfassende Liste der Bibliotheken, mit denen Sie Popper. js in vorhandenen Frameworks verwenden können, finden Sie auf der Seite [Erwähnungen](/MENTIONS.md) .

Alternativ können Sie Ihre eigenen `applyStyles` mit Ihrem benutzerdefinierten überschreiben und Popper. js selbst integrieren!

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

Da die API geändert wurde, haben wir einige Migrations Anweisungen vorbereitet, um das Upgrade auf Popper. js V1 zu vereinfachen.  

https://github.com/FezVrasta/popper.js/issues/62

Wenn Sie Fragen haben, können Sie sich in das Problem einkommentieren.

### <a name="performances"></a>Leistungen

Popper. js ist sehr leistungsfähig. Normalerweise werden 0,5 ms benötigt, um die Position eines Popper zu berechnen (auf einem iMac mit 3,5 g GHz Intel Core i5).  
Dies bedeutet, dass keine [Jank](https://www.chromium.org/developers/how-tos/trace-event-profiling-tool/anatomy-of-jank)verursacht wird, was zu einem reibungslosen Benutzererlebnis führt.

## <a name="notes"></a>Notes

### <a name="libraries-using-popperjs"></a>Bibliotheken mit Popper. js

Das Ziel von Popper. js besteht darin, ein stabiles und leistungsfähiges Positioniermodul bereitzustellen, das in Bibliotheken von Drittanbietern verwendet werden kann.  

Besuchen Sie die Seite [Erwähnungen](/MENTIONS.md) für eine aktualisierte Liste der Projekte.


### <a name="credits"></a>Mitwirkende
Ich möchte einigen Freunden und Projekten für Ihre Arbeit danken:

- [@AndreaScn](https://github.com/AndreaScn) für seine Arbeit auf der GitHub-Seite und die manuellen Tests, die er während der Entwicklung durchführte;
- [@vampolo](https://github.com/vampolo) für die ursprüngliche Idee und für den Namen der Bibliothek;
- [Sysdig](https://github.com/Draios) für all die tollen Dinge, die ich in diesen Jahren gelernt habe, die es mir ermöglicht haben, diese Bibliothek zu schreiben;
- [Tether. js](http://github.hubspot.com/tether/) , weil ich mich beim Schreiben einer Positionier Bibliothek für die reale Welt inspiriert habe;
- [Die Mitwirkenden](https://github.com/FezVrasta/popper.js/graphs/contributors) für Ihre sehr geschätzten Pull-Anforderungen und Fehlerberichte;
- **Sie** für den Stern, den Sie diesem Projekt geben werden, und dass er so genial ist, um diesem Projekt einen Versuch zu geben🙂

### <a name="copyright-and-license"></a>Copyright und Lizenz
Code und Dokumentation Copyright 2016 **Federico Zivolo**. Code, der unter der [mit-Lizenz](LICENSE.md)veröffentlicht wurde. Dokumente werden unter Creative Commons veröffentlicht.
