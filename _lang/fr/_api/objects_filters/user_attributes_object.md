---
nav_title: "Objet Attributs d’utilisateur"
article_title: Objet Attributs d’utilisateur API
page_order: 11
page_type: reference
description: "Cet article explique les différents composants de l’objet alias utilisateur."

---

# Spécification de l’objet Attributs d’utilisateur

Une demande API contenant l’un des champs de l’objet Attributs créera ou mettra à jour un attribut de ce nom avec la valeur donnée sur le profil utilisateur spécifié. Utilisez les champs de profil utilisateur de Braze (énumérés comme suit ou ceux énumérés dans la section des [champs de profils utilisateur de Braze][27]) pour mettre à jour ces valeurs spéciales sur le profil utilisateur dans le tableau de bord ou ajouter vos propres données d’attributs personnalisés à l’utilisateur.

```json
{
  // One of "external_id" or "user_alias" or "braze_id" is required
  "external_id" : (optional, string) see External User ID,
  "user_alias" : (optional, User Alias Object),
  "braze_id" : (optional, string) Braze User Identifier,
  // Setting this flag to true will put the API in "Update Only" mode.
  // When using a "user_alias", "Update Only" defaults to true.
  "_update_existing_only" : (optional, boolean),
  // See note regarding anonymous push token imports
  "push_token_import" : (optional, boolean),
  // Braze User Profile Fields
  "first_name" : "Jon",
  "email" : "bob@example.com",
  // Custom Attributes
  "my_custom_attribute" : value,
  "my_custom_attribute_2" : {"inc" : int_value},
  "my_array_custom_attribute":[ "Value1", "Value2" ],
  // Adding a new value to an array custom attribute
  "my_array_custom_attribute" : { "add" : ["Value3"] },
  // Removing a value from an array custom attribute
  "my_array_custom_attribute" : { "remove" : [ "Value1" ]},
}
```
- [ID utilisateur externe]({{site.baseurl}}/api/basics/#external-user-id-explanation)
- [Objet alias utilisateur]({{site.baseurl}}/api/objects_filters/user_alias_object/)

Pour supprimer un attribut de profil, définissez-le sur `null`. Certains champs, comme `external_id` et `user_alias` ne peuvent pas être supprimés une fois ajoutés à un profil utilisateur.

#### Mettre à jour les profils existants uniquement

Si vous souhaitez mettre à jour uniquement les profils utilisateur existants dans Braze, vous devez passer la clé `_update_existing_only` avec la valeur `true` dans le corps de votre demande. Si cette valeur est omise, Braze créera un nouveau profil utilisateur si `external_id` n’existe pas déjà.

{% alert note %}
Si vous créez un profil utilisateur alias uniquement via les utilisateurs/l’endpoint de suivi, `_update_existing_only` doit être défini sur `false`. Si cette valeur est omise, le profil alias uniquement ne sera pas créé.
{% endalert %}

#### Importation de jetons de notification push

Avant d’importer des jetons de notification push à Braze, vérifiez si cela est nécessaire. Lorsque les SDK Braze sont mis en place, ils gèrent automatiquement les jetons de notification push sans avoir besoin de les télécharger via l’API.

Si vous avez besoin de les télécharger via l’API, vous pouvez le faire pour des utilisateurs identifiés ou des utilisateurs anonymes. Cela signifie qu’un `external_id` doit exister ou que les utilisateurs anonymes doivent avoir un indicateur `push_token_import` défini sur `true`. 

{% alert note %}
Lors de l’importation de jetons de notification push provenant d’autres systèmes, un `external_id` n’est pas toujours disponible. Pour maintenir la communication avec ces utilisateurs pendant votre transition vers Braze, vous pouvez importer les jetons existants pour les utilisateurs anonymes sans fournir d’`external_id` en indiquant `push_token_import` comme `true`.
{% endalert %}

Lorsque vous indiquez `push_token_import` comme `true` :

* `external_id` et `braze_id` ne doivent **pas** être spécifiés
* L’objet d’attribut **doit** contenir un jeton de notification push
* Si le jeton existe déjà dans Braze, la demande est ignorée ; sinon, Braze créera un profil utilisateur temporaire et anonyme pour chaque jeton pour vous permettre de continuer à envoyer des messages à ces personnes

Après l’importation, lorsque chaque utilisateur lance la version compatible Braze de votre application, Braze déplace automatiquement leur jeton de notification push importé vers leur profil utilisateur Braze et efface le profil temporaire.

Braze vérifiera une fois par mois s’il existe des profils anonymes avec l’indicateur `push_token_import` qui n’ont pas de jeton de notification push. Si le profil anonyme n’a plus de jeton de notification push, nous le supprimerons. Cependant, si le profil anonyme a toujours un jeton de notification push, indiquant que l’utilisateur ne s’est pas encore connecté au périphérique avec le jeton de notification push, nous ne ferons rien.

Pour plus d’informations, consultez [Migration des jetons de notification push][3].

#### Types de données des attributs personnalisés

Les types de données suivants peuvent être stockés en tant qu’attribut personnalisé :

| Type de données | Remarques |
| --- | --- |
| Tableaux | Les tableaux d’attribut personnalisé sont des ensembles unidimensionnels ; les tableaux multidimensionnels ne sont pas pris en charge. L’ajout d’un élément à un tableau d’attribut personnalisé ajoute l’élément à la fin du tableau, à moins qu’il ne soit déjà présent, auquel cas il passe de sa position actuelle à la fin du tableau.<br><br>Par exemple, si un tableau `['hotdog','hotdog','hotdog','pizza']` a été importé, il apparaîtra dans l’attribut de tableau comme `['hotdog', 'pizza']` car seules des valeurs uniques sont prises en charge.<br><br>En plus de définir les valeurs d’un tableau en indiquant quelque chose comme `"my_array_custom_attribute":[ "Value1", "Value2" ]`, vous pouvez ajouter des tableaux existants en faisant quelque chose comme `"my_array_custom_attribute" : { "add" : ["Value3"] },` ou supprimer les valeurs d’un tableau en écrivant quelque chose comme `"my_array_custom_attribute" : { "remove" : [ "Value1" ]}`<br><br>Le nombre maximum d’éléments par défaut dans les tableaux d’attributs personnalisés est de 25, mais peut être augmenté sur demande. Pour plus d’informations, consultez les [tableaux][6]. |
| Booléens |  |
| Dates | Doit être stocké au format [ISO 8601][19] ou dans l’un des formats suivants : <br>- `yyyy-MM-ddTHH:mm:ss:SSSZ` <br>- `yyyy-MM-ddTHH:mm:ss` <br>- `yyyy-MM-dd HH:mm:ss` <br>- `yyyy-MM-dd` <br>- `MM/dd/yyyy` <br>- `ddd MM dd HH:mm:ss.TZD YYYY` <br><br>Notez que le « T » est un indicateur de temps, et non une marque substitutive. Il ne doit pas être modifié ou supprimé. <br><br>Les attributs de temps sans fuseau horaire seront par défaut à minuit UTC (et seront formatés sur le tableau de bord comme l’équivalent de minuit UTC dans le fuseau horaire de la société). <br><br> Les événements avec des horodatages dans le futur seront par défaut à l’heure actuelle. |
| Floats |  |
| Entiers | Les attributs personnalisés Integer peuvent être incrémentés par des entiers positifs ou négatifs en leur affectant un objet avec le champ « inc » et la valeur par laquelle vous souhaitez les incrémenter. <br><br>Exemple : `"my_custom_attribute_2" : {"inc" : int_value},`|
| Chaînes de caractères |  |
{: .reset-td-br-1 .reset-td-br-2}

Pour plus d’informations sur l’utilisation d’un événement personnalisé par rapport à un attribut personnalisé, consultez notre documentation correspondante sur les [événements personnalisés]({{site.baseurl}}/user_guide/data_and_analytics/custom_data/custom_events/) et les [attributs personnalisés]({{site.baseurl}}/user_guide/data_and_analytics/custom_data/custom_attributes/).

#### Champs de profil utilisateur Braze {#braze-user-profile-fields}

{% alert important %} 
Les champs de profil utilisateur suivants sont sensibles à la casse, veillez donc à référencer ces champs en minuscule.
{% endalert %}

| Champ profil utilisateur | Spécification des types de données |
| ---| --- |
| pays | (chaîne de caractères) Nous vous demandons de transmettre les indicatifs nationaux à Braze selon la [norme ISO-3166-1 alpha-2][17]. |
| emplacement_actuel | (objet) De la forme {"longitude" : -73.991443, "latitude" : 40.753824} |
| date_de_première_session | (date à laquelle l’utilisateur s’est servi de l’application pour la première fois) Chaîne de caractères au format ISO 8601 ou dans l’un des formats suivants : <br>- `yyyy-MM-ddTHH:mm:ss:SSSZ` <br>- `yyyy-MM-ddTHH:mm:ss` <br>- `yyyy-MM-dd HH:mm:ss` <br>- `yyyy-MM-dd` <br>- `MM/dd/yyyy` <br>- `ddd MM dd HH:mm:ss.TZD YYYY` |
| date_de_dernière_session | (date à laquelle l’utilisateur s’est servi de l’application pour la dernière fois) Chaîne de caractères au format ISO 8601 ou dans l’un des formats suivants : <br>- `yyyy-MM-ddTHH:mm:ss:SSSZ` <br>- `yyyy-MM-ddTHH:mm:ss` <br>- `yyyy-MM-dd HH:mm:ss` <br>- `yyyy-MM-dd` <br>- `MM/dd/yyyy` <br>- `ddd MM dd HH:mm:ss.TZD YYYY`  |
| ddn | (date de naissance) Chaîne de caractères au format « AAAA-MM-DD », p. ex. 1980-12-21. |
| E-mail | (chaîne de caractères) |
| e-mail_souscrire | (chaîne de caractères) Les valeurs disponibles sont « confirmé » (explicitement consenti à recevoir des e-mails), « désabonné » (explicitement refusé de recevoir des e-mails), et « abonné » (ni accepté, ni refusé).  |
|email_open_tracking_disabled (Suivi ouverture e-mail désactivé)|(booléen) vrai ou faux accepté.  Définissez sur True (Vrai) pour désactiver le pixel de suivi d’ouverture dans tous les futurs e-mails envoyés à cet utilisateur.|
|email_click_tracking_disabled (Suivi clics e-mail désactivé)|(booléen) vrai ou faux accepté.  Définissez sur True (Vrai) pour désactiver le suivi de clic pour tous les liens dans les futurs e-mails envoyés à cet utilisateur.|
| id_externe | (chaîne de caractères) De l’identifiant utilisateur unique. |
| facebook | hachage contenant l’un des `id` (chaîne de caractères), `likes` (tableau de chaînes de caractères), `num_friends` (entier). |
| _prénom | (chaîne de caractères) |
| sexe | (chaîne de caractères) « H », « F », « A » (autre), « S/O » (sans objet), « P » (préfère ne pas dire) ou nul (inconnu). |
| accueil_ville | (chaîne de caractères) |
| langue | (chaîne de caractères) la langue doit être transmise à Braze selon la [norme ISO-639-1][24]. Pour les langues prises en charge, consultez notre [liste de langues acceptées][2]. |
| _nom | (chaîne de caractères) |
|e-mail_marqué_comme_courrier indésirable_à| (chaîne de caractères) Date à laquelle l’e-mail de l’utilisateur a été marqué comme courrier indésirable. Apparaît au format ISO 8601 ou dans l’un des formats suivants : <br>- `yyyy-MM-ddTHH:mm:ss:SSSZ` <br>- `yyyy-MM-ddTHH:mm:ss` <br>- `yyyy-MM-dd HH:mm:ss` <br>- `yyyy-MM-dd` <br>- `MM/dd/yyyy` <br>- `ddd MM dd HH:mm:ss.TZD YYYY` |
| téléphone | (chaîne de caractères) |
| souscrire_notifications push | (chaîne de caractères) Les valeurs disponibles sont « confirmé » (explicitement consenti à recevoir des notifications push), « désabonné » (explicitement refusé de recevoir des notifications push), et « abonné » (ni accepté, ni refusé).  |
| jetons_de notification push | Tableau d’objets avec `app_id` et la chaîne de caractères `token`. Vous pouvez éventuellement fournir un `device_id` pour le périphérique auquel ce jeton est associé, par exemple, `[{"app_id": App Identifier, "token": "abcd", "device_id": "optional_field_value"}]`. Si aucun `device_id` n’est pas fourni, il sera généré de manière aléatoire. |
| fuseau_horaire | (chaîne de caractères) Nom de fuseau horaire de la [base de données de fuseaux horaires IANA][26] (par ex. « Amérique/New_York » ou « Heure de l’Est (États-Unis et Canada) »). Seules les valeurs de fuseau horaire valides seront définies. |
| twitter | Hachage contenant l’un des `id` (entier), `screen_name` (chaîne de caractères, nom d’utilisateur Twitter), `followers_count` (entier), `friends_count` (entier), `statuses_count` (entier). |
{: .reset-td-br-1 .reset-td-br-2}

Les valeurs de langue qui sont explicitement définies via cette API prévaudront sur les informations locales que Braze reçoit automatiquement du périphérique.

####  Demande d’exemple d’attribut utilisateur

```json
POST https://YOUR_REST_API_URL/users/track
Content-Type: application/json
Authorization: Bearer YOUR-REST-API-KEY
{
  "attributes" : [
    {
      "external_id" : "user1",
      "first_name" : "Jon",
      "has_profile_picture" : true,
      "dob": "1988-02-14",
      "music_videos_favorited" : { "add" : [ "calvinharris-summer" ], "remove" : ["nickiminaj-anaconda"] }
    },
    {
      "external_id" : "user2",
      "first_name" : "Jill",
      "has_profile_picture" : false,
      "push_tokens": [{"app_id": App Identifier, "token": "abcd", "device_id": "optional_field_value"}]

    },
    {
      "user_alias" : { "alias_name" : "device123", "alias_label" : "my_device_identifier"},
      "first_name" : "Alice",
      "has_profile_picture" : false,
    }
  ]
}
```

Cet exemple contient deux objets Attributs utilisateur parmi les 75 autorisés par appel d’API.


[2]: {{site.baseurl}}/user_guide/data_and_analytics/user_data_collection/language_codes/
[3]: {{site.baseurl}}/help/help_articles/push/push_token_migration/
[6]: {{site.baseurl}}/developer_guide/platform_wide/analytics_overview/#arrays
[15]: {{site.baseurl}}/user_guide/data_and_analytics/user_data_collection/
[17]: http://en.wikipedia.org/wiki/ISO_3166-1 "ISO-3166-1 codes"
[19]: http://en.wikipedia.org/wiki/ISO_8601 "ISO 8601 Time Code Wiki"
[24]: http://en.wikipedia.org/wiki/List_of_ISO_639-1_codes "ISO-639-1 codes"
[26]: https://en.wikipedia.org/wiki/List_of_tz_database_time_zones
[27]: #braze-user-profile-fields