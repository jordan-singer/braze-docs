---
nav_title: "Listes et adresses e-mail"
article_title: Endpoints des listes et adresses e-mail
search_tag: Endpoint
page_order: 0
layout: featured

description: "Cette page d’accueil explique et répertorie les endpoints Braze de listes et adresses e-mail."
page_type: landing

guide_top_header: "Endpoints des listes et adresses e-mail"
guide_top_text: "Le statut de l’abonnement aux e-mails des utilisateurs peut être mis à jour et récupéré via Braze à l’aide d’une API RESTful. Vous pouvez utiliser l’API pour configurer une synchronisation bidirectionnelle entre Braze et d’autres systèmes de messagerie ou votre propre base de données."

guide_featured_title: ""
guide_featured_list:
  - name: "GET : Répertorier les rebonds élevés"
    link: /docs/api/endpoints/email/get_list_hard_bounces/
    fa_icon: fas fa-reply-all
  - name: "GET : Demander les adresses e-mail désinscrites"
    link: /docs/api/endpoints/email/get_query_unsubscribed_email_addresses/
    fa_icon: fas fa-envelope
  - name: "POST : Modifier le statut de l’abonnement aux e-mails"
    link: /docs/api/endpoints/email/post_email_subscription_status/
    fa_icon: fas fa-at
  - name: "POST : Supprimer les rebonds élevés"
    link: /docs/api/endpoints/email/post_remove_hard_bounces/
    fa_icon: fas fa-reply-all
  - name: "POST : Supprimer les courriers indésirables"
    link: /docs/api/endpoints/email/post_remove_spam/
    fa_icon: fas fa-envelope-open
  - name: "POST : Ajouter l’e-mail à la liste noire"
    link: /docs/api/endpoints/email/post_blacklist/
    fa_icon: fas fa-envelope-open
---
{% comment %}
redirect from email_sync.md
{% endcomment %}
