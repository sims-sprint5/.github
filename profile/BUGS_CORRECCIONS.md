# Documentació de Testing i Millores

---

## Llista de Bugs

| # | Bug | Estat |
|---|-----|-------|
| 1 | Error visual a l'apartat de configuració: els requadres de text no són visibles en mode fosc del navegador | ✅ Arreglat |
| 2 | Al mapa, en prémer un vehicle apareix el botó de "cancelar reserva" tot i no haver reservat cap vehicle | ✅ Arreglat |
| 3 | Dintre de la rodona del geofencing, es pot clicar a qualsevol lloc i apareix un petit requadre amb informació del vehicle | ⚠️ Parcial |
| 4 | Alguns missatges d'error no estan ben traduïts | ✅ Arreglat |
| 5 | Quan es crea una reserva, el vehicle no apareix com a reservat i permet emplenar el formulari de forma infinita | ✅ Arreglat |
| 6 | La icona de "Mark" a l'apartat de geofencing no carrega | ✅ Arreglat |
| 7 | Si s'envia un missatge de suport llarg, la interfície es trenca | ✅ Arreglat |

### Detall dels bugs

**Bug 1 — Error visual en mode fosc**
Els requadres de text a l'apartat de configuració no eren visibles en mode fosc del navegador.

ABANS:
![Bug 1.1](img/testing/image1.png)

ARA:
![Bug 1](img/fixed/image1.png)

---

**Bug 2 — Botó "cancelar reserva" al mapa**
Al mapa, en prémer un vehicle apareixia el botó de "cancelar reserva" tot i no haver fet cap reserva. Aquest botó ja no apareix.

---

**Bug 3 — Rodona del geofencing (parcial)**
Dintre de la rodona del geofencing es pot clicar a qualsevol lloc i apareix un petit requadre amb informació del vehicle. Aquest comportament està parcialment resolt.

---

**Bug 4 — Missatges d'error no traduïts**
Alguns missatges d'error no estaven ben traduïts. Tots els missatges d'error han estat traduïts correctament.

---

**Bug 5 — Vehicle no apareix com a reservat**
Quan es creava una reserva, el vehicle no apareixia com a reservat i permetia emplenar el formulari de reserva de forma infinita. Ara el vehicle apareix correctament com a reservat un cop es confirma la reserva.

---

**Bug 6 — Icona "Mark" al geofencing**
La icona de "Mark" a l'apartat de geofencing no carregava. Era un error d'una imatge que no havia d'existir.

![Bug 6](img/testing/image2.png)

---

**Bug 7 — Interfície trencada amb missatges llargs**
Si s'enviava un missatge de suport llarg, la interfície es trencava.

ABANS:
![Bug 7.1](img/testing/image3.png)

ARA:
![Bug 7](img/fixed/image3.png)

---

## Llista de Funcionalitats No Implementades (inicial) i Estat Actual

| Funcionalitat | Estat inicial | Estat actual |
|---------------|--------------|--------------|
| Sistema de reserves | Tenien placeholders, faltava el backend | ✅ Implementat |
| Chatbot | No implementat | ✅ Implementat |
| Seeders | No implementats | ⚠️ Pendent |

**Sistema de reserves:** Hem implementat el sistema de reserves, ja que és una funcionalitat bàsica per a l'aplicació. Ara els usuaris poden reservar vehicles i els administradors poden veure les reserves realitzades.

**Chatbot:** Hem implementat el chatbot, que permet als usuaris enviar missatges de suport i rebre respostes automàtiques. Això millora l'experiència de l'usuari i facilita la comunicació amb el suport tècnic.

---

## Llista de Millores d'Usabilitat

| Millora | Estat |
|---------|-------|
| Mode fosc, ja que solucionava l'error del text no visible | ✅ Implementat |
| Al mapa, mostrar només els vehicles disponibles en el moment (o el vehicle reservat si s'està fent una reserva) | ✅ Implementat |
| Amagar el botó de reservar si el vehicle no està disponible | ✅ Implementat |
| Al registre, millorar els caràcters obligatoris al camp de contrasenya (majúscules i números) | ✅ Implementat |

---

## ADMIN

| Ítem | Estat |
|------|-------|
| En el formulari de crear nou vehicle, l'administrador ha de posar manualment la ubicació i latitud. Seria més senzill poder seleccionar-la directament al mapa. El mateix passa amb el camp "color del vehicle". | ⚠️ Pendent |
| En iniciar sessió com a administrador, aquest veu també l'apartat de client (pot reservar vehicles, enviar tickets de suport a ell mateix). Cal revisar els rols de cada usuari. | ⚠️ Pendent |

---

## TENANT

| Ítem | Estat |
|------|-------|
| Un cop fas login com a tenant, no hi ha cap secció a la sidebar, però si recarregues la pàgina apareixen | ⚠️ Pendent |
| En el formulari de crear nou tenant, els camps de validació de contrasenya apareixien en format d'error com a notificació per darrere del formulari. Seria més senzill fer la validació mentre s'escriu. | ✅ Arreglat |
| Apareixia un missatge d'error en crear el tenant, tot i que realment sí es creava i apareixia a la base de dades. | ✅ Arreglat |

**Tenant bug — Validació de contrasenya (arreglat):**
![Tennant bug 1](img/testing/image4.png)

**Tenant bug — Missatge d'error en crear tenant (arreglat):**
![Tennant bug 2](img/testing/image5.png)
