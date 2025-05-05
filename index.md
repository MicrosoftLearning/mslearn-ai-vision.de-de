---
title: Entwickeln von Lösungen für maschinelles Sehen in Azure
permalink: index.html
layout: home
---

Die folgenden Übungen sollen Ihnen eine praktische Lernerfahrung bieten, in der Sie häufige Aufgaben erkunden, die Entwickelnde beim Erstellen von Lösungen für maschinelles Sehen auf Microsoft Azure ausführen.

> **Hinweis**: Um die Übungen durchführen zu können, benötigen Sie ein Azure-Abonnement, das Ihnen ausreichende Berechtigungen und Kontingente für die Bereitstellung der erforderlichen Azure-Ressourcen und generativen KI-Modelle bietet. Wenn Sie noch kein Konto haben, können Sie sich für ein [Azure-Konto](https://azure.microsoft.com/free) anmelden. Für neue Benutzende gibt es dort eine kostenlose Testoption, die Guthaben für die ersten 30 Tage beinhaltet.

## Übungen

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions/Labs'" %} {% for activity in labs  %}
<hr>
### [{{ activity.lab.title }}]({{ site.github.url }}{{ activity.url }}) {{activity.lab.description}} {% endfor %}

> **Hinweis**: Diese Übungen können zwar eigenständig absolviert werden, sie sind jedoch als Ergänzung zu den Modulen auf [Microsoft Learn](https://learn.microsoft.com/training/paths/create-computer-vision-solutions-azure-ai/) gedacht, in denen Sie tiefer in einige der zugrunde liegenden Konzepte eintauchen können, auf denen diese Übungen basieren.
