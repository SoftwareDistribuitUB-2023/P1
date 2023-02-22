
DAN DAN DISH
============

DAN DAN DISH neix d'una modificació del joc "Pedra-paper-tisores" on dues persones intenten agafar desprevingut al rival per a "disparar-lo". En el DAN DAN DISH només hi ha tres possibles accions:

* Recarregar
* Bloquejar
* Disparar

Originalment es juga de manera síncrona, cara-a-cara, on l'acció de bloquejar s'indica fent una creu sobre el pit amb els braços, la de disparar elevant les mans al cap (deixant a descobert el pit) i la de disparar simulant una pistola.

El transcurs del joc va així: al principi, cap dels dos jugador té "bales a la recambra". Per tant, l'acció de disparar és il·legal. Per afegir bales, el jugador ha de decidir dur a terme l'acció de recarregar. Per facilitar les coses, la pistola pot admetre tantes bales com faci falta. L'acció de bloqueig es pot dur a terme en qualsevol estat i evita "morir" quan l'altre jugador decideix disparar. El joc s'acaba quan un dels jugadors decideix fer l'acció de "disparar" i l'altre, a la vegada, decideix recarregar. El jugador que ha disparat és el guanyador. 

Atenció! Si les dos persones disparen a la vegada, els dos sobreviuen i es segueix el joc. En el cas de que ambdós recarreguin, també.

Missatges
=========
El client (c) i el servidor (s) suporta  tipus de missatges amb els
següents codis d'operacions:

Message| Code | Direcció
-------|------|--------
HELLO    |1| C-S
READY    |2| S-C
PLAY     |3| C-S
ADMIT    |4| S-C
ACTION   |5| C-S
RESULT   |6| S-C
ERROR    |8| S-C


La capçalera d'un missatge conté el codi d'operació associat amb aquest paquet i els paràmetres necessaris. Els tipus de dades de les capçaleres es detallen a continuació:
-  Els camps de tipus **string** representen cadenes de bytes codificats en UTF8 (2 bytes) de JAVA acabat amb un últim byte 0 que és un byte en format de xarxa (Big Endian)). 
-  Així mateix, els camps amb tipus d'un o diversos bytes, aquests **bytes** són bytes en format de xarxa (Big Endian). 
-  Els camps de tipus **int32** és un sencer de 32 bits (4 bytes) en format xarxa 

Les trames o paquets es detallen a continuadció:
-   El paquet **HELLO** (codi d'operació 1) té el format que es mostra en la Figura 1, on *id* és un **int32** i on *Name* és el nom del jugador com a **string**

                                 1 byte    int32   string    1 byte     
                                -----------------------------------
                                | Opcode |  id   |  Name    |  0  |
                                -----------------------------------
                                    Figura 1: Missatge HELLO
 
- El paquet **READY** (codi operació 2) té el format que es mostra en la Figura 2, on *id* és un **int32**

                                 1 byte    int32       
                                ----------------------------------
                                | Opcode |  id    | 
                                ----------------------------------
       
                                    Figura 2: Missatge READY

- El paquet **PLAY** (codi operació 3) té el format que es mostra en la Figura 3, on *id* és un **int32**.
   
                                  1 byte    int32       
                                ----------------------------------
                                | Opcode |  id | 
                                ----------------------------------
                                    Figura 3: Missatge PLAY



- El paquet **ADMIT** (codi operació 4) té el format que es mostra en la Figura 4, on *bool* és un byte en format xarxa on 0 vol dir no admès i 1 vol dir admès. 

                                  1 byte    1 byte       
                                ----------------------------------
                                | Opcode |  bool    | 
                                ----------------------------------
                                    Figura 4: Missatge ADMIT


- El paquet **ACTION** (codi operació 5) té el format que es mostra en la Figura 5, on *Action* és una paraula com a una cadena de 5 caràcters en format UTF (2 bytes)


                                  1 byte    5*2 bytes       
                                ------------------------
                                | Opcode |  Action  | 
                                ------------------------
                                    Figura 5: Missatge WORD

- El paquet **RESULT** (codi operació 6) té el format que es mostra en la Figura 6, on *Result* és un codi com a una cadena de 5 caràcters en format UTF (2 bytes)


                                  1 byte    5*2 bytes        
                                --------------------------
                                | Opcode |  Result   |  
                                --------------------------
                                    Figura 6: Missatge RESULT


- El paquet **ERROR** (codi operació 8) té el format que es mostra en la Figura 8, on *ErrCode* és és un byte en format xarxa  i on *Msg* és un codi com a string.
  

                                1 byte    1 byte      string  1 byte
                                -------------------------------------
                                | Opcode |  ErrCode    | Msg | 0
                                -------------------------------------
                                    Figura 8: Missatge ERROR



                 
Els possibles missatges d'error són:

- ERRCODE: 1, MSG: CARÀCTER NO RECONEGUT
- ERRCODE: 2, MSG: MISSATGE DESCONEGUT
- ERRCODE: 3, MSG: MISSATGE FORA DE PROTOCOL
- ERRCODE: 4, MSG: INICI DE SESSIÓ INCORRECTE
- ERRCODE: 5, MSG: PARAULA DESCONEGUDA
- ERRCODE: 6, MSG: MISSATGE MAL FORMAT
- ERRCODE: 99, MSG: ERROR DESCONEGUT

Protocol
========
La partida la inicia el client amb una comanda **HELLO**. El client genera l'id usant la funció _Random()_ i serà de cinc dígits. No pot començar per zero. 

C -----HELLO (id, name)-----> S

De la mateixa manera, el Servidor contestarà una comanda **READY** amb el mateix ID. En cas contrari s'enviarà un **ERROR** d'*Inici de Sessió Incorrecte*.  

C <------READY (id) --------- S

A continuació el client pot començar la partida amb un **PLAY**, passant l'ID de sessió rebut anteriorment. 

C -----PLAY (id)-----> S

El Servidor comprovarà que aquest id sigui el mateix que es va enviar anteriorment i contestarà amb un **ADMIT** juntament amb un 1 si és correcte o amb un 0 si no ho és. A continuació, el servidor triarà el seu moviment (recordeu que no es pot disparar en el primer moviment ja que no hi ha bales a la recambra) però no el compartirà amb el client. Després, envia:
 
C <------ADMIT (bool) --------- S

A continuació el Client enviarà el seu moviment **ACTION**: 

C -----ACTION (Word)-----> S

Aquest moviment estarà representat per una paraula de 5 lletres:

| ACTION | Moviment   |
|--------|------------|
| BLOCK  | Bloquejar  |
| CHARG  | Recarregar |
| SHOOT  | Disparar   |

El Servidor comprovarà que el moviment rebut sigui vàlid. En cas que el moviment no existeixi es retornarà un **ERROR** de *Moviment Desconegut*. En cas contrari, s'enviarà un **RESULT**

C <------ RESULT (Result, Flag) --------- S

Aquest resultat pot ser:

| RESULT | Flag | Signficat                             |
|--------|------|---------------------------------------|
| ENDS   | 0    | Guanya Servidor                       |
| ENDS   | 1    | Guanya Client                         |
| PLUS   | 0    | Servidor ha pogut recarregar una bala |
| PLUS   | 1    | Client ha pogut recarregar una bala   |
| DRAW   | 0    | Ambdós jugadors han disparat          |
| SAFE   | 0    | Servidor ha bloquejat una bala        |
| SAFE   | 1    | Client ha bloquejat una bala          |

"Flag" està il·lustrat com una columna per a que s'entengui millor, però si us fixeu al protocol, la trama és de 10 bytes, per tant, aquest "flag" anirà dintre de "result".

No tots els results són possibles després de certes actions. Penseu bé quins escenaris planteja cada "action" i programeu el "result" corresponent. Òbviament, servidor no pot cambiar la seva resposta després de rebre "l'action" de client. 

A partir d'aquí es seguirà jugant fins que un dels dos perdri.

Els missatges d'error es podran enviar en qualsevol moment que el Servidor detecti alguna situació estranya durant l'execució de la partida. Quan el Client rebi un error l'haurà de mostrar per pantalla. El servidor davant dels errors no prendrà cap decisió, simplement es quedarà en l'estat actual esperant un nou missatge del client. El client segons sigui l'error pot o bé tornar a enviar un missatge o tancar la connexió amb el servidor.

C <------ ERROR (ErrCode,Msg) --------- S


Un cop s'hagi acabat una partida, es podrà tornar a jugar una nova partida en la mateixa sessió, per fer això caldrà enviar el missatge **PLAY** 

C ----- PLAY (id)-----> S

El client pot acabar quan vulgui les partides, només cal que es desconnecti. 

   
Exemple partida
===============

Exemple de partida. Els espais s'han posat per clairificar els missatges, però en la trama no hi són.


    C- [TCP Connect]
    S- [TCP Accept]
    
    //Client wins

    HELLO  C -------1 17845 Blai0--> S
    READY  C <------2 17845 --------- S
    PLAY   C -------3 17845 --------> S
    ADMIT  C <------4 1 ------------- S
    ACTION C -------5 CHARG --------> S
    RESULT C <------6 PLUS0 --------- S
    ACTION C -------5 BLOCK --------> S
    RESULT C <------6 PLUS0 --------- S
    ACTION C -------5 SHOOT --------> S
    RESULT C <------6 ENDS1 --------- S
    
    //Juguen un altre cop. Client intenta recarregar dos cops i Servidor dispara & guanya.

    PLAY   C -------3 17845 --------> S
    ADMIT  C <------4 1 ------------- S
    ACTION C -------5 CHARG --------> S
    RESULT C <------6 PLUS0 --------- S
    ACTION C -------5 CHARG --------> S
    RESULT C <------6 ENDS0 --------- S

    //Juguen un altre cop. Un bug al codi de Client fa que dispari sense bales.
    //Servidor envia error i segueixen jugant. Client intenta recarregar dos cops i Servidor dispara & guanya.  
    PLAY   C -------3 17845 --------> S
    ADMIT  C <------4 1 ------------- S
    ACTION C -------5 CHARG --------> S
    RESULT C <------6 PLUS0 --------- S
    ACTION C -------5 SHOOT --------> S
    RESULT C <------6 DRAW0 --------- S
    ACTION C -------5 SHOOT --------> S
    ERROR  C <------8 3,"Missatge fora de protocol"0 -------- S
    ACTION C -------5 CHARG --------> S
    RESULT C <------6 PLUS0 --------- S
    ACTION C -------5 CHARG --------> S
    RESULT C <------6 ENDS0 --------- S
    
    //Juguen un quart cop. Servidor i Client es bloquejen varies bales
    //Guanya Client al final
    PLAY   C -------3 17845 --------> S
    ADMIT  C <------4 1 ------------- S
    ACTION C -------5 CHARG --------> S
    RESULT C <------6 PLUS0 --------- S
    ACTION C -------5 SHOOT --------> S
    RESULT C <------6 SAFE0 --------- S
    ACTION C -------5 BLOCK --------> S
    RESULT C <------6 SAFE1 --------- S
    ACTION C -------5 CHARG --------> S
    RESULT C <------6 PLUS1 --------- S
    ACTION C -------5 SHOOT --------> S
    RESULT C <------6 ENDS1 --------- S
    
    C- [conexion closed]
    S- [conexion closed]

Heurístiques
============

L'objectiu de la pràctica és programar un Client & Servidor funcional amb els que es pugui jugar al joc sense errors. Això implica que, de primeres, feu un Servidor "naive" que trii una acció (correcte) "hardcodejada". De totes maneres, es reconeixerà de manera molt positiva l'implementació d'heurístiques que permetin al Servidor triar una acció segons el context de la partida. No estem parlant d'Intel·ligències Artificials ni models complexs de Machine Learning, simplement d'una sèrie de raonamanets simples. Per exemple, té sentit bloquejar a la primera jugada?

Multi-threading
===============

Un cop s'expliqui a teoria el multi-threading, Servidor s'haurà de canviar per a soportar diferents Clients simultàniament, és a dir, jugar diferents partides a la vegada. 

Mode 2 jugadors
================
També s'haurà de poder fer que juguin dos jugadors, fent el servidor de proxy entre els dos jugadors. 
