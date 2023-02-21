Sessió 2
========

En aquesta sessió es comparteix l'explicació del joc i la definició del protocol. Llegiu curosament aquesta definició i pregunteu qualsevol dubte al professor de pràctiques.

En el codi base de la sessió 0, el qual envia un missatge bàsic, s'usa la funció _writeUTF()_ i _readUTF()_ tal qual. En canvi, ara que tenim el protocol, haurem de codificar les trames a bytes i descodificar-les un cop rebudes.
Mireu els cotractes de cada funció a [DataInput](https://docs.oracle.com/javase/7/docs/api/java/io/DataInput.html) per saber quina s'ajusta exactament als tipus que hi ha en el protocol.  


## Tasques per a la següent sessió

1. Enviament de trames HELLO, READY, PLAY i ADMIT. Per a fer-ho, una idea d'organització de classes podria ser la següent:
 
 - Utilitats:
	- Util.java: Classe d'utilitats que faran servir tant Client com Servidor. Podeu crear un Util.jar i carregar-lo com a llibreria externa per a Client i Server.
 - Server:
 	- Server.java: Inicialitza el socket, utils i del·lega la partida a GameHandler.java
 	- GameHandler.java: Inicialització de les variables del joc i de la classe GameProtocol.java. Mètode _play()_ on es gestiona en tot moment els estats del joc.
 	- GameProtocol.java: Classe que conté els "senders()" i "receivers()" del protocol, com per exemple, _sendReady()_ o _receiveHello()_. 

 - Client:
 	- Client.java: Inicialitza el socket, utils i del·lega la partida a GameClient.java
 	- GameClient.java: Inicialització dels atributs pròpis de Client i de la classe CleintProtocol.java. Mètode _play()_ on es gestiona en tot moment els estats del joc.
 	- ClientProtocol.java: Classe que conté els "senders()" i "receivers()" del protocol, com per exemple, _receiveReady()_ o _sendHello()_. 

2. Crear la configuració per al vostre IDE per tal de tenir (Podeu fer servir Maven o similars):
   - Projecte Client
   - Projecte Server
   - Llibreria amb el paquet Util.jar
   - Suit de Testing Junit

