# 🍫  Chocolate RAP Project Documentation-MD AZAD

## Chocolate RAP Project — Overview

---

**Ye project ek simple SAP Fiori Read-Only app hai jo chocolate ka master data aur uski price display karta hai.**

**📦 Project Overview**PropertyValuePackage Name`ZSONU_CHOCOLATE_PKG`TechnologySAP RAP (RESTful Application Programming) OData VersionV2/V4LanguageABAP

Ek **SAP RAP based Read-Only OData service** jisme:

- Chocolates ki list dikhe (Dairy Milk, KitKat, Snickers...)
- Har chocolate ki price alag table mein ho
- Dono ko Fiori browser mein navigate kar sake
- **Koi Create / Update / Delete nahi** — sirf READ ✅

💡 Non-CRUD RAP app mein **Behavior Definition (BDEF) ki zaroorat nahi hoti** — sirf CDS Views aur Service enough hai.

## 📌 What is SAP RAP?

**RAP = RESTful Application Programming Model**

SAP RAP ek modern framework hai jo SAP ne banaya hai **Fiori Apps** aur **OData Services** banane ke liye. Ye ABAP ke upar kaam karta hai.

**RAP ke 3 main pillars:**

| Pillar | Kaam |
| --- | --- |
| **Data Model** | CDS Views — data kaisa dikhega define karta hai |
| **Behavior** | BDEF — create, update, delete allowed hai ya nahi |
| **Service** | OData — browser/Fiori tak data pohonchata hai |

## 🏗️ RAP Architecture — Simple Diagram

`┌─────────────────────────────────────────────┐
│              FIORI APP (Browser)            │
└────────────────────┬────────────────────────┘
                     ↓
┌─────────────────────────────────────────────┐
│         SERVICE BINDING (ZSB_CHOCOLATE)     │
│         OData V2/V4 Endpoint                │
└────────────────────┬────────────────────────┘
                     ↓
┌─────────────────────────────────────────────┐
│      SERVICE DEFINITION (ZUI_CHOCOLATE)     │
│      Expose which entities to OData         │
└────────────────────┬────────────────────────┘
                     ↓
┌─────────────────────────────────────────────┐
│    CONSUMPTION VIEWS  (ZC_ — UI Layer)      │
│    ZC_CHOCOLATE + ZC_CHOCOLATE_PRICE        │
│    @UI annotations — what Fiori sees        │
└────────────────────┬────────────────────────┘
                     ↓
┌─────────────────────────────────────────────┐
│    INTERFACE VIEWS  (ZI_ — BO Layer)        │
│    ZI_CHOCOLATE + ZI_CHOCOLATE_PRICE        │
│    Stable data model — composition tree     │
└────────────────────┬────────────────────────┘
                     ↓
┌─────────────────────────────────────────────┐
│       DATABASE TABLES  (Physical Storage)   │
│       ZCHOCOLATE + ZCHOCOLATE_PRICE         │
└─────────────────────────────────────────────┘`

## 📦 Project Info

| Property | Value |
| --- | --- |
| **Package Name** | `ZSONU_CHOCOLATE_PKG` |
| **Technology** | SAP RAP |
| **OData Version** | V2 / V4 |
| **Language** | ABAP |
| **Theme** | Chocolate Master Data App 🍫 |

> 💡 **Package** = SAP mein ek folder jisme saare related objects rakhe jaate hain. Jaise Java mein package ya Node mein module folder hota hai.
> 

## 🗄️ Layer 1 — Database Tables

> **Database Table kya hoti hai?**
Ye physical storage hai — actual data yahan save hota hai. ABAP mein ise `TRANSPARENT TABLE` kehte hain jo directly database mein ek table banati hai.
> 

---

### 📋 Table 1 — `ZCHOCOLATE`

**Label:** Primary Chocolate Database Table

> Ye table chocolate ka **master data** store karti hai — naam aur company kaun si hai.
> 

| Field | Type | Key | Description |
| --- | --- | --- | --- |
| `CLIENT` | `abap.clnt` | ✅ | SAP Client — auto handled |
| `SERIAL_NO` | `abap.numc(2)` | ✅ | Unique ID — 01 to 99 |
| `CHOCOLATE_NAME` | `abap.char(20)` | ❌ | Chocolate ka naam |
| `COMPANY_NAME` | `abap.char(20)` | ❌ | Manufacturer company |
| `CREATED_ON` | `abp_creation_tstmpl` | ❌ | 🤖 RAP Auto — kab create hua |
| `CREATED_BY` | `abp_creation_user` | ❌ | 🤖 RAP Auto — kisne create kiya |
| `CHANGED_ON` | `abp_locinst_lastchange_tstmpl` | ❌ | 🤖 RAP Auto — kab change hua |
| `CHANGED_BY` | `abp_locinst_lastchange_user` | ❌ | 🤖 RAP Auto — kisne change kiya |

Create a **Database Table-1: ZCHOCOLATE**

```abap
@EndUserText.label : '1. Primary Chocolate Database Table'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
@AbapCatalog.tableCategory : #TRANSPARENT
@AbapCatalog.deliveryClass : #A
@AbapCatalog.dataMaintenance : #RESTRICTED
define table zchocolate {

  key client     : abap.clnt not null;
  key serial_no  : abap.numc(2) not null;
  chocolate_name : abap.char(20);
  company_name   : abap.char(20);
  created_on     : abp_creation_tstmpl;
  created_by     : abp_creation_user;
  changed_on     : abp_locinst_lastchange_tstmpl;
  changed_by     : abp_locinst_lastchange_user;

}

-> Save and Activate

```

> 🤖 **RAP Admin Fields** = Ye fields manually set nahi karte — RAP framework automatically fill karta hai jab CRUD operations hote hain.
> 

---

### 📋 Table 2 — `ZCHOCOLATE_PRICE`

**Label:** Chocolate Price Details Table

> Ye table chocolate ki **price aur currency** store karti hai. `SERIAL_NO` se `ZCHOCOLATE` se link hoti hai.
> 

| Field | Type | Key | Description |
| --- | --- | --- | --- |
| `CLIENT` | `abap.clnt` | ✅ | SAP Client |
| `SERIAL_NO` | `abap.numc(2)` | ✅ | Foreign Key → ZCHOCOLATE |
| `CHOCOLATE_NAME` | `abap.char(20)` | ❌ | Chocolate ka naam |
| `PRICE` | `/dmo/flight_price` | ❌ | Price — currency ke saath |
| `CURRENCY_CODE` | `abap.cuky` | ❌ | INR, USD etc. |
| `CREATED_ON` | `abp_creation_tstmpl` | ❌ | 🤖 RAP Auto |
| `CREATED_BY` | `abp_creation_user` | ❌ | 🤖 RAP Auto |
| `CHANGED_ON` | `abp_locinst_lastchange_tstmpl` | ❌ | 🤖 RAP Auto |
| `CHANGED_BY` | `abp_locinst_lastchange_user` | ❌ | 🤖 RAP Auto |

Create a **Database Table-2: ZCHOCOLATE_PRICE**

```abap
@EndUserText.label : '2. Chocolate Price Table'
@AbapCatalog.enhancement.category : #NOT_EXTENSIBLE
@AbapCatalog.tableCategory : #TRANSPARENT
@AbapCatalog.deliveryClass : #A
@AbapCatalog.dataMaintenance : #RESTRICTED
define table zchocolate_price {

  key client     : abap.clnt not null;
  key serial_no  : abap.numc(2) not null;
  chocolate_name : abap.char(20);
  @Semantics.amount.currencyCode : 'zchocolate_price.currency_code'
  price          : /dmo/flight_price;
  currency_code  : abap.cuky;
  created_on     : abp_creation_tstmpl;
  created_by     : abp_creation_user;
  changed_on     : abp_locinst_lastchange_tstmpl;
  changed_by     : abp_locinst_lastchange_user;

}

-> Save and Activate
```

> 💡 `@Semantics.amount.currencyCode` annotation `PRICE` field pe lagti hai jo SAP ko batati hai ki currency reference kahan hai.
> 

---

## 👁️ Layer 2 — Interface Views (`ZI_`)

> **Interface View kya hoti hai?**
Ye CDS (Core Data Services) view hai jo **database table ka data** read karke ek structured model banata hai. Ye **stable** hoti hai — iska kaam sirf data expose karna hai, UI se koi matlab nahi.
> 
> 
> Rule: `ZI_` prefix = Interface View
> 

---

### 👁️ View 1 — `ZI_CHOCOLATE` (Root Interface View)

> Ye **root** hai puri composition tree ka. Iska matlab — ye parent hai aur `ZI_CHOCOLATE_PRICE` iska child hai.
> 

| Property | Value |
| --- | --- |
| **Type** | `define root view entity` |
| **Source** | `as select from zchocolate` |
| **Composition** | `[0..*] of Zi_CHOCOLATE_PRICE as _price` |
| **Role** | Parent / Root of BO tree |

```abap
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: '1. Primary Chocolate Interface View'
@Metadata.ignorePropagatedAnnotations: true
define root view entity Zi_CHOCOLATE
  as select from zchocolate
  composition [0..*] of Zi_CHOCOLATE_PRICE as _price
{
  key serial_no      as SerialNo,

      // -- Business Fields --
      chocolate_name as ChocolateName,
      company_name   as CompanyName,

      // -- RAP Admin Fields --
      created_by     as CreatedBy,
      created_on     as CreatedOn,
      changed_by     as ChangedBy,
      changed_on     as ChangedOn,

      // -- Child Association --
      _price
}

-> Save & Activate Later ( abhi error dega )
```

**Key Concepts:**

`composition [0..*] of ZI_CHOCOLATE_PRICE as _price
     ↑              ↑                        ↑
  keyword      child view name          association alias
  (parent       (jo child hai)          (_ prefix = convention)
   side pe)`

> 💡 `composition` matlab — child, parent ke bina exist nahi kar sakta. Ek chocolate ke saath uski price bhi delete ho jaayegi.
> 

**Fields:**

| Projected Name | Source Field | Type |
| --- | --- | --- |
| `SerialNo` | `serial_no` | Key |
| `ChocolateName` | `chocolate_name` | Business |
| `CompanyName` | `company_name` | Business |
| `CreatedBy/On` | `created_by/on` | Admin |
| `ChangedBy/On` | `changed_by/on` | Admin |
| `_price` | — | Child Association |

---

### 👁️ View 2 — `ZI_CHOCOLATE_PRICE` (Child Interface View)

> Ye **child** hai — parent `ZI_CHOCOLATE` ke through hi access hota hai.
> 

| Property | Value |
| --- | --- |
| **Type** | `define view entity` |
| **Source** | `as select from zchocolate_price` |
| **Association** | `to parent Zi_CHOCOLATE as _chocolate` |
| **Join** | `$projection.SerialNo = _chocolate.SerialNo` |
| **Role** | Child node |

```abap
@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: '2. Chocolate Price Interface View'
@Metadata.ignorePropagatedAnnotations: true
define view entity Zi_CHOCOLATE_PRICE
  as select from zchocolate_price
  association to parent Zi_CHOCOLATE as _chocolate
    on $projection.SerialNo = _chocolate.SerialNo
{
      // -- Key Field --
  key serial_no      as SerialNo,

      // -- Business Fields --
      chocolate_name as ChocolateName,

      // -- Price with Currency Reference --
      @Semantics.amount.currencyCode: 'CurrencyCode'
      price          as Price,
      currency_code  as CurrencyCode,

      // -- RAP Admin Fields --
      created_on     as CreatedOn,
      created_by     as CreatedBy,
      changed_on     as ChangedOn,
      changed_by     as ChangedBy,

      // -- Parent Association --
      _chocolate
}

```

**IMPORTANT:** Activate both **ZI_CHOCOLATE , ZI_CHOCOLATE_PRICE**  at same time.

**Key Concepts:**

`association to parent ZI_CHOCOLATE as _chocolate
     ↑           ↑        ↑              ↑
  keyword     direction  parent view   alias name
              (child →    (kaun sa       (_ prefix)
               parent)     parent)`

**Fields:**

| Projected Name | Source Field | Note |
| --- | --- | --- |
| `SerialNo` | `serial_no` | Key |
| `ChocolateName` | `chocolate_name` | Business |
| `Price` | `price` | Amount |
| `CurrencyCode` | `currency_code` | Currency |
| `CreatedBy/On` | — | Admin |
| `ChangedBy/On` | — | Admin |
| `_chocolate` | — | Parent Association |

---

## 🎨 Layer 3 — Consumption Views (`ZC_`)

> **Consumption View kya hoti hai?**
Ye `ZI_` (Interface View) ke upar ek projection hai — sirf Fiori UI ke liye banaya jaata hai. Isme `@UI` annotations, `redirected to` aur `provider contract` hota hai.
> 
> 
> Rule: `ZC_` prefix = Consumption View
> 

**Interface vs Consumption — Key Difference:**

|  | `ZI_` Interface | `ZC_` Consumption |
| --- | --- | --- |
| Source | `as select from DB_TABLE` | `as projection on ZI_VIEW` |
| UI Annotations | ❌ Nahi | ✅ Haan (via MDE) |
| `provider contract` | ❌ | ✅ |
| `redirected to` | ❌ | ✅ |
| Exposed in Service | ❌ | ✅ |

---

### 🎨 View 3 — `ZC_CHOCOLATE` (Root Consumption View)

> Ye Fiori ko dikhta hai — list page aur object page yahi se aata hai.
> 

| Property | Value |
| --- | --- |
| **Type** | `define root view entity` |
| **Source** | `as projection on Zi_CHOCOLATE` |
| **Key Annotation** | `@Metadata.allowExtensions: true` |

```abap
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: '1. Chocolate Consumption View'
@Metadata.ignorePropagatedAnnotations: false
@Metadata.allowExtensions: true

define root view entity ZC_CHOCOLATE
  as projection on ZI_CHOCOLATE
{
  key SerialNo,

      // -- Business Fields --
      ChocolateName,
      CompanyName,

      // -- RAP Admin Fields --
      CreatedBy,
      CreatedOn,
      ChangedBy,
      ChangedOn,

      // -- Redirected to Consumption Child --
      _price : redirected to composition child ZC_CHOCOLATE_PRICE
}

-> Save & Activate Later ( abhi error dega )
```

`_price : redirected to composition child ZC_CHOCOLATE_PRICE
--  ↑              ↑                           ↑
-- association   keyword                  consumption child
-- naam (ZI_ se  (ZI_ ki jagah           (ZC_ wala use karo)
--  aaya)         ZC_ use karo)`

---

### 🎨 View 4 — `ZC_CHOCOLATE_PRICE` (Child Consumption View)

> Price records ka UI projection.
> 

| Property | Value |
| --- | --- |
| **Type** | `define view entity` |
| **Source** | `as projection on Zi_CHOCOLATE_PRICE` |
| **Key Annotation** | `@Metadata.allowExtensions: true` |

```abap
@AbapCatalog.viewEnhancementCategory: [#NONE]
@AccessControl.authorizationCheck: #NOT_REQUIRED
@EndUserText.label: '2. Chocolate Price Consumption View'
@Metadata.ignorePropagatedAnnotations: false
@Metadata.allowExtensions: true

define view entity ZC_CHOCOLATE_PRICE
  as projection on ZI_CHOCOLATE_PRICE
{
      // -- Key Field --
  key SerialNo,

      // -- Business Fields --
      ChocolateName,
      Price,
      CurrencyCode,

      // -- RAP Admin Fields --
      CreatedOn,
      CreatedBy,
      ChangedOn,
      ChangedBy,

      // -- Redirected to Consumption Parent --
      _chocolate : redirected to parent ZC_CHOCOLATE
}

```

**IMPORTANT:** Activate both **ZC_CHOCOLATE , ZC_CHOCOLATE_PRICE**  at same time.

`_chocolate : redirected to parent ZC_CHOCOLATE`

---

## ⚙️ Layer 4 — Service Definition `ZUI_CHOCOLATE`

> **Service Definition kya hoti hai?**
Ye batata hai ki **kaunsi CDS views** OData service mein expose karni hain. Ye sirf ek mapping file hai — actual endpoint nahi banata.
> 

abap

```abap
@EndUserText.label: 'Chocolate App - OData Service Definition'
define service ZUI_CHOCOLATE {
  expose ZC_CHOCOLATE       as Chocolate;  -- Root
  expose ZC_CHOCOLATE_PRICE as Price;      -- Child
}
```

| Exposed View | Alias | OData Entity Set |
| --- | --- | --- |
| `ZC_CHOCOLATE` | `Chocolate` | `Chocolate` set |
| `ZC_CHOCOLATE_PRICE` | `Price` | `Price` set |

> ✅ Always `ZC_` views expose karo — kabhi `ZI_` nahi!
> 

---

## 🔗 Layer 5 — Service Binding `ZSB_CHOCOLATE`

> **Service Binding kya hoti hai?**
Ye Service Definition ko ek actual **OData HTTP endpoint** mein convert karta hai jise Fiori ya browser access kar sake.
> 

| Property | Value |
| --- | --- |
| **Name** | `ZSB_CHOCOLATE` |
| **Based On** | `ZUI_CHOCOLATE` |
| **Type** | OData V2 / V4 |
| **URL** | Auto-generated by SAP |

> ⚠️ **Important:** Har baar Service Definition change karo — Service Binding mein jaake **Publish** button dabao. Bina Publish ke changes reflect nahi honge browser mein!
> 

---

## 🏃 Layer 6 — Data Seeding Classes

> **Class Runner kya hota hai?**
Ye ek ABAP class hai jo `if_oo_adt_classrun` implement karta hai. Ise **F9** press karke directly ADT se run kar sakte hain — ek quick script jaisa. Iska kaam test/seed data insert karna hai, Iske alawe aur koi kam nahi hae project me.
> 

---

### 🏃 Class 1 — `ZCL_INSERT_CHOCOLATE`

> 30 chocolate records insert karta hai `ZCHOCOLATE` mein.
> 

| Property | Value |
| --- | --- |
| **Interface** | `if_oo_adt_classrun` |
| **Target Table** | `ZCHOCOLATE` |
| **Records** | 30 chocolates |
| **Run** | F9 in ADT |

```abap
CLASS zcl_insert_chocolate DEFINITION
  PUBLIC
  FINAL
  CREATE PUBLIC .

  PUBLIC SECTION.

    INTERFACES if_oo_adt_classrun .
  PROTECTED SECTION.
  PRIVATE SECTION.
ENDCLASS.

CLASS zcl_insert_chocolate IMPLEMENTATION.

METHOD if_oo_adt_classrun~main.
  DELETE FROM zchocolate.
  out->write( |Old Data Deleted from Database 🥲| ).

  DATA(lv_user) = cl_abap_context_info=>get_user_technical_name( ).
  DATA lv_time  TYPE abp_creation_tstmpl.

  DATA: gt_chocolate TYPE TABLE OF zchocolate.
  gt_chocolate = VALUE #(
( serial_no = 1  chocolate_name = 'Candyman'       company_name = 'Parle'     created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 2  chocolate_name = 'Dairy Milk'     company_name = 'Cadbury'   created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 3  chocolate_name = '5 Star'         company_name = 'Cadbury'   created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 4  chocolate_name = 'KitKat'         company_name = 'Nestle'    created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 5  chocolate_name = 'Munch'          company_name = 'Nestle'    created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 6  chocolate_name = 'Ferrero Rocher' company_name = 'Ferrero'   created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 7  chocolate_name = 'Snickers'       company_name = 'Mars'      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 8  chocolate_name = 'Twix'           company_name = 'Mars'      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 9  chocolate_name = 'Bar One'        company_name = 'Nestle'    created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 10 chocolate_name = 'Milkybar'       company_name = 'Nestle'    created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 11 chocolate_name = 'Perk'           company_name = 'Cadbury'   created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 12 chocolate_name = 'Bournville'     company_name = 'Cadbury'   created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 13 chocolate_name = 'Toblerone'      company_name = 'Mondelez'  created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 14 chocolate_name = 'Mars Bar'       company_name = 'Mars'      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 15 chocolate_name = 'Galaxy'         company_name = 'Mars'      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 16 chocolate_name = 'Melody'         company_name = 'Parle'     created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 17 chocolate_name = 'Kismi'          company_name = 'Parle'     created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 18 chocolate_name = 'Amul Dark'      company_name = 'Amul'      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 19 chocolate_name = 'Kinder Joy'     company_name = 'Ferrero'   created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 20 chocolate_name = 'Lindt Swiss'    company_name = 'Lindt'     created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 21 chocolate_name = 'Hersheys Bar'   company_name = 'Hershey'   created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 22 chocolate_name = 'Reeses'         company_name = 'Hershey'   created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 23 chocolate_name = 'Bounty'         company_name = 'Mars'      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 24 chocolate_name = 'Fuse'           company_name = 'Cadbury'   created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 25 chocolate_name = 'Gems'           company_name = 'Cadbury'   created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 26 chocolate_name = 'Eclairs'        company_name = 'Cadbury'   created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 27 chocolate_name = 'Coffee Bite'    company_name = 'Lotte'     created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 28 chocolate_name = 'LuvIt'          company_name = 'Global CP' created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 29 chocolate_name = 'After Eight'    company_name = 'Nestle'    created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
( serial_no = 30 chocolate_name = 'Aero'           company_name = 'Nestle'    created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )
                       ).

  INSERT zchocolate FROM TABLE  @gt_chocolate.
  out->write( |New Data Inserted Successfully 😀:)| ).

ENDMETHOD.

ENDCLASS.
```

**Logic Flow:**

`1. DELETE FROM zchocolate          ← old data saaf karo
2. lv_user = get_user_name         ← current user
3. GET TIME STAMP FIELD lv_time    ← current timestamp
4. gt_chocolate = VALUE #( ... )   ← 30 records define karo
5. INSERT zchocolate FROM TABLE    ← DB mein save karo
6. out->write( 'Done' )            ← console pe print karo`

**Sample Companies:** Cadbury, Nestle, Mars, Parle, Ferrero, Hershey, Amul, Lindt, Mondelez, Lotte

---

### 🏃 Class 2 — `ZCL_INSERT_CHOCOLATE_PRICE`

> 30 price records insert karta hai `ZCHOCOLATE_PRICE` mein.
> 

| Property | Value |
| --- | --- |
| **Interface** | `if_oo_adt_classrun` |
| **Target Table** | `ZCHOCOLATE_PRICE` |
| **Records** | 30 prices |
| **Currency** | INR |
| **Run** | F9 in ADT |

```abap
CLASS zcl_insert_chocolate_price DEFINITION
  PUBLIC
  FINAL
  CREATE PUBLIC .

  PUBLIC SECTION.

    INTERFACES if_oo_adt_classrun .
  PROTECTED SECTION.
  PRIVATE SECTION.
ENDCLASS.

CLASS zcl_insert_chocolate_price IMPLEMENTATION.

 METHOD if_oo_adt_classrun~main.
  DELETE FROM zchocolate_price.
  out->write( |Old Price Data Deleted 🥲| ).

  DATA(lv_user) = cl_abap_context_info=>get_user_technical_name( ).
DATA lv_time  TYPE abp_creation_tstmpl.

  DATA: gt_price TYPE TABLE OF zchocolate_price.
  gt_price = VALUE #(

    ( serial_no = 1  chocolate_name = 'Candyman'       price = '10.00'  currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 2  chocolate_name = 'Dairy Milk'     price = '20.00'  currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 3  chocolate_name = '5 Star'         price = '20.00'  currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 4  chocolate_name = 'KitKat'         price = '30.00'  currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 5  chocolate_name = 'Munch'          price = '10.00'  currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 6  chocolate_name = 'Ferrero Rocher' price = '399.00' currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 7  chocolate_name = 'Snickers'       price = '50.00'  currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 8  chocolate_name = 'Twix'           price = '60.00'  currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 9  chocolate_name = 'Bar One'        price = '30.00'  currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 10 chocolate_name = 'Milkybar'       price = '20.00'  currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 11 chocolate_name = 'Perk'           price = '10.00'  currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 12 chocolate_name = 'Bournville'     price = '99.00'  currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 13 chocolate_name = 'Toblerone'      price = '350.00' currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 14 chocolate_name = 'Mars Bar'       price = '60.00'  currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 15 chocolate_name = 'Galaxy'         price = '80.00'  currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 16 chocolate_name = 'Melody'         price = '5.00'   currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 17 chocolate_name = 'Kismi'          price = '5.00'   currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 18 chocolate_name = 'Amul Dark'      price = '130.00' currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 19 chocolate_name = 'Kinder Joy'     price = '50.00'  currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 20 chocolate_name = 'Lindt Swiss'    price = '499.00' currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 21 chocolate_name = 'Hersheys Bar'   price = '180.00' currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 22 chocolate_name = 'Reeses'         price = '200.00' currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 23 chocolate_name = 'Bounty'         price = '60.00'  currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 24 chocolate_name = 'Fuse'           price = '20.00'  currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 25 chocolate_name = 'Gems'           price = '10.00'  currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 26 chocolate_name = 'Eclairs'        price = '5.00'   currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 27 chocolate_name = 'Coffee Bite'    price = '5.00'   currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 28 chocolate_name = 'LuvIt'          price = '20.00'  currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 29 chocolate_name = 'After Eight'    price = '350.00' currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

    ( serial_no = 30 chocolate_name = 'Aero'           price = '150.00' currency_code = 'INR'
      created_by = lv_user  created_on = lv_time  changed_by = lv_user  changed_on = lv_time )

  ).

  INSERT zchocolate_price FROM TABLE @gt_price.
  out->write( |Price Data Inserted Successfully 😀| ).

ENDMETHOD.
ENDCLASS.
```

**Logic Flow:**

`1. DELETE FROM zchocolate_price    ← old data saaf karo
2. lv_user = get_user_name         ← current user
3. GET TIME STAMP FIELD lv_time    ← current timestamp
4. gt_price = VALUE #( ... )       ← 30 price records
5. INSERT zchocolate_price FROM TABLE ← DB mein save karo
6. out->write( 'Done' )            ← console pe print karo`

---

## 📊 Complete Object List

| # | Object Name | Type | Description |
| --- | --- | --- | --- |
| 1 | `ZSONU_CHOCOLATE_PKG` | Package | Container for all objects |
| 2 | `ZCHOCOLATE` | DB Table | Master chocolate data |
| 3 | `ZCHOCOLATE_PRICE` | DB Table | Price data |
| 4 | `ZI_CHOCOLATE` | CDS Interface View | Root BO model |
| 5 | `ZI_CHOCOLATE_PRICE` | CDS Interface View | Child BO model |
| 6 | `ZC_CHOCOLATE` | CDS Consumption View | Root UI projection |
| 7 | `ZC_CHOCOLATE_PRICE` | CDS Consumption View | Child UI projection |
| 8 | `ZUI_CHOCOLATE` | Service Definition | OData entity mapping |
| 9 | `ZSB_CHOCOLATE` | Service Binding | OData HTTP endpoint |
| 10 | `ZCL_INSERT_CHOCOLATE` | ABAP Class | Seed master data |
| 11 | `ZCL_INSERT_CHOCOLATE_PRICE` | ABAP Class | Seed price data |

---

## ⚡ Activation Order

> ⚠️ **Rule:** Child hamesha Parent se pehle activate hota hai!
> 

`Step 1  →  ZCHOCOLATE                  (DB Table)
Step 2  →  ZCHOCOLATE_PRICE            (DB Table)
Step 3  →  ZI_CHOCOLATE_PRICE          (Interface Child — FIRST!)
Step 4  →  ZI_CHOCOLATE                (Interface Root)
Step 5  →  ZC_CHOCOLATE_PRICE          (Consumption Child — FIRST!)
Step 6  →  ZC_CHOCOLATE                (Consumption Root)
Step 7  →  ZUI_CHOCOLATE               (Service Definition)
Step 8  →  ZSB_CHOCOLATE → Publish!    (Service Binding)
Step 9  →  ZCL_INSERT_CHOCOLATE        (Run F9)
Step 10 →  ZCL_INSERT_CHOCOLATE_PRICE  (Run F9)`

---

## 🐛 Common Issues & Fixes

| # | Issue | Root Cause | Fix |
| --- | --- | --- | --- |
| 1 | Navigation not working in browser | `redirected to` missing | Add in `ZC_` views |
| 2 | Activation error on parent | Child not activated first | Always child first |
| 3 | No data in browser | INSERT missing in class | Add `INSERT table FROM TABLE` |
| 4 | MDE annotation error | Field has no `@UI.*` | Add `@UI.identification` |
| 5 | UI not updated after changes | Service Binding not republished | Click Publish |
| 6 | Timestamp type mismatch | Wrong type used | Use `GET TIME STAMP FIELD` |
| 7 | `__composition__` error | Double underscore typo | Use `composition` |
| 8 | `provider contract` error | Old SAP version | Remove it — not supported |

---

## 💡 Key Concepts Summary

> Quick revision ke liye:
> 

| Concept | Definition |
| --- | --- |
| **RAP** | SAP ka modern framework — Fiori apps banane ke liye |
| **CDS View** | SQL jaisi language se data model define karna |
| **Interface View `ZI_`** | Stable BO data model — DB se data read karta hai |
| **Consumption View `ZC_`** | UI facing projection — Fiori dikhta hai |
| **Composition** | Parent-Child relation — child, parent ke bina exist nahi kar sakta |
| **Association** | Do independent entities ka relation |
| **`redirected to`** | `ZC_` mein batata hai — `ZI_` ki jagah `ZC_` use karo |
| **Service Definition** | Kaunsi views OData mein expose karni hain |
| **Service Binding** | OData ka actual HTTP endpoint banata hai |
| **MDE** | Metadata Extension — UI annotations alag file mein |
| **Class Runner** | F9 se run hone wali utility class — seed data ke liye |

**Final Folder Structure**

![image.png](%F0%9F%8D%AB%20Chocolate%20RAP%20Project%20Documentation-MD%20AZAD/image.png)

**OUTPUT:**

![image.png](%F0%9F%8D%AB%20Chocolate%20RAP%20Project%20Documentation-MD%20AZAD/image%201.png)

![image.png](%F0%9F%8D%AB%20Chocolate%20RAP%20Project%20Documentation-MD%20AZAD/image%202.png)

---

> 🍫 **Project by:** Md. Azad ( SAP-15, BIPARD Patna ) | Package: `ZSONU_CHOCOLATE_PKG` | Technology: SAP RAP
>