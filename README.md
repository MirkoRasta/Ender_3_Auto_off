# Ender_3_Auto_off
System for automatic shutdown at the end of the printing

______________
Italiano:

Questa pagina nasce dalla necessità di poter spengere la stampante al termine della stampa in maniera più elegante rispetto al comune switch posto in serie all'alimentazione, azionato durante la presentazione nella parte finale, impedendo all'hotend di raffreddarsi correttamente.

Il progetto è nella sua prima versione, è probabile che nel futuro verranno apportate delle modifiche per perfezionarne il funzionamento.

MI RACCOMANDO attenzione alla 230V che pizzica.
_ _ _ _ _

Per la parte hardware servono almeno:
1) Un relè 24VDC con un contatto NO (normalmente aperto) da una decina di Ampere. (Nel mio caso un Finder 40.51.9.024.0000).
2) Un MOSFET a canale N ad arricchimento (io ho usato un IRF540 perché avevo quello, ma ne va bene uno qualunque, basta che abbia una corrente di canale maggiore della corrente necessaria alla bobina del relè che per il mio relè era sui 30mA).
3) Un fotoaccoppiatore (io ho usato un PC817, sempre perche avevo quello, vanno bene un pò tutti, basta che siano NPN).
4) Un diodo di ricircolo per la bobina del relè (es: 1N4148 o simili).
5) Una resistenza da 100KOhm o là di lì.
6) Una resistenza da 270Ohm o là di lì (in questo caso non troppo più in là però).
7) Un pulsante NO (normalmente aperto, ormai avete imparato) da una decina di Ampere.
8) Qualche pezzetto di filo, un saldatore, qualche capocorda...

Lo schema di collegamento lo trovate nelle foto, appena riesco ne carico anche uno semplificato.

Una volta creato il circuito si deve collegare alla macchina, che non è banale.

La mia Ender 3 è equipaggiata con una "SKR mini e3 v1.2" che (OVVIAMENTE) non presenta lo spinotto PS_ON per l'accensione dell'alimentatore. 
Ho risolto utilizzando la presina "NEOPIXEL" credo destinata al pilotaggio dei LED RGB (chi mette i LED RGB su una Ender 3?).
Bisogna innanzitutto spostare il jumper chiamato "Power Choose" nella posizione 5V, (quella più esterna, se sbagliate comunque al massimo rompete il fotoaccoppiatore, ma non è nemmeno detto).
A questo punto "basta" fare un cavetto con il connettore HX2.54 con due fili (pin centrale e pin esterno) che vada direttamente al fotoaccoppiatore tramite la resistenza. Volendo uno può anche utilizzare un pezzo di cavo di un endstop se non si vuole cimentare nella realizzazione del cavetto su misura.
_ _ _ _
Per la parte software invece basta fare un paio di modifiche al firmware, allego anche gli screen:
1) Verso la riga 330 del file "Configuration.h" bisogna decommentare il #define PSU_CONTROL e lasciare il resto com'è.
2) Verso la riga 28 del file "pins_BTT_SKR_MINI_E3_V1.2.h" che si trova nella cartella "stm32f1" all'interno della cartella "pins" (ricapitolando: Marlin\src\pins\stm32f1\pins_BTT_SKR_MINI_E3_V1.2.h) bisogna commentare la riga " #define NEOPIXEL_PIN PC7 ", al fine di liberare il pin PC7 del processore e utilizzarlo per i nostri scopi.
3) in "pins_BTT_SKR_MINI_E3_common.h" (Marlin\src\pins\stm32f1\pins_BTT_SKR_MINI_E3_common.h) basta aggiungere (più o meno ovunque, io l'ho messo alla riga 44, prima di #define SERVO0_PIN) la riga " #define PS_ON_PIN PC7 "

Fatto, a questo punto basta flashare il nuovo firmware e inserire alla fine di ogni G-Code l'istruzione M80, chiaramente dopo aver aspettato che la temperatura sia scesa sotto la soglia che volete voi con le apposite istruzioni.
