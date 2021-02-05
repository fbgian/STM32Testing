# Test eseguiti con STM32F303K8

Per prima cosa creare un nuovo progetto "STM32"  e selezionare la board da programmare, in questo caso la STM32F303K8 lasciando i valori di default e lasciando il `c` come linguaggio di programmazione.

## Pinout STM32F303K8 
![Pinout della STM32F303K8](https://os.mbed.com/media/uploads/bcostm/nucleo_f303k8_2017_10_10.png)

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


## Leggere i valori di un sensore analogico
Vedremo l'esempio con un potenziometro.
Per leggere il valore da un sensore per prima cosa dobbiamo inizializzare il pinout **PA0** ad `ADC1_IN1` per poi andare nelle categorie di sinistra alla voce "Analog" e cliccare su `ADC1`, arrivati qui selezionare l'IN* da noi scelto, in questo caso l'1, e impostarlo su single-ended in quanto a noi ci servirà per convertire il segnale di un solo sensore.
A questo punto, una volta generato il codice salvando con `ctrl + s`, torniamo nel `main.c` e notiamo che abbiamo una nuova variabile chiamata `ADC_HandleTypeDef hadc1` che è quella che si occupa del pinout a cui abbiamo collegato il sensore, dopo di essa instanziare una variabile dentro al main `uint16_t raw` che utilizzeremo per immagazzinare il valore.

Il codice da scrivere nel **while** per leggere e convertire i valori del sensore:

	    HAL_ADC_Start(&hadc1);
		HAL_ADC_PollForConversion(&hadc1, HAL_MAX_DELAY);
		raw = HAL_ADC_GetValue(&hadc1); // qui avremo il valore del sensore 
  
  
Se vogliamo vedere il valore sul pc senza dover utilizzare altri strumenti, abbiamo bisogno di un monitor seriale.
## Monitor seriale
Dobbiamo configurare un pinout a **USART2_TX** nel file `.ioc`, con la nostra board il relativo pinout è **PA2**. Fatto ciò andiamo nella categoria "Connectivity" , selezionare **USART2** e cambiare la modalità in **Asynchronous**, i valori sottostanti possiamo lasciarli di default. Torniamo nel `main.c` sempre dopo aver generato il codice e notiamo la nuova variabile **UART_HandleTypeDef huart2;**.
Andiamo nel **main** e instanziamo una variabile `char msg[10];` che utilizzeremo per scrivere il messaggio da visualizzare nel monitor.

All'interno del while scriveremo: 

    sprintf(msg, "%hu\r\n", raw);
	//Mando il messaggio all'uart (usare uart2)
	HAL_UART_Transmit(&huart2, (uint8_t*)msg, strlen(msg), HAL_MAX_DELAY);

Per utilizzare il monitor consiglio di usare il monitor seriale di arduino poichè già configurato. Un errore che ho riscontrato è stato visualizzare valori del tutto differenti da quelli aspettati, per risolvere ho aumentato il **baudrate** nel monitor di arduino a 38400 baud .
