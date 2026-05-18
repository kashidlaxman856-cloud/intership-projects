#TIC TAC TOE GAME WITH AI PLAYER

CODE PERFORMED:

import math

# Initialize the board
board = [' ' for _ in range(9)]

def print_board():
    for row in [board[i*3:(i+1)*3] for i in range(3)]:
        print('| ' + ' | '.join(row) + ' |')

def available_moves():
    return [i for i, spot in enumerate(board) if spot == ' ']

def empty_squares():
    return ' ' in board

def num_empty_squares():
    return board.count(' ')

def make_move(square, letter):
    if board[square] == ' ':
        board[square] = letter
        return True
    return False

def check_winner(square, letter):
    # Check row
    row_ind = square // 3
    row = board[row_ind*3 : (row_ind+1)*3]
    if all([spot == letter for spot in row]):
        return True
    # Check column
    col_ind = square % 3
    column = [board[col_ind+i*3] for i in range(3)]
    if all([spot == letter for spot in column]):
        return True
    # Check diagonals
    if square % 2 == 0:
        diagonal1 = [board[i] for i in [0, 4, 8]]
        if all([spot == letter for spot in diagonal1]):
            return True
        diagonal2 = [board[i] for i in [2, 4, 6]]
        if all([spot == letter for spot in diagonal2]):
            return True
    return False

def is_board_full():
    """Check if the board is completely full"""
    return len(available_moves()) == 0

def has_winner(test_board, letter):
    """Check if there's a winner on the given board state"""
    for i in range(9):
        if test_board[i] == letter:
            # Temporarily use global board to check winner
            global board
            old_board = board.copy()
            board = test_board
            result = check_winner(i, letter)
            board = old_board
            if result:
                return True
    return False

# Minimax Algorithm
def minimax(test_board, depth, is_maximizing):
    """
    Minimax algorithm for Tic-Tac-Toe AI
    test_board: current board state
    depth: current depth in the game tree
    is_maximizing: True if AI turn, False if human turn
    """
    max_player = 'O'  # AI
    min_player = 'X'  # Human
    
    # Check terminal states
    if has_winner(test_board, max_player):
        return {'score': 10 - depth, 'move': None}
    elif has_winner(test_board, min_player):
        return {'score': depth - 10, 'move': None}
    elif is_board_full():
        return {'score': 0, 'move': None}
    
    if is_maximizing:
        # AI's turn - maximize score
        best_score = -math.inf
        best_move = None
        
        for move in available_moves():
            test_board[move] = max_player
            result = minimax(test_board, depth + 1, False)
            test_board[move] = ' '
            
            if result['score'] > best_score:
                best_score = result['score']
                best_move = move
        
        return {'score': best_score, 'move': best_move}
    else:
        # Human's turn - minimize score
        best_score = math.inf
        best_move = None
        
        for move in available_moves():
            test_board[move] = min_player
            result = minimax(test_board, depth + 1, True)
            test_board[move] = ' '
            
            if result['score'] < best_score:
                best_score = result['score']
                best_move = move
        
        return {'score': best_score, 'move': best_move}

def ai_move():
    """Get the best move for the AI using minimax"""
    result = minimax(board.copy(), 0, True)
    return result['move']

# Game Loop
def play():
    global board
    board = [' ' for _ in range(9)]
    
    print("Welcome to AI Tic-Tac-Toe! You are 'X' and go first.")
    print("The AI is 'O' and will try to beat you!")
    print_board()
    print("\nPositions are numbered 0-8:")
    print(" 0 | 1 | 2")
    print("-----------")
    print(" 3 | 4 | 5")
    print("-----------")
    print(" 6 | 7 | 8\n")
    
    letter = 'X'
    
    while empty_squares():
        if letter == 'O':
            print("\nAI is thinking...")
            move = ai_move()
            
            if move is not None:
                make_move(move, 'O')
                print(f"AI makes a move to square {move}")
                print_board()
                
                if check_winner(move, 'O'):
                    print("\nAI wins! Better luck next time.")
                    return
                
                letter = 'X'
            else:
                print("Draw!")
                return
        else:
            try:
                move = int(input("\nYour turn (0-8): "))
                
                if move < 0 or move > 8:
                    print("Please enter a number between 0 and 8.")
                    continue
                
                if move not in available_moves():
                    print("That square is already taken. Try again.")
                    continue
                
                make_move(move, 'X')
                print_board()
                
                if check_winner(move, 'X'):
                    print("\nWow! You won! (This should be nearly impossible!)")
                    return
                
                letter = 'O'
            except ValueError:
                print("Please enter a valid number between 0 and 8.")
    
    print("\nIt's a draw!")

if __name__ == '__main__':
    play()

https://github.com/user-attachments/assets/6ecc83d6-a3a8-4911-bdb9-f80b44a32cbf



