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
        [0,1,2],[3,4,5],[6,7,8],     # rows
        [0,3,6],[1,4,7],[2,5,8],     # cols
        [0,4,8],[2,4,6]              # diagonals
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





