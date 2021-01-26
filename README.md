# Test eseguiti con STM32F303K8

Per prima cosa creare un nuovo progetto "STM32"  e selezionare la board da programmare, in questo caso la STM32F303K8 lasciando i valori di default e lasciando il `c` come linguaggio di programmazione.

## Blink del Led integrato

Per prima cosa fare riferimento al datasheet della board per andare a vedere quale è il pinout associato al led, in questo caso `PB3`. Successivamente andare nel file `.ioc` per modificare i pinout della stessa, quindi assicurandosi di essere su Pinout & Configuration andare a cliccare sopra `PB3` e selezionare **GPIO_Output**.

Dopodichè nella tabella di sinistra, sotto la voce System Core, cliccare su GPIO per configurare l'uscita.  Cliccando su quella che per ora è l'unica uscita disponibile, appariranno sotto le voci modificabili impostando come Label **LED**.


Una volta impostato il pinout, dobbiamo scrivere il codice, per fare ciò andiamo nel file `.c` scorrendo fin dentro il `while(1)`  fino a trovarci a scrivere dentro `/* USER CODE BEGIN 3 */`.
La funzione che ci servirà è:

`void HAL_GPIO_WritePin(GPIO_TypeDef* GPIOx, uint16_t GPIO_Pin, GPIO_PinState PinState){}` che richiede tre variabili:


 - *GPIO_TypeDef* GPIOx* - il nome del pinout associato a label, in questo caso scriveremo: **LED_GPIO_Port**
 - *uint16_t GPIO_Pin* - Qui scriveremo **LED_Pin** che è associato nel main.h
 - *GPIO_PinState PinState* - Qui scriveremo **1** / **0** a seconda di come vogliamo il led, rispettivamente acceso o spento.

Esempio di accensione e spegnimento:




    HAL_GPIO_WritePin(LED_GPIO_Port, LED_Pin, 1);
    HAL_Delay(150);
    HAL_GPIO_WritePin(LED_GPIO_Port, LED_Pin, 0);
    HAL_Delay(150);
