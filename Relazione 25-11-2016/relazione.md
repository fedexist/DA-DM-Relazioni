% Esercitazione 4
% Edoardo Ferrante; Federico D'Ambrosio

# Classificazione multiclasse con alberi decisionali e foreste

## Alberi decisionali

Gli alberi decisionali sono grafi che sono usati per la classificazione attraverso l'uso di tagli, operati nei nodi degli stessi, su una features dei campioni del dataset.
Tale feature, scelta per ogni nodo tra tutte le features dei campioni, è quella che fornisce il miglior risultato secondo la nostra metrica, nel nostro caso abbiamo cercato il miglior guadagno entropico G.

$G = H(A) - (\frac{n_{B1}}{n_A} H(B_1) + \frac{n_{B2}}{n_A} H(B_2))$ 

con $H(Y) = - \sum_{i} p_i \log(1-p_i)$ e $p_i$ la probabilità che un elemento di classe $i$ sia all'interno dell'insieme Y.  

### Fase di addestramento

La costruzione di un albero, per il quale l'unico iperparametro è la profondità/altezza, è ricorsiva e, per questo, si comincia dal determinare la condizione di arresto della ricorsione.
Questa non sarà altro che la disgiunzione di due condizioni: 

  - l'altezza dei sotto-alberi ancora da creare è $0$, che significa che siamo arrivati alle foglie, l'altezza massima del nostro albero; 
  - la presenza di elementi di una sola classe all'interno dell'insieme di campioni che consideriamo, che implica che non ci sia più nulla da classificare.

Se siamo giunti ad una situazione di stop, quindi, possiamo classificare tutti i campioni che soddisfano i tagli dettati dal percorso fino alla foglia in esame che, graficamente è un settore squadrato dello spazio, etichettandoli con la classe che è maggiormente presente in tale settore.

\pagebreak

Invece, durante la normale ricorsione della creazione dell'albero si segue la seguente procedura:

  1. $G_{best}$, il guadagno entropico in tale nodo viene inizializzato a $-\infty$;
  2. Per ogni feature i-esima, i campioni vengono ordinati in base a tale feature;
  3. Considerate, ora, le etichette dei campioni ordinati, se due etichette contigue sono diverse, allora, si calcola il guadagno entropico che si avrebbe nel passaggio da uno all'altro;
  4. Se il guadagno appena calcolato, $G$, è maggiore di $G_{best}$, allora $G_{best} = G$ e si effettua un taglio tra i 2 campioni relativi alle 2 etichette che stiamo considerando.

Alla fine di tale procedura, avremo scelto una feature, $f$, la quale sarà la feature migliore possibile, in termini di guadagno di informazione per il nodo corrente, e un taglio relativo a tale feature, $cut$. 
Di conseguenza, possiamo procedere nella ricorsione dividendo tutti i dati in modo tale che vengano creati dei nuovi sottonodi: il figlio destro, che conterrà tutti i dati che hanno $X[f] < cut$, e il figlio sinistro, con $X[f] \ge cut$.
  
### Fase in avanti

Una volta terminata la fase di addestramento, e, quindi, la costruzione dell'albero decisionale, dato un dataset possiamo effettuarne la classificazione semplicemente esplorando l'albero costruito in precedenza:
 
  1. Per ogni campione da classificare, esploro l'albero: se il nodo corrente è una foglia, associo la classe della foglia come etichetta del campione in esame;
  2. altrimenti, se il taglio, associato alla feature del nodo corrente, è maggiore del valore della corrispondente feature del campione, esploro il sotto-albero destro, altrimenti, il sinistro.

\pagebreak
  
### Risultati

Nel caso di classificazione binaria, possiamo vedere che la divisione non è ottimale, visto che i tagli costruiti dall'albero sono delle rette costanti, la divisione dei campioni classificati è in settori squadrati, 
ben lontana dall'ottimale separatore lineare, in termini di accuratezza.

![Classificazione binaria: è evidente che gli alberi decisionali non sono ottimali](graph1.png)

Nel caso di classificazione multi-classe (in particolare, 7 classi), analogamente al caso precedente, notiamo che i settori sono sempre squadrati. Notiamo, inoltre, come ci possiamo ragionevolmente aspettare che, aumentando
o diminuendo l'altezza massima dell'albero, aumenta e diminuisce la frammentazione, e quindi l'accuratezza, dei settori calcolati. Infatti, a una maggiore altezza equivale un maggior numero di tagli.
Nelle immagini sottostanti è possibile notare la differenza tra un'altezza massima uguale a 10, e una uguale a 3.

![Classificazione multiclasse: altezza = 10](graph2.png) \ ![Classificazione multiclasse: altezza = 3](graph3.png)


\pagebreak

## Random forest
Vista la semplicità di implementazione e di uso degli alberi, ricercatori si sono chiesti come utilizzarli per avere un buon classificatore; preso in considerazione il fatto che tanti classificatori indipendenti mediocri, ma con una accuratezza maggiore del $50 \%$ se utilizzati assieme, tramite un sistema di voto, forniscono un nuovo classificatore di accuratezza maggiore;

Infatti se $p_i > 0.5$ per $i \in \{0,1,...,n\}$ e il risultato è scelto tramite moda allora la probabilità di errare (in un caso di classificazione binaria) è data dalla probabilità che errino $\lfloor{n/2}\rfloor+1$ classificatori $p_j$ con $j\in C$ e $C \subset{\{0,1,...,n\}}$ è uguale a $\prod\limits_{j}(1-p_j)$ un numero decrescente nella dimensione di $n$.

Vari studi indipendenti portarono alla nascita del random forest, un algoritmo che consente per l'appunto di classificare dei campioni servendosi di alberi casuali in gran quantità.

L'algoritmo inizia con la creazione degli alberi di decisione ai quali viene dato per l'addestramento un sottoinsieme scelto a caso dei dati e per il quale vengono prese in considerazione per il taglio un sottoinsieme delle variabili anche esso preso a caso (nel nostro caso scegliamo $\sqrt{d}$ dove $d$ è la dimensionalità), ciò per generare alberi il più possibile indipendenti fra di loro.
Inoltre a differenza degli alberi visti singolarmente non metteremo un'altezza massima, in questo modo genereremo alberi ancora meno omogenei.

Addestrati tutti gli alberi, la fase avanti della foresta è semplicemente data dalla fase avanti su ciascun albero e dal voto (tramite moda) sui risultati di questi.

Non avendo limiti sull'altezza, l'algoritmo di random forest è apparentemente senza iperparametri, tuttavia ve ne sono 5 celati nell'implementazione:

1. Ad ogni albero vengono passati un numero n di campioni, questo n è deciso a priori.
  2. Ogni albero utilizza $\sqrt{d}$ dimensioni per fare il taglio, un'altra scelta arbitraria.
  3. L'utilizzo del guadagno entropico come funzione da massimizzare con il taglio.
  4. Il numero di alberi utilizzati.
  5. Il peso dato ad ogni albero, che noi abbiamo stabilito a 1.

![Dal grafico possiamo vedere che la divisione dei campioni in classi è meno squadrata rispetto a quella vista dai singoli alberi specialmente nella parte centrale dove la la classificazione è più difficile e quindi il voto la rende più frammentata.
](graph4.png)



\pagebreak



## Un caso reale: classificazione di vini

Per testare il potenziale del random forest abbiamo preso un problema reale in cui ci erano forniti i dati chimici di un vino e un etichetta legata ad essi fra tre possibili classi, presumibilmente legate alla qualità del vino, dove $1$ è la qualità minima e $3$ la massima.

Avendo un unico insieme di dati utilizziamo il metodo Montecarlo, quindi n volte ripetiamo questo procedimento: 

1. Dividiamo l'insieme con una permutazione casuale in modo da avere all'incirca il $70\%$ dei dati nell'insieme di addestramento e il $30\%$ in quello di validazione.
2. Addestriamo la foresta sul primo sottoinsieme.
3. Validiamo la foresta sul secondo sottoinsieme.

Quindi su ciascuna iterazione calcoliamo l'accuratezza quindi ne facciamo la media per avere quella finale.

Facendo partire l'algoritmo troviamo un'accuratezza del $93\%$, che ci fa pensare di aver trovato un ottimo modello, tuttavia ci ricordiamo che l'accuratezza da sola è poco utile e quindi calcoliamo anche la varianza mettendo un intervallo di confidenza di $2\sigma$ e troviamo così una varianza del $27\%$ e quindi vediamo che in realtà il modello non è molto buono, e necessitiamo di più dati.

Infine possiamo calcolare la matrice di confusione per vedere, non solo quanto abbiamo sbagliato a classificare, ma anche quali classi abbiamo confuso di più. Per esempio, possiamo notare che campioni di classe $3$ pochissime volte sono stati classificati come appartententi classe $1$.
E, vista la nostra interpretazione delle classi, questo è positivo, perché significa che, anche se sbagliamo, raramente confondiamo vini eccelsi con vini scadenti, ma le uniche incertezze che abbiamo sono sul passaggio fra classe intermedia e classi estreme.
