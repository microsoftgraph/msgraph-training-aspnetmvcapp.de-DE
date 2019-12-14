<!-- IGNORE THE HTML BLOCK BELOW, THE INTERESTING PART IS AFTER IT -->

<h1 align="center"><span data-ttu-id="fa6e9-101">Popper. js</span><span class="sxs-lookup"><span data-stu-id="fa6e9-101">Popper.js</span></span></h1>

<p align="center"><span data-ttu-id="fa6e9-102">
    <strong>Eine Bibliothek, mit der Poppers in Webanwendungen positioniert werden.</strong>
</span><span class="sxs-lookup"><span data-stu-id="fa6e9-102">
    <strong>A library used to position poppers in web applications.</strong>
</span></span></p>

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

## <a name="wut-poppers"></a><span data-ttu-id="fa6e9-103">Wut?</span><span class="sxs-lookup"><span data-stu-id="fa6e9-103">Wut?</span></span> <span data-ttu-id="fa6e9-104">Poppers?</span><span class="sxs-lookup"><span data-stu-id="fa6e9-104">Poppers?</span></span>

<span data-ttu-id="fa6e9-105">Ein Popper ist ein Element auf dem Bildschirm, das aus dem natürlichen Fluss ihrer Anwendung "springt".</span><span class="sxs-lookup"><span data-stu-id="fa6e9-105">A popper is an element on the screen which "pops out" from the natural flow of your application.</span></span>  
<span data-ttu-id="fa6e9-106">Häufige Beispiele für Poppers sind QuickInfos, popover und Dropdowns.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-106">Common examples of poppers are tooltips, popovers and drop-downs.</span></span>


## <a name="so-yet-another-tooltip-library"></a><span data-ttu-id="fa6e9-107">Und noch eine weitere ToolTip-Bibliothek?</span><span class="sxs-lookup"><span data-stu-id="fa6e9-107">So, yet another tooltip library?</span></span>

<span data-ttu-id="fa6e9-108">Nun, im Grunde **Nein**.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-108">Well, basically, **no**.</span></span>  
<span data-ttu-id="fa6e9-109">Popper. js ist ein **Positionierungs Modul**, dessen Zweck es ist, die Position eines Elements zu berechnen, damit es in der Nähe eines bestimmten Reference-Elements positioniert werden kann.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-109">Popper.js is a **positioning engine**, its purpose is to calculate the position of an element to make it possible to position it near a given reference element.</span></span>  

<span data-ttu-id="fa6e9-110">Das Modul ist vollständig modular aufgebaut, und die meisten seiner Features werden als **Modifizierer** implementiert (ähnlich wie Middlewares oder Plugins).</span><span class="sxs-lookup"><span data-stu-id="fa6e9-110">The engine is completely modular and most of its features are implemented as **modifiers** (similar to middlewares or plugins).</span></span>  
<span data-ttu-id="fa6e9-111">Die gesamte Codebasis ist in ES2015 geschrieben, und seine Features werden dank [SauceLabs](https://saucelabs.com/) und [TravisCI](https://travis-ci.org/)automatisch auf realen Browsern getestet.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-111">The whole code base is written in ES2015 and its features are automatically tested on real browsers thanks to [SauceLabs](https://saucelabs.com/) and [TravisCI](https://travis-ci.org/).</span></span>

<span data-ttu-id="fa6e9-112">Popper. js hat keine Abhängigkeiten.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-112">Popper.js has zero dependencies.</span></span> <span data-ttu-id="fa6e9-113">Kein jQuery, kein LoDash, nichts.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-113">No jQuery, no LoDash, nothing.</span></span>  
<span data-ttu-id="fa6e9-114">Es wird von großen Unternehmen wie [Twitter in Bootstrap V4](https://getbootstrap.com/), [Microsoft in webclipper](https://github.com/OneNoteDev/WebClipper) und [Atlassian in AtlasKit](https://aui-cdn.atlassian.com/atlaskit/registry/)verwendet.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-114">It's used by big companies like [Twitter in Bootstrap v4](https://getbootstrap.com/), [Microsoft in WebClipper](https://github.com/OneNoteDev/WebClipper) and [Atlassian in AtlasKit](https://aui-cdn.atlassian.com/atlaskit/registry/).</span></span>

### <a name="popperjs"></a><span data-ttu-id="fa6e9-115">Popper. js</span><span class="sxs-lookup"><span data-stu-id="fa6e9-115">Popper.js</span></span>

<span data-ttu-id="fa6e9-116">Dies ist das Modul, die Bibliothek, die die Formatvorlagen berechnet und optional auf die Poppers anwendet.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-116">This is the engine, the library that computes and, optionally, applies the styles to the poppers.</span></span>

<span data-ttu-id="fa6e9-117">Einige der wichtigsten Punkte sind:</span><span class="sxs-lookup"><span data-stu-id="fa6e9-117">Some of the key points are:</span></span>

- <span data-ttu-id="fa6e9-118">Positionierungs Elemente, die Sie im ursprünglichen Dom-Kontext halten (nicht mit Ihrem Dom durcheinander);</span><span class="sxs-lookup"><span data-stu-id="fa6e9-118">Position elements keeping them in their original DOM context (doesn't mess with your DOM!);</span></span>
- <span data-ttu-id="fa6e9-119">Ermöglicht das Exportieren der berechneten Informationen in die Integration in Reaktions-und andere Ansichts Bibliotheken;</span><span class="sxs-lookup"><span data-stu-id="fa6e9-119">Allows to export the computed informations to integrate with React and other view libraries;</span></span>
- <span data-ttu-id="fa6e9-120">Unterstützt Shadow DOM-Elemente;</span><span class="sxs-lookup"><span data-stu-id="fa6e9-120">Supports Shadow DOM elements;</span></span>
- <span data-ttu-id="fa6e9-121">Vollständig anpassbar dank der Modifizierer-basierten Struktur;</span><span class="sxs-lookup"><span data-stu-id="fa6e9-121">Completely customizable thanks to the modifiers based structure;</span></span>

<span data-ttu-id="fa6e9-122">Besuchen Sie unsere [Projektseite](https://fezvrasta.github.io/popper.js) , um zahlreiche Beispiele dafür zu finden, was Sie mit Popper. js tun können!</span><span class="sxs-lookup"><span data-stu-id="fa6e9-122">Visit our [project page](https://fezvrasta.github.io/popper.js) to see a lot of examples of what you can do with Popper.js!</span></span>

<span data-ttu-id="fa6e9-123">Hier finden Sie [die Dokumentation](/docs/_includes/popper-documentation.md).</span><span class="sxs-lookup"><span data-stu-id="fa6e9-123">Find [the documentation here](/docs/_includes/popper-documentation.md).</span></span>


### <a name="tooltipjs"></a><span data-ttu-id="fa6e9-124">ToolTip. js</span><span class="sxs-lookup"><span data-stu-id="fa6e9-124">Tooltip.js</span></span>

<span data-ttu-id="fa6e9-125">Da viele Benutzer nur eine einfache Möglichkeit benötigen, leistungsstarke Tooltips in Ihre Projekte zu integrieren, haben wir **ToolTip. js**erstellt.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-125">Since lots of users just need a simple way to integrate powerful tooltips in their projects, we created **Tooltip.js**.</span></span>  
<span data-ttu-id="fa6e9-126">Es handelt sich um eine kleine Bibliothek, die das automatische Erstellen von QuickInfos mit AS Engine Popper. js vereinfacht.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-126">It's a small library that makes it easy to automatically create tooltips using as engine Popper.js.</span></span>  
<span data-ttu-id="fa6e9-127">Die API ist fast identisch mit dem berühmten ToolTip-System von Bootstrap, auf diese Weise wird Sie einfach in Ihre Projekte integriert.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-127">Its API is almost identical to the famous tooltip system of Bootstrap, in this way it will be easy to integrate it in your projects.</span></span>  
<span data-ttu-id="fa6e9-128">Auf die von ToolTip. js generierten QuickInfos kann dank der `aria` Tags zugegriffen werden.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-128">The tooltips generated by Tooltip.js are accessible thanks to the `aria` tags.</span></span>

<span data-ttu-id="fa6e9-129">Hier finden Sie [die Dokumentation](/docs/_includes/tooltip-documentation.md).</span><span class="sxs-lookup"><span data-stu-id="fa6e9-129">Find [the documentation here](/docs/_includes/tooltip-documentation.md).</span></span>


## <a name="installation"></a><span data-ttu-id="fa6e9-130">Installation</span><span class="sxs-lookup"><span data-stu-id="fa6e9-130">Installation</span></span>
<span data-ttu-id="fa6e9-131">Popper. js steht in den folgenden Paket-Managern und CDNs zur Verfügung:</span><span class="sxs-lookup"><span data-stu-id="fa6e9-131">Popper.js is available on the following package managers and CDNs:</span></span>

| <span data-ttu-id="fa6e9-132">Source</span><span class="sxs-lookup"><span data-stu-id="fa6e9-132">Source</span></span> |                                                                                  |
|:-------|:---------------------------------------------------------------------------------|
| <span data-ttu-id="fa6e9-133">npm</span><span class="sxs-lookup"><span data-stu-id="fa6e9-133">npm</span></span>    | `npm install popper.js --save`                                                   |
| <span data-ttu-id="fa6e9-134">Garn</span><span class="sxs-lookup"><span data-stu-id="fa6e9-134">yarn</span></span>   | `yarn add popper.js`                                                             |
| <span data-ttu-id="fa6e9-135">NuGet</span><span class="sxs-lookup"><span data-stu-id="fa6e9-135">NuGet</span></span>  | `PM> Install-Package popper.js`                                                  |
| <span data-ttu-id="fa6e9-136">Bower</span><span class="sxs-lookup"><span data-stu-id="fa6e9-136">Bower</span></span>  | `bower install popper.js --save`                     |
| <span data-ttu-id="fa6e9-137">unpkg</span><span class="sxs-lookup"><span data-stu-id="fa6e9-137">unpkg</span></span>  | [`https://unpkg.com/popper.js`](https://unpkg.com/popper.js)                     |
| <span data-ttu-id="fa6e9-138">cdnjs</span><span class="sxs-lookup"><span data-stu-id="fa6e9-138">cdnjs</span></span>  | [`https://cdnjs.com/libraries/popper.js`](https://cdnjs.com/libraries/popper.js) |

<span data-ttu-id="fa6e9-139">ToolTip. js außerdem:</span><span class="sxs-lookup"><span data-stu-id="fa6e9-139">Tooltip.js as well:</span></span>

| <span data-ttu-id="fa6e9-140">Source</span><span class="sxs-lookup"><span data-stu-id="fa6e9-140">Source</span></span> |                                                                                  |
|:-------|:---------------------------------------------------------------------------------|
| <span data-ttu-id="fa6e9-141">npm</span><span class="sxs-lookup"><span data-stu-id="fa6e9-141">npm</span></span>    | `npm install tooltip.js --save`                                                  |
| <span data-ttu-id="fa6e9-142">Garn</span><span class="sxs-lookup"><span data-stu-id="fa6e9-142">yarn</span></span>   | `yarn add tooltip.js`                                                            |
| <span data-ttu-id="fa6e9-143">Bower</span><span class="sxs-lookup"><span data-stu-id="fa6e9-143">Bower\*</span></span> | `bower install tooltip.js=https://unpkg.com/tooltip.js --save`                   |
| <span data-ttu-id="fa6e9-144">unpkg</span><span class="sxs-lookup"><span data-stu-id="fa6e9-144">unpkg</span></span>  | [`https://unpkg.com/tooltip.js`](https://unpkg.com/tooltip.js)                   |
| <span data-ttu-id="fa6e9-145">cdnjs</span><span class="sxs-lookup"><span data-stu-id="fa6e9-145">cdnjs</span></span>  | [`https://cdnjs.com/libraries/popper.js`](https://cdnjs.com/libraries/popper.js) |

<span data-ttu-id="fa6e9-146">\*: Bower wird nicht offiziell unterstützt, sondern kann zur Installation von ToolTip. js nur über das unpkg.com-CDN verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-146">\*: Bower isn't officially supported, it can be used to install Tooltip.js only trough the unpkg.com CDN.</span></span> <span data-ttu-id="fa6e9-147">Diese Methode hat die Einschränkung, dass keine bestimmte Version der Bibliothek definiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-147">This method has the limitation of not being able to define a specific version of the library.</span></span> <span data-ttu-id="fa6e9-148">Bower und Popper. js schlägt vor, NPM oder Garn für Ihre Projekte zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-148">Bower and Popper.js suggests to use npm or Yarn for your projects.</span></span>  
<span data-ttu-id="fa6e9-149">Weitere Informationen finden Sie in diesem [Thema](https://github.com/FezVrasta/popper.js/issues/390).</span><span class="sxs-lookup"><span data-stu-id="fa6e9-149">For more info, [read the related issue](https://github.com/FezVrasta/popper.js/issues/390).</span></span>

### <a name="dist-targets"></a><span data-ttu-id="fa6e9-150">Dist-Ziele</span><span class="sxs-lookup"><span data-stu-id="fa6e9-150">Dist targets</span></span>

<span data-ttu-id="fa6e9-151">Popper. js wird derzeit mit drei Zielen im Hinterkopf ausgeliefert: UMD, ESM und ESNext.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-151">Popper.js is currently shipped with 3 targets in mind: UMD, ESM and ESNext.</span></span>

- <span data-ttu-id="fa6e9-152">UMD-universelle Modul Definition: AMD, RequireJS und Globals;</span><span class="sxs-lookup"><span data-stu-id="fa6e9-152">UMD - Universal Module Definition: AMD, RequireJS and globals;</span></span>
- <span data-ttu-id="fa6e9-153">ESM-es-Module: für WebPack/Rollup oder Browser zur Unterstützung der Spezifikation;</span><span class="sxs-lookup"><span data-stu-id="fa6e9-153">ESM - ES Modules: For webpack/Rollup or browser supporting the spec;</span></span>
- <span data-ttu-id="fa6e9-154">ESNext: verfügbar in `dist/`, kann mit WebPack verwendet werden und `babel-preset-env`;</span><span class="sxs-lookup"><span data-stu-id="fa6e9-154">ESNext: Available in `dist/`, can be used with webpack and `babel-preset-env`;</span></span>

<span data-ttu-id="fa6e9-155">Stellen Sie sicher, dass Sie die richtige für Ihre Anforderungen verwenden.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-155">Make sure to use the right one for your needs.</span></span> <span data-ttu-id="fa6e9-156">Wenn Sie Sie mit einem `<script>` -Tag importieren möchten, verwenden Sie UMD.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-156">If you want to import it with a `<script>` tag, use UMD.</span></span>

## <a name="usage"></a><span data-ttu-id="fa6e9-157">Verwendung</span><span class="sxs-lookup"><span data-stu-id="fa6e9-157">Usage</span></span>

<span data-ttu-id="fa6e9-158">Wenn ein vorhandener Popper DOM-Knoten vorhanden ist, Fragen Sie Popper. js, um ihn in der Nähe seiner Schaltfläche zu positionieren.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-158">Given an existing popper DOM node, ask Popper.js to position it near its button</span></span>

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

### <a name="callbacks"></a><span data-ttu-id="fa6e9-159">Rückrufe</span><span class="sxs-lookup"><span data-stu-id="fa6e9-159">Callbacks</span></span>

<span data-ttu-id="fa6e9-160">Popper. js unterstützt zwei Arten von Rückrufen, `onCreate` der Rückruf wird aufgerufen, nachdem das Popper initialisiert wurde.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-160">Popper.js supports two kinds of callbacks, the `onCreate` callback is called after the popper has been initialized.</span></span> <span data-ttu-id="fa6e9-161">Die `onUpdate` eine wird bei jeder nachfolgenden Aktualisierung aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-161">The `onUpdate` one is called on any subsequent update.</span></span>

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

### <a name="writing-your-own-modifiers"></a><span data-ttu-id="fa6e9-162">Schreiben eigener Modifizierer</span><span class="sxs-lookup"><span data-stu-id="fa6e9-162">Writing your own modifiers</span></span>

<span data-ttu-id="fa6e9-163">Popper. js basiert auf einer "Plugin-artigen" Architektur, die meisten seiner Features sind vollständig gekapselte "Modifizierer".</span><span class="sxs-lookup"><span data-stu-id="fa6e9-163">Popper.js is based on a "plugin-like" architecture, most of its features are fully encapsulated "modifiers".</span></span>  
<span data-ttu-id="fa6e9-164">Ein Modifizierer ist eine Funktion, die jedes Mal aufgerufen wird, wenn Popper. js die Position von Popper berechnen muss.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-164">A modifier is a function that is called each time Popper.js needs to compute the position of the popper.</span></span> <span data-ttu-id="fa6e9-165">Aus diesem Grund sollten Modifizierer sehr leistungsfähig sein, um Engpässe zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-165">For this reason, modifiers should be very performant to avoid bottlenecks.</span></span>  

<span data-ttu-id="fa6e9-166">Informationen zum Erstellen eines Modifikators finden [Sie in der Dokumentation zu Modifizierern](docs/_includes/popper-documentation.md#modifiers--object) .</span><span class="sxs-lookup"><span data-stu-id="fa6e9-166">To learn how to create a modifier, [read the modifiers documentation](docs/_includes/popper-documentation.md#modifiers--object)</span></span>


### <a name="react-vuejs-angular-angularjs-emberjs-etc-integration"></a><span data-ttu-id="fa6e9-167">"Reagiere", "Vue. js", "Kantig", "AngularJS", "Glut. js" (etc...)-Integration</span><span class="sxs-lookup"><span data-stu-id="fa6e9-167">React, Vue.js, Angular, AngularJS, Ember.js (etc...) integration</span></span>

<span data-ttu-id="fa6e9-168">Das Integrieren von Drittanbieterbibliotheken in Reaktions-oder anderen Bibliotheken kann ein Problem sein, da Sie das DOM normalerweise ändern und die Bibliotheken verrückt steuern.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-168">Integrating 3rd party libraries in React or other libraries can be a pain because they usually alter the DOM and drive the libraries crazy.</span></span>  
<span data-ttu-id="fa6e9-169">Popper. js schränkt alle DOM-Änderungen innerhalb des `applyStyle` Modifikators ein, Sie können ihn einfach deaktivieren und die Popper-Koordinaten mithilfe Ihrer Bibliothek von Choice manuell anwenden.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-169">Popper.js limits all its DOM modifications inside the `applyStyle` modifier, you can simply disable it and manually apply the popper coordinates using your library of choice.</span></span>  

<span data-ttu-id="fa6e9-170">Eine umfassende Liste der Bibliotheken, mit denen Sie Popper. js in vorhandenen Frameworks verwenden können, finden Sie auf der Seite [Erwähnungen](/MENTIONS.md) .</span><span class="sxs-lookup"><span data-stu-id="fa6e9-170">For a comprehensive list of libraries that let you use Popper.js into existing frameworks, visit the [MENTIONS](/MENTIONS.md) page.</span></span>

<span data-ttu-id="fa6e9-171">Alternativ können Sie Ihre eigenen `applyStyles` mit Ihrem benutzerdefinierten überschreiben und Popper. js selbst integrieren!</span><span class="sxs-lookup"><span data-stu-id="fa6e9-171">Alternatively, you may even override your own `applyStyles` with your custom one and integrate Popper.js by yourself!</span></span>

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

### <a name="migration-from-popperjs-v0"></a><span data-ttu-id="fa6e9-172">Migration von Popper. js v0</span><span class="sxs-lookup"><span data-stu-id="fa6e9-172">Migration from Popper.js v0</span></span>

<span data-ttu-id="fa6e9-173">Da die API geändert wurde, haben wir einige Migrations Anweisungen vorbereitet, um das Upgrade auf Popper. js V1 zu vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-173">Since the API changed, we prepared some migration instructions to make it easy to upgrade to Popper.js v1.</span></span>  

https://github.com/FezVrasta/popper.js/issues/62

<span data-ttu-id="fa6e9-174">Wenn Sie Fragen haben, können Sie sich in das Problem einkommentieren.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-174">Feel free to comment inside the issue if you have any questions.</span></span>

### <a name="performances"></a><span data-ttu-id="fa6e9-175">Leistungen</span><span class="sxs-lookup"><span data-stu-id="fa6e9-175">Performances</span></span>

<span data-ttu-id="fa6e9-176">Popper. js ist sehr leistungsfähig.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-176">Popper.js is very performant.</span></span> <span data-ttu-id="fa6e9-177">Normalerweise werden 0,5 ms benötigt, um die Position eines Popper zu berechnen (auf einem iMac mit 3,5 g GHz Intel Core i5).</span><span class="sxs-lookup"><span data-stu-id="fa6e9-177">It usually takes 0.5ms to compute a popper's position (on an iMac with 3.5G GHz Intel Core i5).</span></span>  
<span data-ttu-id="fa6e9-178">Dies bedeutet, dass keine [Jank](https://www.chromium.org/developers/how-tos/trace-event-profiling-tool/anatomy-of-jank)verursacht wird, was zu einem reibungslosen Benutzererlebnis führt.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-178">This means that it will not cause any [jank](https://www.chromium.org/developers/how-tos/trace-event-profiling-tool/anatomy-of-jank), leading to a smooth user experience.</span></span>

## <a name="notes"></a><span data-ttu-id="fa6e9-179">Notes</span><span class="sxs-lookup"><span data-stu-id="fa6e9-179">Notes</span></span>

### <a name="libraries-using-popperjs"></a><span data-ttu-id="fa6e9-180">Bibliotheken mit Popper. js</span><span class="sxs-lookup"><span data-stu-id="fa6e9-180">Libraries using Popper.js</span></span>

<span data-ttu-id="fa6e9-181">Das Ziel von Popper. js besteht darin, ein stabiles und leistungsfähiges Positioniermodul bereitzustellen, das in Bibliotheken von Drittanbietern verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-181">The aim of Popper.js is to provide a stable and powerful positioning engine ready to be used in 3rd party libraries.</span></span>  

<span data-ttu-id="fa6e9-182">Besuchen Sie die Seite [Erwähnungen](/MENTIONS.md) für eine aktualisierte Liste der Projekte.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-182">Visit the [MENTIONS](/MENTIONS.md) page for an updated list of projects.</span></span>


### <a name="credits"></a><span data-ttu-id="fa6e9-183">Mitwirkende</span><span class="sxs-lookup"><span data-stu-id="fa6e9-183">Credits</span></span>
<span data-ttu-id="fa6e9-184">Ich möchte einigen Freunden und Projekten für Ihre Arbeit danken:</span><span class="sxs-lookup"><span data-stu-id="fa6e9-184">I want to thank some friends and projects for the work they did:</span></span>

- <span data-ttu-id="fa6e9-185">[@AndreaScn](https://github.com/AndreaScn) für seine Arbeit auf der GitHub-Seite und die manuellen Tests, die er während der Entwicklung durchführte;</span><span class="sxs-lookup"><span data-stu-id="fa6e9-185">[@AndreaScn](https://github.com/AndreaScn) for his work on the GitHub Page and the manual testing he did during the development;</span></span>
- <span data-ttu-id="fa6e9-186">[@vampolo](https://github.com/vampolo) für die ursprüngliche Idee und für den Namen der Bibliothek;</span><span class="sxs-lookup"><span data-stu-id="fa6e9-186">[@vampolo](https://github.com/vampolo) for the original idea and for the name of the library;</span></span>
- <span data-ttu-id="fa6e9-187">[Sysdig](https://github.com/Draios) für all die tollen Dinge, die ich in diesen Jahren gelernt habe, die es mir ermöglicht haben, diese Bibliothek zu schreiben;</span><span class="sxs-lookup"><span data-stu-id="fa6e9-187">[Sysdig](https://github.com/Draios) for all the awesome things I learned during these years that made it possible for me to write this library;</span></span>
- <span data-ttu-id="fa6e9-188">[Tether. js](http://github.hubspot.com/tether/) , weil ich mich beim Schreiben einer Positionier Bibliothek für die reale Welt inspiriert habe;</span><span class="sxs-lookup"><span data-stu-id="fa6e9-188">[Tether.js](http://github.hubspot.com/tether/) for having inspired me in writing a positioning library ready for the real world;</span></span>
- <span data-ttu-id="fa6e9-189">[Die Mitwirkenden](https://github.com/FezVrasta/popper.js/graphs/contributors) für Ihre sehr geschätzten Pull-Anforderungen und Fehlerberichte;</span><span class="sxs-lookup"><span data-stu-id="fa6e9-189">[The Contributors](https://github.com/FezVrasta/popper.js/graphs/contributors) for their much appreciated Pull Requests and bug reports;</span></span>
- <span data-ttu-id="fa6e9-190">**Sie** für den Stern, den Sie diesem Projekt geben werden, und dass er so genial ist, um diesem Projekt einen Versuch zu geben🙂</span><span class="sxs-lookup"><span data-stu-id="fa6e9-190">**you** for the star you'll give to this project and for being so awesome to give this project a try 🙂</span></span>

### <a name="copyright-and-license"></a><span data-ttu-id="fa6e9-191">Copyright und Lizenz</span><span class="sxs-lookup"><span data-stu-id="fa6e9-191">Copyright and license</span></span>
<span data-ttu-id="fa6e9-192">Code und Dokumentation Copyright 2016 **Federico Zivolo**.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-192">Code and documentation copyright 2016 **Federico Zivolo**.</span></span> <span data-ttu-id="fa6e9-193">Code, der unter der [mit-Lizenz](LICENSE.md)veröffentlicht wurde.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-193">Code released under the [MIT license](LICENSE.md).</span></span> <span data-ttu-id="fa6e9-194">Dokumente werden unter Creative Commons veröffentlicht.</span><span class="sxs-lookup"><span data-stu-id="fa6e9-194">Docs released under Creative Commons.</span></span>
