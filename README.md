# Nudge Reducer

Le Reducer permet de filtrer les messages en provenance des agents Nudge.  
Cela permet d'alléger ces messages pour faciliter le traitement du serveur Nudge en cas de charge trop importate.


### Structure du Reducer

- **nudge-reducer-1.0.0-uber.jar** : le programme à proprement parlé 
- **start-reducer.sh** : le script de démarrage (doit être positionné à côte du .jar)
- **nudge-reducer.properties** : le fichier de configuraiton (doit être positionné le répertoire `~/.nudge`)

[Téléchargement des fichiers](https://github.com/atakama/nudge-reducer-doc/releases) 

#### Pré-requis

- Java 11


### Démarrer le Reducer

- Pour démarrer le reducer, il faut exécuter le script de démarrage `start-reducer.sh` qui doit être dans le même 
répertoire que le jar `nudge-reducer-1.0.0-uber.jar`  

``` 
./start-reducer.sh
``` 

- le reducer crée un sous répertoire `logs` et écrit ses traces dans le fichier `nudge-reducer.log`  
Ce fichier de log suit une rotation quotidienne.  


### Configuration
##### Configuration Reducer
Propriétés disponibles dans le fichier **nudge-reducer.properties** qui doit être positionné dans le répertoire `~/.nudge/`

| Propriété | Description | Rechargeable à chaud |
| ----------- | ----------- | ----------- |
| http.port | port d'écoute du reducer pour la réception des messages des agents Nudge | non |
| reduction.percentage | Pourcentage de réduction des messages des agents Nudge | oui |
| disable.jmx | true : filtre les éléments jmx des messages des agents Nudge, false : les garde | oui |
| disable.profiling | true : filtre les éléments du profiling des messages des agents Nudge, false : les garde | oui |
| nudge.host | hostname ou ip du serveur Nudge pour réceptionner les messages filtrés | oui |
| nudge.port | port d'écoute du serveur Nudge pour réceptionner les messages filtrés | oui |
| nudge.ssl | true : si le protocole de sécurité est actif sur le serveur Nudge, false : si il n'y a pas de protocole de sécurité actif | oui |


##### Configuration JVM
A travers le script de démarrage du reducer **start-reducer.sh**, il est possible de configurer la mémoire de la JVM et la configuration JMX.  
Par exemple ce-dessous, le reducer est configuré avec une heap à 2Go au maximum et une écoute JMX sur le port **9010** sur le host **sample.atakama-technologies.com**
```$bash
#!/usr/bin/env bash
set -euo pipefail

RMI_HOST=sample.atakama-technologies.com

java -Xms500m -Xmx2G \
	-Dvertx.logger-delegate-factory-class-name=io.vertx.core.logging.SLF4JLogDelegateFactory \
	-Dcom.sun.management.jmxremote \
	-Djava.net.preferIPv4Stack=true \
	-Djava.rmi.server.hostname=${RMI_HOST} \
	-Dcom.sun.management.jmxremote.local.only=false \
	-Dcom.sun.management.jmxremote.port=9010 \
	-Dcom.sun.management.jmxremote.rmi.port=9010 \
	-Dcom.sun.management.jmxremote.authenticate=false \
	-Dcom.sun.management.jmxremote.ssl=false \
	-jar nudge-reducer-1.0.0-uber.jar &

```



