% Esercitazione 2
% Edoardo Ferrante; Federico D'Ambrosio

# Regressione tramite i moltiplicatori di Lagrange e il Kernel Lagrangiano

## Retta
Si è cominciata l'esercitazione calcolando il piano, che nel caso bidimensionale è denotato da una retta, che fittasse meglio un dataset formato da $n$ punti distribuiti casualmente tra $-1$ e $1$ tramite il calcolo di $\underline{w}$, la normale di tale piano.

$\min_{w} ||X \underline{w} - \underline{y}||^2 + \lambda || \underline{w} ||^2 \implies \underline{w}^* = ( X^T X + \lambda I )^+ X^T \underline{y}$

Tale formulazione, considerando $\underline{w} = X^T \underline{\alpha} \iff \underline{w} \in \mathbb{R}^d$, con 

 - $\underline{\alpha}$, un vettore di pesi;
 - le componenenti $\underline{x}_i$ della matrice $X$, formanti una base dello spazio $\mathbb{R}^d$ nel caso in esame $d=1$). 
 
può essere sostituita dalla seguente:


$\min_{\underline{\alpha}} ||X X^T \underline{\alpha} - \underline{y}||^2 + \lambda \underline{\alpha}^T X X^T \underline{\alpha}$
$\underline{\alpha} = ( X X^T + \lambda I )^+ \underline{y}$

Come ci si aspetta, le 2 formulazioni sono equivalenti e danno lo stesso risultato, con le stesse proprietà, al variare del parametro $\lambda$ e del rumore $\sigma$, rispetto alla precedente esercitazione: con $\lambda$ molto grande la regressione migliore è una retta costante con ordinata $0$

## Sinusoide



### Conclusioni

## Caso 3: Spirale Archimedea


### Conclusioni