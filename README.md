# KI_Game

# Games.01: Handsimulation: Minimax und alpha-beta-Pruning

1. Innere Knoten:

E = max(9,1,6) = 9

F = max(2,1,1) = 2

G = max(6,5,2) = 6

C = min(E,F,G) = min(9,2,6) = 2

B = min(8,7,3) = 3

D = min(2,1,3) = 1

A = max(B,C,D) = max(3,2,1) = 3

die Minimax-Wurzelbewertung ist A = 3. Der optimale Zug für MAX ist der Zweig zu B, weil B = 3 ist maximal.

2. 

Start A: α = −∞, β = +∞.

Nach Auswertung B: A: α = 3, β = +∞.

Beim Eintritt in C: C sieht α = 3, β = +∞.

Nach E: C.curr_min = 9 (keine Prune).

Nach F: C.curr_min = 2 → weil 2 <= α(=3) ⇒ prune G.

Nach C: A bleibt α = 3.

Bei D: D sieht α = 3. Nach erstem Blatt 2 → 2 <= 3 ⇒ prune die restlichen D-Kinder.

3. Ja. α–β-Pruning profitiert stark von einer guten Heuristik-Reihenfolge der Kinder.
   
Beispiel :

Bei C wurde in der gezeigten Reihenfolge E(=9) → F(=2) → G(=6) ausgewertet. Weil E (9) zuerst kam, mussten wir E vollständig berechnen. Hätte man F (das den kleinen Wert 2 liefert) als erstes unter C ausgewertet, wäre curr_min bei C sofort = 2 und somit hätte man E und G komplett prunen können. 

# Games.02: Optimale Spiele: Minimax und alpha-beta-Pruning

X = "X"

O = "O"

EMPTY = " "

def Terminal_Test(state):

    return winner(state) is not None or EMPTY not in state

def Utility(state):

    w = winner(state)
    
    if w == X: return 1
    
    if w == O: return -1
    
    return 0

def winner(board):

    wins = [
        [0,1,2],[3,4,5],[6,7,8],     
        [0,3,6],[1,4,7],[2,5,8],     
        [0,4,8],[2,4,6]              
    ]
    for a,b,c in wins:
    
        if board[a] != EMPTY and board[a] == board[b] == board[c]:
        
            return board[a]
            
    return None

def Successors(state, player):

    return [(i, state[:i] + player + state[i+1:])
    
            for i in range(9) if state[i] == EMPTY]

INF = float("inf")

def Max_Value(state):

    if Terminal_Test(state):
    
        return Utility(state)

    v = -INF
    
    for (a, s) in Successors(state, X):
    
        v = max(v, Min_Value(s))
        
    return v


def Min_Value(state):

    if Terminal_Test(state):
    
        return Utility(state)

    v = INF
    
    for (a, s) in Successors(state, O):
    
        v = min(v, Max_Value(s))
        
    return v


def Minimax(state):

    val = -INF
    
    action = None
    
    for (a, s) in Successors(state, X):
    
        v = Min_Value(s)
        
        if v > val:
        
            val = v
            
            action = a
            
    return action

2.a  

def Max_Value_AB(state, alpha=-INF, beta=INF):
    global nodes_alphabeta
    nodes_alphabeta += 1

    if Terminal_Test(state):
        return Utility(state)

    v = -INF
    for (a, s) in Successors(state, X):
        v = max(v, Min_Value_AB(s, alpha, beta))
        if v >= beta:
            return v  # Beta-Cut
        alpha = max(alpha, v)
    return v

def Min_Value_AB(state, alpha=-INF, beta=INF):
    global nodes_alphabeta
    nodes_alphabeta += 1

    if Terminal_Test(state):
        return Utility(state)

    v = INF
    for (a, s) in Successors(state, O):
        v = min(v, Max_Value_AB(s, alpha, beta))
        if v <= alpha:
            return v  # Alpha-Cut
        beta = min(beta, v)
    return v

def Minimax_AB(state):
    val = -INF
    action = None
    alpha = -INF
    beta = INF
    for (a, s) in Successors(state, X):
        v = Min_Value_AB(s, alpha, beta)
        if v > val:
            val = v
            action = a
        alpha = max(alpha, val)
    return action

Bei leeren Board (alle 9 Felder frei) prüft Minimax alle 9!=362880 möglichen Spielverläufe theoretisch.

Mit Alpha-Beta-Pruning werden viele Knoten abgeschnitten, sobald ein Zug garantiert schlechter als ein bisher gefundener Zug ist.

 Szenario zum Vergleich:

ein Board testen, bei dem X oder O schon einige Felder besetzt hat, z.B.:

board = "X O X    "

Dann wird Alpha-Beta noch effizienter, weil viele Züge früh verworfen werden.

# Games.05: Minimax generalisier

Wurzel -> (1,2,3)

Spieler 1 (x1​),K_A: (1, 2, 3), K_B: (-1, 5, 2), max(1,−1)=1-> (1, 2, 3)

Spieler 2 (x2​),K_A (1, 2, 3), K_B: (6, 1, 2),   max(2,1)=2 ->(1, 2, 3)

Spieler 2 (x2​),K_C: (-1, 5, 2), K_D: (5, 4, 5), max(5,4)=5 ->(-1, 5, 2)

Spieler 3 (x3​), K_A(1, 2, 3), (4, 2, 1),  max(3,1)=3 ->(1, 2, 3)

Spieler 3 (x3​),K_B(6, 1, 2), (7, 4, -1), max(2,−1)=2 ->(6, 1, 2)

Spieler 3 (x3​),K_C(5, 1, -1), (-1, 5, 2),max(−1,2)=2 ->(-1, 5, 2)

Spieler 3 (x3​), K_D(7, 7, -1), (5, 4, 5) ,max(−1,5)=5 ->(5, 4, 5)

# Games.03: Minimax vereinfachen

----------------------------------------------Bewertung durch den Standard-Minimax-Algorithmus-------------------------------------------------------

Wir berechnen die Werte von unten nach oben.

Ebene 2 (MAX-Knoten, Vorgänger der Blätter)

Knoten E1: wählt max(10, 5) = 10

Knoten E2: wählt max(8, 3) = 8

Knoten E3: wählt max(9, 15) = 15

Ebene 1 (MIN-Knoten)

Knoten M1: wählt min(Wert E1) = min(10) = 10

Knoten M2: wählt min(Wert E2, Wert E3) = min(8, 15) = 8

Ebene 0 (MAX-Knoten, Wurzel)

Wurzel A: wählt max(Wert M1, Wert M2) = max(10, 8) = 10

Ergebnis: Der optimale Wert für MAX ist 10.

-------------------------------------------Bewertung durch den vereinfachten Negamax-Algorithmus----------------------------------------

Negamax maximiert immer den negativen Wert des Nachfolgers.

Ebene 2 (MAX-Knoten)

Knoten E1: max(-(-10), -(-5)) = max(10, 5) = 10

Knoten E2: max(-(-8), -(-3)) = max(8, 3) = 8

Knoten E3: max(-9, -15) = 15

Ebene 1 (MIN-Knoten)

Knoten M1: max(-(Wert E1)) = max(-10) = -10

Knoten M2: max(-(Wert E2), -(Wert E3)) = max(-8, -15) = -8

Ebene 0 (MAX-Knoten, Wurzel)

Wurzel A: max(-(Wert M1), -(Wert M2)) = max(-(-10), -(-8)) = max(10, 8) = 10

--->Ergebnis:  der vereinfachte Negamax-Algorithmus liefert den optimalen Wert 10 für MAX.

Beide Algorithmen sind äquivalent, da Negamax die Nullsummenspiel-Eigenschaft ausnutzt und dadurch die Min-Value-Funktion nicht benötigt.
