---
name: write-like-me
description: Schrijft of herschrijft communicatie in mijn stem, los, direct en collegiaal, standaard in Vlaams Nederlands.
disable-model-invocation: true
---

Schrijf standaard in Vlaams Nederlands, tenzij een andere taal gevraagd wordt. Pas lengte en vorm aan ontvanger en kanaal aan; de stem blijft dezelfde.

## Toon
- Collegiaal en los, zoals tegen een collega op kantoor: `je`, `we`, `ik`. Gebruik `u` alleen waar dat formeel verwacht wordt.
- Verzorgd maar niet stijf: geen aanhef- of slotformules, opgeklopte taal of beleefdheid als opvulling. Korte reacties mogen kort blijven: `merci!`, `top, lgtm`.
- Feiten stellig, voorstellen suggestief. Zwak vermoedens af met `lijkt me`, `denk ik`, `vermoed ik` of `precies`, maar niet elke zin.

## Opbouw
- Begin bij hoe het nu zit, dan het gevolg, dan het voorstel: "Op dit moment … Hierdoor … Beter kunnen we …". Geef kort het waarom.
- Wat ik deed, schrijf ik in de ik-vorm en de verleden tijd: "ik pastte … aan", "ik haalde … weg", "ik keek de code na en merkte nog een paar dingen op".
- Stel twijfels en voorstellen als echte vragen: "Die logica mag eruit lijkt me?", "Kunnen we hier niet beter …?", "Of mis ik iets?", "Wat denk je?"

## Woorden
- Gewone spreektaal met Vlaamse kleur waar die natuurlijk valt: `eens`, `gewoon`, `wel`, `nog`, `even`, `sowieso`, `een pak`, `moest(en)` als voorwaarde.
- Gebruik code- en domeintermen zoals ze in de code heten (`selectedRows`, source of truth, checkboxes). Verzin er geen Nederlandse vertalingen voor, maar stapel ook geen functienamen op.
- Gangbare afkortingen mogen: `ipv`, `bv.`, `ivm`, `idd`. Schrijf aantallen in cijfers (`1 manier`, `2 calls`) en een overgang met een pijl (`zod -> valibot`).
- Wees concreet: noem rol, pagina, bestand, commit of link in plaats van "sommige gebruikers" of "daar".

## Vorm
- Een korte reactie bestaat uit doorlopende zinnen, liefst maximaal 3 regels. Meerdere bevindingen of wijzigingen worden korte bullets met telkens wat, waar en waarom.
- Schrijf volle zinnen, geen telegramstijl. Gebruik geen kopjes, vet of samenvatting tenzij de vorm erom vraagt, en hooguit af en toe een ✅ of 🙂.
- Elke zin moet maar op 1 manier te lezen zijn. Zou iemand moeten terugvragen wat je bedoelt, maak het dan concreter. Schrap herhaling: liever compact dan volledig.

## Zo klinkt het
> Code nagekeken en functioneel ziet dit er goed uit. Top dat je hiervoor een aparte command en handler gebruikt. Ik verwacht precies nog wel wat tests voor deze nieuwe classes?

> Op dit moment nesten we alle vue pagina's in `#tw-reset` om tailwind af te scheiden van de legacy css. Dat werkt, maar vraagt extra setup voor modals en popovers. Met `@scope` kan dat eenvoudiger.

> Hier kan je gewoon de variabele gebruiken die je iets hoger aanmaakt. Verder lgtm.
