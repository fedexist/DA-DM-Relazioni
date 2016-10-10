% Esercitazione 1

# Il valore di $\pi$ : stima e confidenza

## Stima del valore di $\pi$
La prima parte dell'esercitazione verte sulla ricerca del valore di $\pi$ modellando tale valore come la probabilità $P$ che, all'interno di un riferimento cartesiano con origine nel centro di un cerchio di raggio unitario inscritto in un quadrato, un punto di coordinate $(x, y), x,y \in (0,1)$, si trovi o meno all'interno dell'area del cerchio.
Infatti, si ha che: $P=\frac {\pi}{4}$, da cui possiamo ottenere $\pi= 4P$ e possiamo dedurre che, conoscendo il valore della probabilità $P$, possiamo ricavare il vero valore di $\pi$.
Si è proceduto, quindi, a generare casualmente una quantità fissata $n$ di punti, memorizzati in un vettore apposito
```Matlab
%% vettore 2 x n, ogni colonna è un punto
point = rand(2,n); 
```
dal quale procediamo, poi, a contare il numero di punti che hanno distanza dall'origine $\leq 1$
```Matlab
inside = sum(sum(point.*point) <= 1);
```
per poi utilizzare tale valore per calcolare il valore di $\pi$ che volevamo trovare. 
```Matlab
empirical_pi = 4 * inside/n;
```
È importante evidenziare che tale risultato non è altro che una stima, a cui siamo costretti a ricorrere per la natura finita di un singolo esperimento (quantità fissata di campioni $n$) e della realtà stessa.

\pagebreak

## Teorema di Čebyšëv: accuratezza e confidenza

Per quanto detto sopra, possiamo ripetere l'esperimento un numero $m$ di volte, generando per ogni $m$-esimo ciclo un nuovo set di punti da utilizzare per stimare $\pi$.
In questa nuova situazione, possiamo analizzare la relazione che sussiste tra l'accuratezza e la confidenza, relazione evidenziata nella disuguaglianza di Čebyšëv:

$\Pr\left(|{\bar{x}-\mu}| \ge \epsilon\right)\le \;\delta$

con

$\delta = \frac{\sigma_x^2}{n\cdot\epsilon^2} \; \; \; \epsilon = \sqrt{\frac{\sigma_x^2}{n\cdot\delta}}  \; \; \; \sigma_x^2 = varianza\,di\,x$

che può essere riscritta come

$\Pr\left(|{\bar{x}-\mu}| \le \epsilon\right)\ge \;1-\delta$

Quindi, per effettuare la nuova analisi abbiamo bisogno di $\sigma^2_x$, per poter ricavare $\epsilon$. Per fare questo, maggioriamo il suo valore sfruttando la definizione della varianza:

$\sigma^2_x = E_x\{x^2\} - \mu^2 \le\mu-\mu^2=\mu ( 1 - \mu ) \le \frac{1}{4}$

Tale maggiorazione è giustificata dal fatto che:

- $x^2 \le x \in [0,1] \implies E_x\{x^2\} \le E_x\{x\} = \mu$
- $\mu$ è una variabile aleatoria $\in [0,1]$, per cui $\sigma^2_x$ ha come valore medio $\frac{1}{2}$ (ndr. non necessario, e forse neanche corretto)
- $\sigma^2_x (\mu) =\mu ( 1 - \mu )$ è una funzione concava che ha come massimo $\mu = \frac{1}{2} \implies \sigma^2_x = \frac{1}{4}$

Possiamo, quindi, fissato un $\delta$ obiettivo, trovare $\epsilon$ e usarlo per calcolare la vera accuratezza del risultato degli $m$ esperimenti:

```Matlab
delta = 0.05;
m = 1000000;
sigma2 = 1/4;
epsilon = sqrt(sigma2/(n*delta));
%% All'interno del ciclo, per ogni esperimento da 1 a m
    if (abs(empirical_pi - pi) > 4*epsilon)
        true_delta = true_delta + 1;
    end
%% Fuori dal ciclo
true_delta = true_delta / m;
```
Tuttavia, si ottiene sempre che $\delta_{true} = 0$, questo perché le condizioni imposte da Čebyšëv sono troppo conservative e il vero $\delta$ è molto più piccolo di quello stimato. Intuiamo, quindi, che Čebyšëv non è sufficiente 
per effettuare una stima abbastanza veritiera del $\delta$ e per questo si introduce la Disuguaglianza di Hoeffding:

$\Pr\left(|{\bar{x}-\mu}| \ge t\right)\le \;2e^{-2nt^2} = \delta \implies \epsilon = t = \sqrt{\frac{log(\frac{2}{\delta})}{2n}}$

Per cui, il delta stimato imponendo `epsilon = sqrt(log(2/delta)/(2*n))` è molto più vicino al $\delta$ vero.


\pagebreak

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