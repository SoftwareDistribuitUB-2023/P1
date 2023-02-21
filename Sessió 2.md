Sessió 2
========

En aquesta sessió es comparteix l'explicació del joc i la definició del protocol. Llegiu curosament aquesta definició i pregunteu qualsevol dubte al professor de pràctiques.

En el codi base de la sessió 0, el qual envia un missatge bàsic, s'usa la funció _writeUTF()_ tal qual. En canvi, ara que tenim el protocol, haurem de codificar les trames a bytes i descodificar-les un cop rebudes. Per a fer-ho, es compartirà, mitjançant el campus virtual, la classe Utils.java amb les funcions bàsiques de comunicació mitjançant Sockets. Aquesta classe serà usada tant per Client i Servidor. 

## Tasques per a la següent sessió

Enviament de trames HELLO, READY, PLAY i ADMIT. Per a fer-ho, una idea d'organització de classes podria ser la següent:

 - Server:
 	- Server.java: Inicialitza el socket, utils i del·lega la partida a GameHandler.java
 	- GameHandler.java: Inicialització de les variables del joc i de la classe GameProtocol.java. Mètode _play()_ on es gestiona en tot moment els estats del joc.
 	- GameProtocol.java: Classe que conté els "senders()" i "receivers()" del protocol, com per exemple, _sendReady()_ o _receiveHello()_. 

 - Client:
 	- Client.java: Inicialitza el socket, utils i del·lega la partida a GameClient.java
 	- GameClient.java: Inicialització dels atributs pròpis de Client i de la classe CleintProtocol.java. Mètode _play()_ on es gestiona en tot moment els estats del joc.
 	- ClientProtocol.java: Classe que conté els "senders()" i "receivers()" del protocol, com per exemple, _receiveReady()_ o _sendHello()_. 
