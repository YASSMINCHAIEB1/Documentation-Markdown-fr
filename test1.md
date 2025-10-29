# Documentation du site (API HTTP SMS)

## Introduction générale :
   Le site **Tunisie SMS** permet d’envoyer et de gérer des SMS facilement. Les utilisateurs peuvent envoyer des SMS individuels ou en masse, programmer leur envoi, suivre leur statut, gérer leur compte et consulter un tableau de bord.
- Présentation du site et du service :
   - **Service :** Envoi de SMS via le fournisseur L2T / Tunisie SMS.
   - **Accès API :** dans le menu **API → HTTP SMS API** de votre compte.

---
## Introduction (http SMS) :
L’**HTTP SMS API** est une interface qui permet à un système de gérer l’envoi de SMS automatiquement, sans passer par l’interface graphique du site Tunisie SMS.
---

## Définition d’une API
Une **API (Application Programming Interface)** est un ensemble de règles et d’outils qui permet à deux applications ou systèmes de communiquer entre eux.
  Dans notre cas :
L’API HTTP SMS ne communique pas entre deux sites web classiques, mais entre notre application (ou logiciel) et le système d’envoi de SMS du fournisseur. Elle permet ainsi d’envoyer des SMS et de recevoir des accusés de réception automatiquement, sans passer par l’interface du site.
### Prérequis (création d'une application) :
Avant d’utiliser l’API, il est nécessaire de créer une application sur la plateforme.
Cette application doit contenir les informations suivantes :
- Nom de l’application
- Site web associé
- Description de l’application
- Version (v0 ou v1)
- Catégorie (ex : Informatique, Achats, etc.)
- Icône de l’application
Une fois ces étapes terminées, vous obtiendrez un **identifiant (ID)** et une **clé d’authentification (API Key)** avec une **date d’expiration**.
**Pourquoi créer une application ?**
L’application permet d’identifier l’émetteur des requêtes et d’authentifier chaque envoi auprès du serveur.
Sans création d’application → pas de clé API → impossibilité d’envoyer des SMS.
---
## Détails de l’API HTTP SMS :
L’API HTTP SMS permet d’interagir avec le service d’envoi de SMS via des requêtes HTTP.
Chaque requête contient une clé d’authentification (API Key), des paramètres spécifiques, et renvoie une réponse sous forme de code ou de message XML/JSON.
---
### Requête HTTP d’envoi :
Cette requête permet d’envoyer un SMS à un ou plusieurs numéros de téléphone

```http

https://api.l2t.io/dz/s/api/v1/sms?fct=sms&key=35w8G39X48E608jz41g3K6962AtTtC9i1hAW6Z7974tg75KAOQU38rcW148k7f3&mobile=216XXXXXXXX&sms=Hello+World&sender=YYYYYYY&date=jj/mm/aaaa&heure=hh:mm&content-type=%CONTENT-TYPE%    
```
#### Paramètres principaux :
| **Champ** | **Description** |
|------------------|------------------|
|**fct**|Fonction utilisée (ici sms pour envoyer un message) |
|**key**|Clé d’authentification obtenue lors de la création de l’application|
|**mobile**|Numéro du destinataire au format international|
|**sms**|Contenu du message à envoyer|
|**sender**|Nom de l’expéditeur déclaré sur la plateforme|

### URL d’accusé de réception :
Cette requête permet d’obtenir le statut d’un message SMS déjà envoyé.
Elle indique si le message a bien été reçu, échoué, ou est encore en attente


```http


https://api.l2t.io/dz/s/api/v1/sms?fct=dlr&key=35w8G39X48E608jz41g3K6962AtTtC9i1hAW6Z7974tg75KAOQU38rcW148k7f3&msg_id=XXXX;YYYY&content-type=%CONTENT-TYPE%   

```
### Paramètres principaux :
|**Champ**|**Description**|
|------------|----------------|
|**fct**|Fonction appelée, ici (dlr)|
|**msg_id**|Identifiant du ou des messages à vérifier (séparés par ’;’)|

### Réponse DLR :
La réponse de l’API DLR indique le statut de livraison du ou des SMS envoyés.
Elle permet de savoir si le message a bien été livré, échoué ou est en attente.
La réponse peut être renvoyée sous deux formats possibles : JSON ou XML.
### EXAMPLE :
```json
    "success" : true,
    "message": "dlr response",
    "code": "dlr_success",
    "status": 200,
    "data": [
        {
            "message_id": XXXXX,
            "dlr": XXXXX,
            "dlr_date": XXXXXX,
            "ref": "XXXXXXX"
        }
    ]
```
```XML
	<?xml version="1.0"?>
<response>
    <success>1</success>
    <message>dlr response</message>
    <code>dlr_success</code>
    <status>200</status>
    <data>
        <0>
            <message_id>XXXX</message_id>
            <dlr> XXXXXXXX<dlr/>
            <dlr_date> XXXXXXXX<dlr_date/>
            <ref>XXXXX</ref>
        </0>
    </data>
</response>
```
``` json
{
    "success": true,
    "message": "dlr response",
    "code": "dlr_success",
    "status": 200,
    "data": [
        {
            "message_id": "2222",
            "dlr": "DELIVERED",
            "dlr_date": "2025-10-20",
            "ref": "ABC123XYZ"
        }
    ]
}
```
```xml
	<?xml version="1.0"?>
<response>
    <success>1</success>
    <message>dlr response</message>
    <code>dlr_success</code>
    <status>200</status>
    <data>
        <item>
            <message_id>1234</message_id>
            <dlr>DELIVERED</dlr>
            <dlr_date>2025-10-20</dlr_date>
            <ref>ABC123XYZ</ref>
        </item>
    </data>
</response>
```


|**Champ**|**Description**|
|------------|----------------|
|**msg_id**|Identifiant unique du message envoyé|
|**status**|Statut du message (DELIVERED, UNDELIVERED, EXPIRED ,REJECTED, UNKNOWN)|

### Statuts possibles
- **DELIVERED :**<br>
Succès d’envoi. 
- **UNDELIVERED :** <br>
Le message n’a pas pu être livré.<br>
 **Causes possibles :**<br>
Numéro ou adresse invalide.<br>
Téléphone éteint ou hors couverture pendant trop longtemps.<br>
Erreur du fournisseur 
- **EXPIRED :**
Le système a essayé plusieurs fois d’envoyer le message, mais sans succès avant le délai maximum<br>
**Causes possibles :**<br>
 Téléphone éteint ou injoignable
- **REJECTED :**
Le message a été refusé par le réseau ou le fournisseur.<br>
**Causes possibles :**<br>
Numéro bloqué.<br>
Filtrage de contenu (spam, mot interdit, etc.).<br>
Expéditeur non autorisé.

- **UNKNOWN :** <br>
Le statut réel n’a pas pu être déterminé.<br>
**Causes possibles :**<br>
Cela arrive quand le fournisseur n’a pas reçu de retour d’accusé de réception.<br>
Peut-être temporaire ou dû à un problème de communication avec le réseau.<br>


### Requête HTTP solde :
Cette requête permet de consulter le solde de votre compte SMS, c’est-à-dire le nombre de SMS restants disponibles pour l’envoi.
```http
https://api.l2t.io/dz/s/api/v1/sms?fct=balance&key=%KEY%&content-type=%CONTENT-TYPE%
```
|**Champ**|**Description**|
|------------------|------------------|
|**fct**|La fonction appelée : ici balance pour consulter le solde|
|**content-type**|Le format de la réponse souhaitée (json ou xml)|

###  Exemple de réponse — Format JSON

```json
{
    "success": true,
    "message": "Account balance",
    "code": "balance_success",
    "status": 200,
    "data": {
        "id_client": "XXXX",
        "balance": "XXXX"
    }
}
```


### Exemple de réponse — Format XML

```XML
	<?xml version="1.0"?>
<response>
    <success>1</success>
    <message>Account balance</message>
    <code>balance_success</code>
    <status>200</status>
    <data>
        <id_client>XXXX</id_client>
        <balance>XXXX</balance>
    </data>
</response>
```
###  Exemple de réponse — Format JSON
```JSON
{
    "success": true,
    "message": "Account balance",
    "code": "balance_success",
    "status": 200,
    "data": {
        "id_client": "1111",
        "balance": 350
    }
}
```
### Exemple de réponse — Format XML
```XML
<?xml version="1.0"?>
<response>
    <success>1</success>
    <message>Account balance</message>
    <code>balance_success</code>
    <status>200</status>
    <data>
        <id_client>2222</id_client>
        <balance>350</balance>
    </data>
</response>
```
- Status:   Indique si la requête a réussi.
- Balance:  Le nombre de SMS restants sur le compte.

A chaque requête notre API HTTP SMS renvoie des **codes de statut HTTP :** les codes **2xx** indiquent le succès, tandis que les codes **4xx** signalent une erreur due aux informations fournies dans la requête.

|**Status code**|Status signification| 
|------------------|------------------|
|**200**|✅OK — Requête réussie, le SMS a été traité par le serveur|
|**400**|❌Absence de paramètre obligatoire|
|**401**|❌Clé API non autorisée ou invalide|
|**402**|⚠️Solde insuffisant pour envoyer le SMS|
|**420**|⚠️Quota quotidien dépassé|
|**430**|❌Contenu du message manquant|
|**431**|❌Destination (numéro) manquante|
|**440**|⚠️Contenu du message trop long|
|**441**|❌Destination non autorisée|
|**442**|❌Expéditeur non autorisé|
|**500**|💥Erreur interne du serveur|

### Bonnes pratiques :
- Garder la clé API secrète
- Encoder le contenu (sms)
- respecter la réglementation et le consentement
## Conclusion :
L’**API HTTP SMS** de **Tunisie SMS** permet d’envoyer des SMS, de consulter leur statut et de vérifier le solde directement depuis une application externe, sans passer par l’interface graphique du site.
Grâce à l’authentification via la clé API et à la création d’une application, le système peut identifier et sécuriser les requêtes, suivre les envois.


