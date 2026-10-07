# Universal Cart Optimizer

> Un motore di comparazione e ottimizzazione degli acquisti multi-store che costruisce il carrello più conveniente considerando prezzo, quantità, qualità, spedizione, sconti e disponibilità.

## Overview

**Universal Cart Optimizer** nasce da un problema semplice: lo stesso acquisto può richiedere prodotti provenienti da store diversi, ma confrontare manualmente prezzi, quantità, spedizioni, soglie per la consegna gratuita, coupon e qualità del prodotto è lento e poco pratico.

L'obiettivo non è creare l'ennesimo comparatore di prezzi, ma un sistema in grado di rispondere a una domanda più utile:

> **Da quali store devo acquistare ogni prodotto per ottenere il miglior carrello complessivo?**

Il sistema crea un **carrello universale virtuale** composto da prodotti provenienti da retailer differenti e propone diverse strategie di acquisto:

- prezzo minimo;
- miglior rapporto qualità/prezzo;
- minor numero di spedizioni;
- qualità premium;
- miglior compromesso tra costo e comodità.

Il progetto parte dalla nicchia degli **integratori sportivi**, dove prodotti equivalenti vengono venduti con nomi, formulazioni, quantità e prezzi molto diversi, ma l'architettura deve essere sufficientemente generica da poter essere estesa ad altri mercati.

---

## Problem

Oggi un utente che vuole acquistare, ad esempio:

- 2 kg di whey isolate;
- 500 g di creatina monoidrato;
- EAA;
- un prodotto pump senza caffeina;

deve:

1. visitare più store;
2. cercare manualmente prodotti equivalenti;
3. confrontare formati diversi;
4. calcolare il prezzo reale per kg o per dose;
5. verificare il contenuto effettivo di principio attivo;
6. aggiungere le spese di spedizione;
7. controllare eventuali soglie per la spedizione gratuita;
8. applicare codici sconto;
9. confrontare nuovamente il totale finale.

Un prodotto può essere più economico singolarmente ma rendere il carrello complessivo più costoso a causa della spedizione.

Il problema quindi non è semplicemente:

```text
qual è il prodotto più economico?
```

ma:

```text
qual è la combinazione di prodotti e store con il miglior costo totale?
```

---

## Vision

Creare un livello intermedio tra utente e retailer capace di:

1. normalizzare prodotti equivalenti;
2. confrontarli sulla base di metriche reali;
3. costruire un carrello cross-store;
4. ottimizzare automaticamente la distribuzione degli acquisti;
5. indirizzare l'utente ai checkout dei merchant.

In futuro il sistema potrà supportare anche checkout interoperabili tramite standard come UCP o integrazioni dirette con i merchant.

---

# Core Value Proposition

Un normale comparatore mostra:

```text
Creatina A     €9.99
Creatina B    €12.90
Creatina C    €29.90
```

Universal Cart Optimizer deve invece mostrare:

```text
CARRELLO CONSIGLIATO

Whey Isolate
Tsunami Nutrition        €85.43

Pump
ESN                      €39.90

Creatina
OstroVit                  €8.49

EAA
OstroVit                 €17.49

──────────────────────────────
Prodotti                 €151.31
Spedizioni                 €6.90
Sconti                    -€5.00
──────────────────────────────
TOTALE                   €153.21

Risparmio rispetto
all'acquisto su un solo store: €31.40
```

---

# Target Market

## Prima nicchia

Integratori sportivi in Europa.

### Categorie iniziali

- whey concentrate;
- whey isolate;
- whey hydrolyzed;
- caseine;
- creatina;
- EAA;
- BCAA;
- pre-workout;
- pump products;
- elettroliti;
- carboidrati intra-workout.

### Store iniziali

Possibili retailer da integrare:

- Tsunami Nutrition;
- ESN;
- HSN;
- OstroVit;
- GymBeam;
- Bulk;
- MyProtein;
- Prozis.

---

# Target Users

## Fitness enthusiast

Vuole prodotti di qualità ma non vuole pagare inutilmente il brand.

## Bodybuilder

Confronta spesso:

- materia prima;
- quantità;
- dosaggio;
- prezzo per dose;
- contenuto effettivo.

## Powerlifter / atleta

Compra frequentemente:

- creatina;
- pre-workout;
- elettroliti;
- proteine.

## Price-sensitive user

Vuole semplicemente il carrello complessivo più economico.

---

# Main Features

## 1. Universal Product Search

Ricerca simultanea su più store.

Esempio:

```text
creatina monoidrato 500g
```

Risultato:

```text
OstroVit      500 g     €8.49
HSN           500 g    €11.90
GymBeam       500 g    €12.50
ESN           500 g    €29.90
```

---

## 2. Product Normalization

Prodotti commercialmente diversi devono poter essere riconosciuti come appartenenti alla stessa categoria.

Esempio:

```text
ESN Ultrapure Creatine
OstroVit Creatine Monohydrate
HSN Creatine 200 Mesh
```

Normalizzazione:

```text
category: creatine
ingredient: creatine_monohydrate
```

Questo permette confronti basati su dati reali.

---

## 3. Normalized Pricing

Il prezzo della confezione da solo non è sufficiente.

Il sistema deve calcolare metriche come:

### Proteine

```text
€/kg prodotto
€/kg proteina effettiva
proteine %
€/dose
```

### Creatina

```text
€/kg
€/100 g creatina effettiva
€/dose da 5 g
```

### EAA

```text
€/kg prodotto
g EAA/dose
€/100 g EAA effettivi
leucina/dose
€/dose efficace
```

### Pump

```text
citrullina/dose
glicerolo/dose
nitrati/dose
sodio/dose
€/dose
```

---

# Universal Cart

L'utente può aggiungere prodotti provenienti da merchant differenti nello stesso carrello virtuale.

Esempio:

```text
Universal Cart

[ESN]
Crank Pump Pro                €39.90

[Tsunami]
Isolate Pure 2 kg             €85.43

[OstroVit]
Creatine Monohydrate           €8.49
EAA 600 g                     €17.49
```

Il sistema calcola automaticamente:

- subtotale per merchant;
- spedizione;
- soglia spedizione gratuita;
- coupon;
- sconti;
- totale finale.

---

# Cart Optimization Engine

Questa rappresenta la feature centrale del prodotto.

Dato un insieme di prodotti richiesti, il sistema deve trovare la combinazione migliore tra i retailer disponibili.

## Objective Function

In forma semplificata:

```text
minimize:

Σ product_price
+ Σ shipping_cost
- Σ discounts
```

soggetto a vincoli quali:

```text
stock > 0

required_quantity >= target_quantity

product_quality >= minimum_quality

merchant_available = true
```

---

## Optimization Modes

L'utente può scegliere diversi criteri.

### Cheapest

Minimo costo assoluto.

```text
€104.80
3 store
```

### Best Value

Miglior rapporto qualità/prezzo.

```text
€119.40
2 store
```

### Fewer Shipments

Minimizza il numero di ordini.

```text
€128.20
1 store
```

### Premium

Privilegia:

- materie prime certificate;
- formulazioni premium;
- brand selezionati.

```text
€156.90
2 store
```

---

# Shipping Optimization

Il motore deve considerare le regole di spedizione di ogni merchant.

Esempio:

```text
OstroVit

cart subtotal: €42
shipping: €6.90

free shipping threshold: €49
```

Il sistema può suggerire:

```text
Aggiungendo €7 di prodotti
risparmi €6.90 di spedizione.
```

Oppure può trovare automaticamente una combinazione migliore.

---

# Discounts & Coupons

Ogni merchant può avere:

- coupon generali;
- coupon influencer;
- sconti automatici;
- membership;
- sconti quantità;
- promozioni temporanee.

Il motore deve distinguere tra:

```text
public_discount
personal_discount
coupon
automatic_discount
membership_discount
```

Il prezzo mostrato deve specificare chiaramente quale sconto è stato applicato.

---

# Price History

Ogni offerta può mantenere uno storico.

```text
ProductOfferPrice

offer_id
price
discount_price
timestamp
```

Questo permette di mostrare:

```text
Current price: €39.90

30-day average: €44.20

Lowest price:
€36.90
```

---

# Price Alerts

L'utente può creare una wishlist e impostare:

```text
Avvisami quando:

ESN Crank Pump Pro < €35
```

oppure:

```text
Avvisami quando il mio
carrello scende sotto €130.
```

---

# Product Quality Model

Il sistema non deve basarsi solo sul prezzo.

Ogni prodotto può essere valutato secondo parametri specifici della categoria.

## Example: Whey Isolate

```text
protein_percentage
protein_source
manufacturing_method
amino_spiking
lactose
third_party_testing
raw_material
```

## Example: Creatine

```text
creatine_form
purity
mesh_size
raw_material
third_party_testing
```

## Example: EAA

```text
total_eaa
leucine
isoleucine
valine
lysine
threonine
phenylalanine
methionine
histidine
tryptophan
```

---

# Architecture

Proposta iniziale:

```text
                    ┌──────────────┐
                    │   Next.js    │
                    │   Frontend   │
                    └──────┬───────┘
                           │
                           ▼
                    ┌──────────────┐
                    │ API Gateway  │
                    └──────┬───────┘
                           │
        ┌──────────────────┼──────────────────┐
        │                  │                  │
        ▼                  ▼                  ▼
 Catalog Service      Cart Service      User Service
        │                  │
        │                  ▼
        │          Optimization Engine
        │
        ▼
 Merchant Integration Layer
        │
 ┌──────┼──────────────────────────────┐
 │      │       │       │              │
 ▼      ▼       ▼       ▼              ▼
ESN    HSN   OstroVit  GymBeam      Tsunami
```

---

# Suggested Tech Stack

## Frontend

```text
Next.js
React
TypeScript
Tailwind CSS
```

## Backend

Possibili alternative:

```text
Java + Spring Boot
```

oppure:

```text
Java + Quarkus
```

## Database

```text
PostgreSQL
```

## Cache

```text
Redis
```

## Messaging

Per evoluzioni future:

```text
NATS JetStream
```

oppure:

```text
Kafka
```

per:

- aggiornamento prezzi;
- stock events;
- price alerts;
- catalog ingestion.

---

# Merchant Adapter Layer

Ogni store deve essere isolato dietro un'interfaccia comune.

Esempio:

```java
public interface MerchantAdapter {

    List<ProductOffer> search(String query);

    ProductOffer getOffer(String productId);

    StockStatus getStock(String productId);

    ShippingQuote calculateShipping(Cart cart);

    CheckoutResult createCheckout(Cart cart);
}
```

Implementazioni:

```text
TsunamiAdapter
ESNAdapter
HSNAdapter
OstroVitAdapter
GymBeamAdapter
```

Questo permette di sostituire in futuro:

```text
scraping
```

con:

```text
affiliate feed
API
UCP
merchant integration
```

senza modificare il dominio applicativo.

---

# Data Sources

Ordine di preferenza:

## 1. Merchant API

Soluzione ideale.

```text
API
GraphQL
REST
```

---

## 2. Affiliate Product Feed

Formati comuni:

```text
CSV
XML
JSON
```

Campi tipici:

```text
SKU
EAN
name
brand
price
discount_price
stock
URL
image
```

---

## 3. UCP

Supportare **Universal Commerce Protocol** come adapter nativo.

Architettura prevista:

```text
Merchant
   │
   ▼
UCP supported?
   │
   ├── YES → UCPAdapter
   │
   └── NO
        │
        ├── MerchantAPIAdapter
        ├── AffiliateFeedAdapter
        └── LegacyAdapter
```

---

## 4. Scraping

Ultima opzione.

Da utilizzare solo quando:

- legalmente consentito;
- tecnicamente sostenibile;
- non esistono API/feed.

Lo scraping non dovrebbe essere il fondamento principale della piattaforma.

---

# Core Data Model

Versione semplificata.

## Merchant

```text
id
name
website
country
currency
free_shipping_threshold
affiliate_program
```

## Product

```text
id
name
brand
category
normalized_product_id
```

## ProductVariant

```text
id
product_id
flavour
size
weight
ean
```

## Ingredient

```text
id
name
normalized_name
```

## ProductIngredient

```text
product_id
ingredient_id
amount
unit
serving_size
```

## Offer

```text
id
merchant_id
product_variant_id
price
discount_price
stock
url
updated_at
```

## ShippingRule

```text
merchant_id
country
base_cost
free_shipping_threshold
```

## Coupon

```text
id
merchant_id
code
type
value
minimum_cart
valid_from
valid_until
```

---

# Checkout Strategy

## Phase 1

Il progetto NON gestisce il pagamento.

Il sistema genera:

```text
Universal Cart
```

e successivamente divide il carrello per merchant.

Esempio:

```text
Checkout

ESN
2 prodotti
€55

[Apri checkout ESN]

OstroVit
3 prodotti
€40

[Apri checkout OstroVit]
```

---

# Deep Checkout

Quando possibile il sistema può:

1. generare il carrello sul sito del merchant;
2. trasferire l'utente;
3. lasciare al merchant pagamento e gestione ordine.

Questo permette di evitare inizialmente:

- gestione pagamenti;
- KYC merchant;
- chargeback;
- IVA marketplace;
- resi centralizzati;
- responsabilità diretta sull'ordine.

---

# Future Unified Checkout

In una fase successiva si può valutare un vero checkout unico.

Possibili tecnologie:

```text
Stripe Connect
UCP
merchant partnership
```

Flusso teorico:

```text
Customer
   │
   │ €150
   ▼
Platform
   │
   ├── Merchant A €60
   ├── Merchant B €50
   ├── Merchant C €35
   │
   └── Platform fee €5
```

Questa fase richiede accordi diretti con i merchant e un'analisi legale molto più approfondita.

---

# Browser Extension

Una possibile evoluzione è un'estensione browser.

Quando l'utente visita un prodotto:

```text
Creatina
€29.90
```

l'estensione può mostrare:

```text
↓ €21.41 altrove

OstroVit
€8.49
```

CTA:

```text
Add to Universal Cart
```

Il carrello rimane disponibile durante la navigazione sui vari retailer.

---

# Monetization

## Affiliate Revenue

Modello iniziale consigliato.

```text
User
   │
   ▼
Universal Cart Optimizer
   │
   ▼
Affiliate Link
   │
   ▼
Merchant
```

La piattaforma riceve una commissione sul sale generato.

---

## Premium Subscription

Possibile modello:

### Free

- ricerca prodotti;
- comparazione;
- universal cart;
- metriche normalizzate.

### Premium

Possibile prezzo:

```text
€2.99 - €4.99 / month
```

Feature:

- price alerts;
- carrello ottimizzato automatico;
- price history avanzata;
- coupon finder;
- wishlist;
- price-drop alerts;
- acquisti ricorrenti.

---

## Sponsored Merchants

I merchant possono acquistare visibilità.

Gli annunci devono essere chiaramente separati da:

```text
Cheapest
Best Value
Best Product
```

per non compromettere la fiducia nella piattaforma.

---

# MVP

L'MVP deve essere volutamente limitato.

## Merchant

```text
Tsunami Nutrition
ESN
HSN
OstroVit
GymBeam
```

## Categories

```text
Whey Isolate
Creatine
EAA
Pump
```

## Features

### V0.1

- catalogo prodotti;
- merchant;
- offerte;
- ricerca.

### V0.2

- normalizzazione prodotti;
- €/kg;
- €/dose;
- contenuto effettivo.

### V0.3

- universal cart;
- shipping rules;
- calcolo totale.

### V0.4

- optimization engine.

### V0.5

- affiliate links;
- checkout redirect.

---

# Example User Flow

```text
User opens app
       │
       ▼
Searches:
"2kg isolate"
       │
       ▼
Adds requirement
       │
       ▼
Searches:
"creatine 500g"
       │
       ▼
Adds requirement
       │
       ▼
Searches:
"EAA"
       │
       ▼
Optimize Cart
       │
       ▼
┌─────────────────────────────┐
│ Cheapest                    │
│ €112                        │
│ 3 stores                    │
├─────────────────────────────┤
│ Best Value                  │
│ €124                        │
│ 2 stores                    │
├─────────────────────────────┤
│ Single Store                │
│ €135                        │
│ 1 store                     │
└─────────────────────────────┘
       │
       ▼
Select strategy
       │
       ▼
Checkout by merchant
```

---

# Roadmap

## Phase 1 — Comparison

- product ingestion;
- normalization;
- price comparison;
- normalized metrics.

## Phase 2 — Universal Cart

- multi-store cart;
- shipping rules;
- coupons;
- merchant grouping.

## Phase 3 — Optimization

- minimum total cost;
- best value;
- minimum merchants;
- premium strategy.

## Phase 4 — Monetization

- affiliate programs;
- price alerts;
- user accounts;
- saved carts.

## Phase 5 — Browser Extension

- product detection;
- cross-store comparison;
- add to universal cart.

## Phase 6 — Merchant Integrations

- direct APIs;
- affiliate feeds;
- UCP;
- merchant partnerships.

## Phase 7 — Unified Checkout

Solo dopo aver raggiunto sufficiente volume e partnership commerciali.

---

# Expansion Strategy

Dopo aver validato il prodotto nel settore integratori:

```text
Supplements
     │
     ▼
Fitness
     │
     ▼
Cosmetics
     │
     ▼
Pet Food
     │
     ▼
Consumer Electronics
```

L'Optimization Engine deve essere indipendente dalla categoria.

---

# Competitive Positioning

Il progetto non deve competere principalmente con:

```text
Google Shopping
Idealo
Trovaprezzi
```

sul semplice confronto del prezzo del singolo articolo.

Il vantaggio competitivo deve essere:

```text
PRODUCT COMPARISON
        +
PRODUCT NORMALIZATION
        +
CART OPTIMIZATION
        +
SHIPPING OPTIMIZATION
        +
CROSS-STORE CART
```

Il prodotto non risponde solamente:

> Dove costa meno questo prodotto?

Risponde:

> **Come devo distribuire tutto il mio ordine tra i vari store per ottenere il miglior risultato complessivo?**

---

# Key Differentiator

La feature centrale può essere sintetizzata come:

> **Smart Cart Optimization**

Input:

```text
I need:

2 kg whey isolate
500 g creatine
EAA
stim-free pump
```

Output:

```text
BEST VALUE CART

Tsunami Nutrition
Whey Isolate                  €85

ESN
Pump                          €39

OstroVit
Creatine                       €9
EAA                           €17

Products                     €150
Shipping                       €6
Discounts                     -€4

TOTAL                         €152

Estimated saving: €31
```

---

# Risks

## Data Reliability

Prezzi e stock cambiano frequentemente.

Mitigazione:

- cache con TTL;
- timestamp;
- aggiornamenti frequenti;
- merchant feed.

## Product Matching

Due prodotti possono sembrare equivalenti ma avere formulazioni differenti.

Mitigazione:

- normalized ingredients;
- ingredient-level matching;
- category-specific rules.

## Scraping

Può essere fragile o non consentito.

Mitigazione:

- affiliate feeds;
- API;
- partnership;
- UCP.

## Checkout Integration

Ogni retailer può avere sistemi differenti.

Mitigazione:

- merchant adapters;
- redirect checkout iniziale.

## Marketplace Regulation

Un checkout centralizzato aumenta significativamente gli obblighi normativi.

Mitigazione:

- evitare la gestione diretta del pagamento durante MVP e prime fasi.

---

# Success Metrics

Metriche iniziali:

```text
search_to_cart_rate

optimized_cart_rate

affiliate_click_rate

checkout_redirect_rate

estimated_user_savings

average_cart_value

repeat_purchase_rate
```

Metriche particolarmente importanti:

```text
Average User Saving
```

e:

```text
% users choosing optimized cart
```

---

# Initial Hypothesis

L'ipotesi da validare è:

> Gli utenti che acquistano frequentemente prodotti comparabili da più retailer sono disposti a utilizzare un servizio che calcoli automaticamente il carrello multi-store più conveniente.

Prima di sviluppare checkout complessi, l'obiettivo deve essere verificare che gli utenti:

1. cerchino prodotti;
2. creino un carrello;
3. utilizzino l'ottimizzazione;
4. clicchino sui retailer suggeriti;
5. completino effettivamente gli acquisti.

---

# Project Principle

> **Optimize before you checkout.**

Il valore della piattaforma non deriva dal possedere il checkout.

Deriva dalla capacità di capire:

**cosa comprare, dove comprarlo e in quale combinazione.**

---

## Status

```text
Concept / Pre-MVP
```

## Initial Scope

```text
Market: Europe
Vertical: Sports Supplements
Business model: Affiliate-first
Checkout model: Merchant redirect
```
