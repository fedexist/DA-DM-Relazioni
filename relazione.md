% Esercitazione 1

# Il valore di $\pi$ : stima e confidenza

## Stima del valore di $\pi$
La prima parte dell'esercitazione verte sulla ricerca del valore di $\pi$ modellando tale valore come la probabilità $P$ che, all'interno di un riferimento cartesiano con origine nel centro di un cerchio di raggio unitario inscritto in un quadrato, un punto di coordinate $(x, y), \forall x,y \in (0,1)$, si trovi o meno all'interno dell'area del cerchio.
Infatti, si ha che: $P=\frac {\pi}{4}$, da cui possiamo ottenere $\pi= 4P$ e possiamo dedurre che, conoscendo il valore della probabilità $P$, possiamo ricavare il vero valore di $\pi$.
Si è proceduto, quindi, a generare casualmente una quantità fissata $n$ di punti, memorizzati in un vettore apposito
```Matlab
point = rand(2,n); % vettore 2xn, ogni colonna è un punto
```
dal quale procediamo, poi, a contare il numero di punti che hanno distanza dall'origine $\leq 1$
```Matlab
inside = sum(sum(point.*point) <= 1);
```
per poi utilizzare tale valore per calcolare il valore di $\pi$ che volevamo trovare. 
```Matlab
empirical_pi = inside/n;
```
È importante evidenziare che tale risultato non è altro che una stima, a cui siamo costretti a ricorrere per la natura finita di un singolo esperimento (quantità fissata di campioni $n$) e della realtà stessa.

## Teorema di Čebyšëv: accuratezza e confidenza

Per quanto detto sopra, possiamo ripetere l'esperimento un numero $m$ di volte, generando per ogni $m$-esimo ciclo un nuovo set di punti da utilizzare per stimare $\pi$.
In questa nuova situazione, possiamo analizzare la relazione che sussiste tra l'accuratezza e la confidenza, relazione evidenziata nella disuguaglianza di Čebyšëv:

$\Pr\left(|{\bar{x}-\mu}| \ge \epsilon\right)\le \;\delta$

con

$\delta = \frac{\sigma^2}{n\cdot\epsilon^2} \Leftrightarrow \epsilon = \sqrt{\frac{\sigma^2}{n\cdot\delta}}$

che può essere riscritta

$\Pr\left(|{\bar{x}-\mu}| \le \epsilon\right)\ge \;1-\delta$


# Stima di una funzione: regressore, dataset ausiliari, analisi dell'errore al variare di complessità e di $\lambda$

La seconda sezione dell'esercitazione è incentrata, invece, sull'identificazione e ottimizzazione di estimatori di funzione e all'analisi del trade-off tra complessità ed errore in funzione del rumore e della quantità di dati a nostra disposizione.
Abbiamo quindi iniziato l'esercitazione generando la *vera funzione*.
```Matlab
xx = linspace(0,1,1000)';
yy = xx.^2;
```
Successivamente abbiamo definito x come un vettore contenente una campionatura randomica lineare in contrasto con la x vera ovvero quella equispaziata lineare usato per la funzione vera
```Matlab
x = rand(n,1)
```
dove n è la cardinalità del data set, generiamo quindi il data set simulando la funzione vera $y = x^2$ aggiungendovi un rumore gaussiano con media 0 e varianza $\sigma$.
```Matlab
y = x.^2 + sigma*randn(size(x));
```

Presupponendo quindi di costruire un estimatore polinomiale $\hat{y_p}= \sum_{i=0}^{p} c_i x^i$ dove $p$ è la complessità scelta dello stesso.
Conoscendo, dunque, il vero sistema, abbiamo deciso di provare $p$ fra $0$ e $3$ per vedere la relazione tra l'errore dell'estimatore e il numero di campioni a disposizione per ognuna di tali complessità.

Necessitiamo quindi di $c_0, c_1, c_2$ e $c_3$, che  calcoliamo tramite
 $c_i = (X_i^TX_i)^+ X_i^T y$
 
$X_i =  \begin{bmatrix}
  1 & x & x^2 & \cdots & x^i \\
  1 & x & x^2& \cdots & x^i\\
\vdots & \vdots& \vdots&\ddots&\vdots\\
  1 & x & x^2 & \cdots & x^i
 \end{bmatrix}$
 
Trovati gli stimatori per ogni $p$,
 
```Matlab
A = ones(size(xx));
yy0 = A*c0;
A = [A,xx];
yy1 = A*c1;
A = [A,xx.^2];
yy2 = A*c2;
A = [A, xx.^3];
yy3 = A*c3;
```

calcoliamo per ognuno l'errore rispetto alla funzione vera:

```Matlab
e0 = sqrt(mean((yy-yy0).^2));
e1 = sqrt(mean((yy-yy1).^2));
e2 = sqrt(mean((yy-yy2).^2));
e3 = sqrt(mean((yy-yy3).^2));
```
Possiamo così analizzare come al variare della dimensione del dataset e della quantità del rumore, cambi la complessità ottima del nostro stimatore.

Mantenendo costante la $\sigma$ a $10$ e ponendo la cardinalità del dataset a $10$ abbiamo che l'errore minimo è dato dal modello costante.

Con $n = 5000$, il regressore lineare è quello ottimale.
Con $n = 10000$, diviene ottimo il modello quadratico.

Mentre per nessun $n$ finito può venire che il modello di terzo grado sia migliore di quello di secondo.

Abbiamo quindi concluso che, all'aumentare dei dati, la complessità dello stimatore aumenta fino a raggiungere il massimo nel grado della funzione vera.

Mantenendo costante la dimensione del dataset a $100$ e ponendo la varianza dell'errore a $10$ abbiamo che l'errore minimo è dato dal modello costante.

Con $\sigma = 2$, il regressore quadratico è quello ottimale.

Sicché, abbiamo inferito che, al diminuire del rumore, si riscontra quanto già evidenziato all'aumentare dei dati.

Avendo queste informazioni sul minimo dobbiamo però considerare che a noi manca la vera funzione tramite cui calcolare l'errore e trovare quindi la complessità ottima.
Per ciò dobbiamo costruirci il nostro stimatore dell'errore...TBC
