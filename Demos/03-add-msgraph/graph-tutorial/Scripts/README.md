<!-- IGNORE THE HTML BLOCK BELOW, THE INTERESTING PART IS AFTER IT -->

<h1 align="center"><span data-ttu-id="db835-101">Popper. js</span><span class="sxs-lookup"><span data-stu-id="db835-101">Popper.js</span></span></h1>

<p align="center"><span data-ttu-id="db835-102">
    <strong>Eine Bibliothek zur Positionierung von Poppers in Webanwendungen.</strong>
</span><span class="sxs-lookup"><span data-stu-id="db835-102">
    <strong>A library used to position poppers in web applications.</strong>
</span></span></p>

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

## <a name="wut-poppers"></a><span data-ttu-id="db835-103">Wut?</span><span class="sxs-lookup"><span data-stu-id="db835-103">Wut?</span></span> <span data-ttu-id="db835-104">Poppers?</span><span class="sxs-lookup"><span data-stu-id="db835-104">Poppers?</span></span>

<span data-ttu-id="db835-105">Ein Popper ist ein Element auf dem Bildschirm, das aus dem natürlichen Fluss der Anwendung ausgeht.</span><span class="sxs-lookup"><span data-stu-id="db835-105">A popper is an element on the screen which "pops out" from the natural flow of your application.</span></span>  
<span data-ttu-id="db835-106">Häufige Beispiele für Poppers sind QuickInfos, popovers und Dropdowns.</span><span class="sxs-lookup"><span data-stu-id="db835-106">Common examples of poppers are tooltips, popovers and drop-downs.</span></span>


## <a name="so-yet-another-tooltip-library"></a><span data-ttu-id="db835-107">Eine weitere ToolTip-Bibliothek?</span><span class="sxs-lookup"><span data-stu-id="db835-107">So, yet another tooltip library?</span></span>

<span data-ttu-id="db835-108">Nun, im Grunde genommen **Nein**.</span><span class="sxs-lookup"><span data-stu-id="db835-108">Well, basically, **no**.</span></span>  
<span data-ttu-id="db835-109">Popper. js ist ein **Positioniermodul**, dessen Zweck es ist, die Position eines Elements zu berechnen, um es in der Nähe eines bestimmten Reference-Elements positionieren zu können.</span><span class="sxs-lookup"><span data-stu-id="db835-109">Popper.js is a **positioning engine**, its purpose is to calculate the position of an element to make it possible to position it near a given reference element.</span></span>  

<span data-ttu-id="db835-110">Das Modul ist vollständig modular aufgebaut, und die meisten seiner Features werden als **Modifizierer** implementiert (ähnlich wie bei Middleware oder Plugins).</span><span class="sxs-lookup"><span data-stu-id="db835-110">The engine is completely modular and most of its features are implemented as **modifiers** (similar to middlewares or plugins).</span></span>  
<span data-ttu-id="db835-111">Die gesamte CodeBase ist in ES2015 geschrieben, und ihre Features werden dank [SauceLabs](https://saucelabs.com/) und [TravisCI](https://travis-ci.org/)automatisch auf echten Browsern getestet.</span><span class="sxs-lookup"><span data-stu-id="db835-111">The whole code base is written in ES2015 and its features are automatically tested on real browsers thanks to [SauceLabs](https://saucelabs.com/) and [TravisCI](https://travis-ci.org/).</span></span>

<span data-ttu-id="db835-112">Popper. js hat keine Abhängigkeiten.</span><span class="sxs-lookup"><span data-stu-id="db835-112">Popper.js has zero dependencies.</span></span> <span data-ttu-id="db835-113">Keine jQuery, keine LoDash, nichts.</span><span class="sxs-lookup"><span data-stu-id="db835-113">No jQuery, no LoDash, nothing.</span></span>  
<span data-ttu-id="db835-114">Sie wird von großen Unternehmen wie [Twitter in Bootstrap V4](https://getbootstrap.com/), [Microsoft in webclipper](https://github.com/OneNoteDev/WebClipper) und [Atlassian in AtlasKit](https://aui-cdn.atlassian.com/atlaskit/registry/)verwendet.</span><span class="sxs-lookup"><span data-stu-id="db835-114">It's used by big companies like [Twitter in Bootstrap v4](https://getbootstrap.com/), [Microsoft in WebClipper](https://github.com/OneNoteDev/WebClipper) and [Atlassian in AtlasKit](https://aui-cdn.atlassian.com/atlaskit/registry/).</span></span>

### <a name="popperjs"></a><span data-ttu-id="db835-115">Popper. js</span><span class="sxs-lookup"><span data-stu-id="db835-115">Popper.js</span></span>

<span data-ttu-id="db835-116">Hierbei handelt es sich um das Modul, die Bibliothek, die die Formatvorlagen für die Poppers berechnet und optional anwendet.</span><span class="sxs-lookup"><span data-stu-id="db835-116">This is the engine, the library that computes and, optionally, applies the styles to the poppers.</span></span>

<span data-ttu-id="db835-117">Einige der wichtigsten Punkte sind:</span><span class="sxs-lookup"><span data-stu-id="db835-117">Some of the key points are:</span></span>

- <span data-ttu-id="db835-118">Positions Elemente, die Sie in Ihrem ursprünglichen Dom-Kontext halten (nicht mit Ihrem DOM!);</span><span class="sxs-lookup"><span data-stu-id="db835-118">Position elements keeping them in their original DOM context (doesn't mess with your DOM!);</span></span>
- <span data-ttu-id="db835-119">Ermöglicht den Export der berechneten Informationen, um Sie in Reaktions-und andere Ansichts Bibliotheken zu integrieren;</span><span class="sxs-lookup"><span data-stu-id="db835-119">Allows to export the computed informations to integrate with React and other view libraries;</span></span>
- <span data-ttu-id="db835-120">Unterstützt Schatten-DOM-Elemente;</span><span class="sxs-lookup"><span data-stu-id="db835-120">Supports Shadow DOM elements;</span></span>
- <span data-ttu-id="db835-121">Vollständig anpassbar dank der Modifiers-basierten Struktur;</span><span class="sxs-lookup"><span data-stu-id="db835-121">Completely customizable thanks to the modifiers based structure;</span></span>

<span data-ttu-id="db835-122">Besuchen Sie unsere [Projektseite](https://fezvrasta.github.io/popper.js) , um eine Vielzahl von Beispielen dazu zu erhalten, was Sie mit Popper. js tun können!</span><span class="sxs-lookup"><span data-stu-id="db835-122">Visit our [project page](https://fezvrasta.github.io/popper.js) to see a lot of examples of what you can do with Popper.js!</span></span>

<span data-ttu-id="db835-123">Hier finden Sie [die Dokumentation](/docs/_includes/popper-documentation.md).</span><span class="sxs-lookup"><span data-stu-id="db835-123">Find [the documentation here](/docs/_includes/popper-documentation.md).</span></span>


### <a name="tooltipjs"></a><span data-ttu-id="db835-124">ToolTip. js</span><span class="sxs-lookup"><span data-stu-id="db835-124">Tooltip.js</span></span>

<span data-ttu-id="db835-125">Da viele Benutzer einfach eine einfache Möglichkeit benötigen, leistungsstarke QuickInfos in Ihre Projekte zu integrieren, haben wir **ToolTip. js**erstellt.</span><span class="sxs-lookup"><span data-stu-id="db835-125">Since lots of users just need a simple way to integrate powerful tooltips in their projects, we created **Tooltip.js**.</span></span>  
<span data-ttu-id="db835-126">Es handelt sich um eine kleine Bibliothek, die die automatische Erstellung von QuickInfos mit AS Engine Popper. js ermöglicht.</span><span class="sxs-lookup"><span data-stu-id="db835-126">It's a small library that makes it easy to automatically create tooltips using as engine Popper.js.</span></span>  
<span data-ttu-id="db835-127">Die API ist fast identisch mit dem bekannten ToolTip-System von Bootstrap, sodass es einfach ist, es in Ihre Projekte zu integrieren.</span><span class="sxs-lookup"><span data-stu-id="db835-127">Its API is almost identical to the famous tooltip system of Bootstrap, in this way it will be easy to integrate it in your projects.</span></span>  
<span data-ttu-id="db835-128">Die QuickInfos, die von ToolTip. js generiert werden, sind `aria` Dank der Tags zugänglich.</span><span class="sxs-lookup"><span data-stu-id="db835-128">The tooltips generated by Tooltip.js are accessible thanks to the `aria` tags.</span></span>

<span data-ttu-id="db835-129">Hier finden Sie [die Dokumentation](/docs/_includes/tooltip-documentation.md).</span><span class="sxs-lookup"><span data-stu-id="db835-129">Find [the documentation here](/docs/_includes/tooltip-documentation.md).</span></span>


## <a name="installation"></a><span data-ttu-id="db835-130">Installation</span><span class="sxs-lookup"><span data-stu-id="db835-130">Installation</span></span>
<span data-ttu-id="db835-131">Popper. js ist auf den folgenden Paketmanagern und CDNs verfügbar:</span><span class="sxs-lookup"><span data-stu-id="db835-131">Popper.js is available on the following package managers and CDNs:</span></span>

| <span data-ttu-id="db835-132">Quelle</span><span class="sxs-lookup"><span data-stu-id="db835-132">Source</span></span> |                                                                                  |
|:-------|:---------------------------------------------------------------------------------|
| <span data-ttu-id="db835-133">npm</span><span class="sxs-lookup"><span data-stu-id="db835-133">npm</span></span>    | `npm install popper.js --save`                                                   |
| <span data-ttu-id="db835-134">Garn</span><span class="sxs-lookup"><span data-stu-id="db835-134">yarn</span></span>   | `yarn add popper.js`                                                             |
| <span data-ttu-id="db835-135">NuGet</span><span class="sxs-lookup"><span data-stu-id="db835-135">NuGet</span></span>  | `PM> Install-Package popper.js`                                                  |
| <span data-ttu-id="db835-136">Bower</span><span class="sxs-lookup"><span data-stu-id="db835-136">Bower</span></span>  | `bower install popper.js --save`                     |
| <span data-ttu-id="db835-137">unpkg</span><span class="sxs-lookup"><span data-stu-id="db835-137">unpkg</span></span>  | [`https://unpkg.com/popper.js`](https://unpkg.com/popper.js)                     |
| <span data-ttu-id="db835-138">cdnjs</span><span class="sxs-lookup"><span data-stu-id="db835-138">cdnjs</span></span>  | [`https://cdnjs.com/libraries/popper.js`](https://cdnjs.com/libraries/popper.js) |

<span data-ttu-id="db835-139">ToolTip. js:</span><span class="sxs-lookup"><span data-stu-id="db835-139">Tooltip.js as well:</span></span>

| <span data-ttu-id="db835-140">Quelle</span><span class="sxs-lookup"><span data-stu-id="db835-140">Source</span></span> |                                                                                  |
|:-------|:---------------------------------------------------------------------------------|
| <span data-ttu-id="db835-141">npm</span><span class="sxs-lookup"><span data-stu-id="db835-141">npm</span></span>    | `npm install tooltip.js --save`                                                  |
| <span data-ttu-id="db835-142">Garn</span><span class="sxs-lookup"><span data-stu-id="db835-142">yarn</span></span>   | `yarn add tooltip.js`                                                            |
| <span data-ttu-id="db835-143">Bower</span><span class="sxs-lookup"><span data-stu-id="db835-143">Bower\*</span></span> | `bower install tooltip.js=https://unpkg.com/tooltip.js --save`                   |
| <span data-ttu-id="db835-144">unpkg</span><span class="sxs-lookup"><span data-stu-id="db835-144">unpkg</span></span>  | [`https://unpkg.com/tooltip.js`](https://unpkg.com/tooltip.js)                   |
| <span data-ttu-id="db835-145">cdnjs</span><span class="sxs-lookup"><span data-stu-id="db835-145">cdnjs</span></span>  | [`https://cdnjs.com/libraries/popper.js`](https://cdnjs.com/libraries/popper.js) |

<span data-ttu-id="db835-146">\*: Bower wird nicht offiziell unterstützt, es kann verwendet werden, um ToolTip. js nur über das unpkg.com CDN zu installieren.</span><span class="sxs-lookup"><span data-stu-id="db835-146">\*: Bower isn't officially supported, it can be used to install Tooltip.js only trough the unpkg.com CDN.</span></span> <span data-ttu-id="db835-147">Diese Methode hat die Einschränkung, dass keine bestimmte Version der Bibliothek definiert werden kann.</span><span class="sxs-lookup"><span data-stu-id="db835-147">This method has the limitation of not being able to define a specific version of the library.</span></span> <span data-ttu-id="db835-148">Bower und Popper. js schlagen vor, NPM oder Garn für Ihre Projekte zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="db835-148">Bower and Popper.js suggests to use npm or Yarn for your projects.</span></span>  
<span data-ttu-id="db835-149">Weitere Informationen finden [Sie im zugehörigen Thema](https://github.com/FezVrasta/popper.js/issues/390).</span><span class="sxs-lookup"><span data-stu-id="db835-149">For more info, [read the related issue](https://github.com/FezVrasta/popper.js/issues/390).</span></span>

### <a name="dist-targets"></a><span data-ttu-id="db835-150">Dist-Ziele</span><span class="sxs-lookup"><span data-stu-id="db835-150">Dist targets</span></span>

<span data-ttu-id="db835-151">Popper. js ist derzeit mit 3 Zielen im Kopf: UMD, ESM und ESNext ausgeliefert.</span><span class="sxs-lookup"><span data-stu-id="db835-151">Popper.js is currently shipped with 3 targets in mind: UMD, ESM and ESNext.</span></span>

- <span data-ttu-id="db835-152">UMD-Universal Modul Definition: AMD, RequireJS und Globals;</span><span class="sxs-lookup"><span data-stu-id="db835-152">UMD - Universal Module Definition: AMD, RequireJS and globals;</span></span>
- <span data-ttu-id="db835-153">ESM-es-Module: für WebPack/Rollup oder Browser, der die Spezifikation unterstützt;</span><span class="sxs-lookup"><span data-stu-id="db835-153">ESM - ES Modules: For webpack/Rollup or browser supporting the spec;</span></span>
- <span data-ttu-id="db835-154">ESNext: verfügbar in `dist/`, kann mit WebPack verwendet werden und `babel-preset-env`;</span><span class="sxs-lookup"><span data-stu-id="db835-154">ESNext: Available in `dist/`, can be used with webpack and `babel-preset-env`;</span></span>

<span data-ttu-id="db835-155">Stellen Sie sicher, dass Sie die richtige für Ihre Anforderungen verwenden.</span><span class="sxs-lookup"><span data-stu-id="db835-155">Make sure to use the right one for your needs.</span></span> <span data-ttu-id="db835-156">Wenn Sie es mit einem `<script>` Tag importieren möchten, verwenden Sie UMD.</span><span class="sxs-lookup"><span data-stu-id="db835-156">If you want to import it with a `<script>` tag, use UMD.</span></span>

## <a name="usage"></a><span data-ttu-id="db835-157">Verwendung</span><span class="sxs-lookup"><span data-stu-id="db835-157">Usage</span></span>

<span data-ttu-id="db835-158">Wenn ein Popper-DOM-Knoten vorhanden ist, bitten Sie Popper. js, ihn in der Nähe seiner Schaltfläche zu positionieren.</span><span class="sxs-lookup"><span data-stu-id="db835-158">Given an existing popper DOM node, ask Popper.js to position it near its button</span></span>

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

### <a name="callbacks"></a><span data-ttu-id="db835-159">Rückrufe</span><span class="sxs-lookup"><span data-stu-id="db835-159">Callbacks</span></span>

<span data-ttu-id="db835-160">Popper. js unterstützt zwei Arten von Rückrufen, `onCreate` der Rückruf wird aufgerufen, nachdem der Popper initalized wurde.</span><span class="sxs-lookup"><span data-stu-id="db835-160">Popper.js supports two kinds of callbacks, the `onCreate` callback is called after the popper has been initalized.</span></span> <span data-ttu-id="db835-161">Die `onUpdate` eine wird bei jeder nachfolgenden Aktualisierung aufgerufen.</span><span class="sxs-lookup"><span data-stu-id="db835-161">The `onUpdate` one is called on any subsequent update.</span></span>

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

### <a name="writing-your-own-modifiers"></a><span data-ttu-id="db835-162">Schreiben eigener Modifizierer</span><span class="sxs-lookup"><span data-stu-id="db835-162">Writing your own modifiers</span></span>

<span data-ttu-id="db835-163">Popper. js basiert auf einer "Plugin-artigen" Architektur, die meisten seiner Features sind vollständig gekapselte "Modifiers".</span><span class="sxs-lookup"><span data-stu-id="db835-163">Popper.js is based on a "plugin-like" architecture, most of its features are fully encapsulated "modifiers".</span></span>  
<span data-ttu-id="db835-164">Ein Modifizierer ist eine Funktion, die aufgerufen wird, wenn Popper. js die Position des Popper berechnen muss.</span><span class="sxs-lookup"><span data-stu-id="db835-164">A modifier is a function that is called each time Popper.js needs to compute the position of the popper.</span></span> <span data-ttu-id="db835-165">Aus diesem Grund sollten Modifikatoren sehr leistungsfähig sein, um Engpässe zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="db835-165">For this reason, modifiers should be very performant to avoid bottlenecks.</span></span>  

<span data-ttu-id="db835-166">Weitere Informationen zum Erstellen eines Modifizierers finden [Sie in der Dokumentation](docs/_includes/popper-documentation.md#modifiers--object) zur Modifiers.</span><span class="sxs-lookup"><span data-stu-id="db835-166">To learn how to create a modifier, [read the modifiers documentation](docs/_includes/popper-documentation.md#modifiers--object)</span></span>


### <a name="react-vuejs-angular-angularjs-emberjs-etc-integration"></a><span data-ttu-id="db835-167">Reagiere, vue. js, eckig, AngularJS, glut. js (etc...) Integration</span><span class="sxs-lookup"><span data-stu-id="db835-167">React, Vue.js, Angular, AngularJS, Ember.js (etc...) integration</span></span>

<span data-ttu-id="db835-168">Die Integration von Drittanbieterbibliotheken in reagieren oder anderen Bibliotheken kann schmerzhaft sein, da Sie in der Regel das DOM ändern und die Bibliotheken verrückt fahren.</span><span class="sxs-lookup"><span data-stu-id="db835-168">Integrating 3rd party libraries in React or other libraries can be a pain because they usually alter the DOM and drive the libraries crazy.</span></span>  
<span data-ttu-id="db835-169">Popper. js schränkt alle seine DOM-Modifikationen `applyStyle` innerhalb des Modifizierers ein, Sie können es einfach deaktivieren und die Popper-Koordinaten mithilfe Ihrer bevorzugten Bibliothek manuell anwenden.</span><span class="sxs-lookup"><span data-stu-id="db835-169">Popper.js limits all its DOM modifications inside the `applyStyle` modifier, you can simply disable it and manually apply the popper coordinates using your library of choice.</span></span>  

<span data-ttu-id="db835-170">Eine umfassende Liste der Bibliotheken, mit denen Sie Popper. js in vorhandene Frameworks verwenden können, finden [](/MENTIONS.md) Sie auf der Seite Erwähnungen.</span><span class="sxs-lookup"><span data-stu-id="db835-170">For a comprehensive list of libraries that let you use Popper.js into existing frameworks, visit the [MENTIONS](/MENTIONS.md) page.</span></span>

<span data-ttu-id="db835-171">Alternativ können Sie Ihre eigenen Benutzer mit Ihrem `applyStyles` eigenen überschreiben und Popper. js selbst integrieren!</span><span class="sxs-lookup"><span data-stu-id="db835-171">Alternatively, you may even override your own `applyStyles` with your custom one and integrate Popper.js by yourself!</span></span>

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

### <a name="migration-from-popperjs-v0"></a><span data-ttu-id="db835-172">Migration von Popper. js v0</span><span class="sxs-lookup"><span data-stu-id="db835-172">Migration from Popper.js v0</span></span>

<span data-ttu-id="db835-173">Da sich die API geändert hat, haben wir einige Migrations Anweisungen vorbereitet, um das Upgrade auf Popper. js V1 zu vereinfachen.</span><span class="sxs-lookup"><span data-stu-id="db835-173">Since the API changed, we prepared some migration instructions to make it easy to upgrade to Popper.js v1.</span></span>  

https://github.com/FezVrasta/popper.js/issues/62

<span data-ttu-id="db835-174">Wenn Sie Fragen haben, können Sie das Problem in diesem Fall kommentieren.</span><span class="sxs-lookup"><span data-stu-id="db835-174">Feel free to comment inside the issue if you have any questions.</span></span>

### <a name="performances"></a><span data-ttu-id="db835-175">Leistungen</span><span class="sxs-lookup"><span data-stu-id="db835-175">Performances</span></span>

<span data-ttu-id="db835-176">Popper. js ist sehr leistungsfähig.</span><span class="sxs-lookup"><span data-stu-id="db835-176">Popper.js is very performant.</span></span> <span data-ttu-id="db835-177">In der Regel dauert es 0,5 Millisekunden, bis die Position eines Popper berechnet wird (auf einem iMac mit Intel Core i5 mit 3,5 g GHz).</span><span class="sxs-lookup"><span data-stu-id="db835-177">It usually takes 0.5ms to compute a popper's position (on an iMac with 3.5G GHz Intel Core i5).</span></span>  
<span data-ttu-id="db835-178">Dies führt dazu, dass keine [Jank](https://www.chromium.org/developers/how-tos/trace-event-profiling-tool/anatomy-of-jank)verursacht wird, was zu einer reibungslosen Benutzerfreundlichkeit führen kann.</span><span class="sxs-lookup"><span data-stu-id="db835-178">This means that it will not cause any [jank](https://www.chromium.org/developers/how-tos/trace-event-profiling-tool/anatomy-of-jank), leading to a smooth user experience.</span></span>

## <a name="notes"></a><span data-ttu-id="db835-179">Hinweise</span><span class="sxs-lookup"><span data-stu-id="db835-179">Notes</span></span>

### <a name="libraries-using-popperjs"></a><span data-ttu-id="db835-180">Bibliotheken mit Popper. js</span><span class="sxs-lookup"><span data-stu-id="db835-180">Libraries using Popper.js</span></span>

<span data-ttu-id="db835-181">Das Ziel von Popper. js ist es, ein stabiles und leistungsstarkes Positioniermodul bereitzustellen, das in Bibliotheken von Drittanbietern verwendet werden kann.</span><span class="sxs-lookup"><span data-stu-id="db835-181">The aim of Popper.js is to provide a stable and powerful positioning engine ready to be used in 3rd party libraries.</span></span>  

<span data-ttu-id="db835-182">Besuchen Sie [](/MENTIONS.md) die Seite Erwähnungen für eine aktualisierte Liste der Projekte.</span><span class="sxs-lookup"><span data-stu-id="db835-182">Visit the [MENTIONS](/MENTIONS.md) page for an updated list of projects.</span></span>


### <a name="credits"></a><span data-ttu-id="db835-183">Mitwirkende</span><span class="sxs-lookup"><span data-stu-id="db835-183">Credits</span></span>
<span data-ttu-id="db835-184">Ich möchte einigen Freunden und Projekten für die Arbeit danken, die Sie getan haben:</span><span class="sxs-lookup"><span data-stu-id="db835-184">I want to thank some friends and projects for the work they did:</span></span>

- <span data-ttu-id="db835-185">[@AndreaScn](https://github.com/AndreaScn) für seine Arbeit auf der GitHub-Seite und die manuellen Tests, die er während der Entwicklung durchgeführt hat;</span><span class="sxs-lookup"><span data-stu-id="db835-185">[@AndreaScn](https://github.com/AndreaScn) for his work on the GitHub Page and the manual testing he did during the development;</span></span>
- <span data-ttu-id="db835-186">[@vampolo](https://github.com/vampolo) für die ursprüngliche Idee und für den Namen der Bibliothek;</span><span class="sxs-lookup"><span data-stu-id="db835-186">[@vampolo](https://github.com/vampolo) for the original idea and for the name of the library;</span></span>
- <span data-ttu-id="db835-187">[Sysdig](https://github.com/Draios) für all die tollen Dinge, die ich in diesen Jahren gelernt habe, die es mir ermöglicht haben, diese Bibliothek zu schreiben;</span><span class="sxs-lookup"><span data-stu-id="db835-187">[Sysdig](https://github.com/Draios) for all the awesome things I learned during these years that made it possible for me to write this library;</span></span>
- <span data-ttu-id="db835-188">[Tether. js](http://github.hubspot.com/tether/) , dass Sie mich dazu inspiriert haben, eine Positionierungs Bibliothek für die reale Welt zu schreiben.</span><span class="sxs-lookup"><span data-stu-id="db835-188">[Tether.js](http://github.hubspot.com/tether/) for having inspired me in writing a positioning library ready for the real world;</span></span>
- <span data-ttu-id="db835-189">[Die Mitwirkenden](https://github.com/FezVrasta/popper.js/graphs/contributors) für Ihre sehr geschätzten Pull-Anforderungen und Fehlerberichte;</span><span class="sxs-lookup"><span data-stu-id="db835-189">[The Contributors](https://github.com/FezVrasta/popper.js/graphs/contributors) for their much appreciated Pull Requests and bug reports;</span></span>
- <span data-ttu-id="db835-190">**Sie** für den Stern, den Sie diesem Projekt geben werden, und dafür, dass Sie so genial sind, um diesem Projekt einen Versuch zu geben🙂</span><span class="sxs-lookup"><span data-stu-id="db835-190">**you** for the star you'll give to this project and for being so awesome to give this project a try 🙂</span></span>

### <a name="copyright-and-license"></a><span data-ttu-id="db835-191">Urheberrecht und Lizenz</span><span class="sxs-lookup"><span data-stu-id="db835-191">Copyright and license</span></span>
<span data-ttu-id="db835-192">Code und Dokumentation Copyright 2016 **Federico Zivolo**.</span><span class="sxs-lookup"><span data-stu-id="db835-192">Code and documentation copyright 2016 **Federico Zivolo**.</span></span> <span data-ttu-id="db835-193">Code, der unter der [mit-Lizenz](LICENSE.md)veröffentlicht wurde.</span><span class="sxs-lookup"><span data-stu-id="db835-193">Code released under the [MIT license](LICENSE.md).</span></span> <span data-ttu-id="db835-194">Dokumente, die unter Creative Commons veröffentlicht wurden.</span><span class="sxs-lookup"><span data-stu-id="db835-194">Docs released under Creative Commons.</span></span>
