Le `:` séparateur ne fonctionne pas avec les clés hiérarchiques de variable d’environnement sur toutes les plateformes. `__`, le double trait de soulignement, est :

* Pris en charge par toutes les plateformes. Par exemple, le `:` séparateur n’est pas pris en charge par [bash](https://linuxhint.com/bash-environment-variables/), mais `__` est.
* Automatiquement remplacé par un `:`