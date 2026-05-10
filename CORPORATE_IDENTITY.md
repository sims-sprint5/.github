
# CANVIS D'IDENTITAT CORPORATIVA

Durant el procès de creació del software, vam demanar a l'equip d'ESARDI que ens creés una identitat corporativa, decidint colors, font de text i logotip.

A l'Sprint 5 ens van proporcionar el full d'estils i a l'Sprint 6 l'hem aplicat.

## Procès

### style.css

Per poder aplicar aquesta nova identitat corporativa hem fet canvis en l'arquitectura, ja que teniem els colors i estils hardcoded als documents.
Hem centralitzat els estils en un document style.css, on hem definit totes les variables CSS dels dos temes que hem decidit utiltizar( Clar i Fosc), per a
que els colors s'adapten automaticament.

També hem establert regles globals de tipografia, com la font "switzer" i estils base per als elements HTML.

### Tailwind.css

Com utilitzem Tailwind, hem modificar l'arxiu tailwind.config.js, que serveix com a "pont" entre les variables definides al arxiu style.css i Taliwind CSS.
Aquí hem definit les classes amb les variables que volem que utilitzi cadascuna.


### Aplicació de les classes als elements

Per últim hem canviat els estils hardcoded a les classes dels elements per les noves classes.


## Canvis personals

Per millorar l'apartat visual hem afegir hovers als botons, per fer-los més grans o afegir un petit canvi de color.
Hem afegir blur o transparència als login o register.
I per últim, com hem mencionat anteriorment, hem decidit afegir un switch per canviar entre el mode clar i fosc, per millorar l'experiència dels usuaris.

