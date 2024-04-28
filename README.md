# TIC-TAC-TOE
TIC TAC TOE
import pygame
import sys
import os

# Initialize Pygame
pygame.init()

# Constants
WIDTH = 550
HEIGHT = 550
LINE_WIDTH = 15
BOARD_ROWS = 3
BOARD_COLS = 3
SQUARE_SIZE = WIDTH // BOARD_COLS
CIRCLE_RADIUS = SQUARE_SIZE // 4
CIRCLE_WIDTH = 15
CROSS_WIDTH = 25
SPACE = SQUARE_SIZE // 4
GRAY = (192, 192, 192)
LINE_COLOR = (0, 0, 0)
CIRCLE_COLOR = (255, 0, 0)
CROSS_COLOR = (0, 0, 255)

# Initialize screen
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("Tic Tac Toe")
screen.fill(GRAY)

# Fonts
font = pygame.font.Font(None, 36)

# Player names and scores
player1_name = ""
player2_name = ""
player1_score = 0
player2_score = 0

# File for storing scores
SCORES_FILE = "scores.txt"

# Load scores from file
def load_scores():
    if os.path.exists(SCORES_FILE):
        with open(SCORES_FILE, "r") as file:
            lines = file.readlines()
            global player1_score, player2_score
            player1_score = int(lines[0].strip())
            player2_score = int(lines[1].strip())

# Save scores to file
def save_scores():
    with open(SCORES_FILE, "w") as file:
        file.write(f"{player1_score}\n")
        file.write(f"{player2_score}\n")

# Load scores at the beginning
load_scores()

# Board
board = [['' for _ in range(BOARD_COLS)] for _ in range(BOARD_ROWS)]

# Function to restart the game
def restart_game():
    global board, game_over
    board = [['' for _ in range(BOARD_COLS)] for _ in range(BOARD_ROWS)]
    game_over = False
    screen.fill(GRAY)
    draw_lines()

# Functions
def draw_lines():
    # Horizontal lines
    pygame.draw.line(screen, LINE_COLOR, (0, SQUARE_SIZE), (WIDTH, SQUARE_SIZE), LINE_WIDTH)
    pygame.draw.line(screen, LINE_COLOR, (0, 2 * SQUARE_SIZE), (WIDTH, 2 * SQUARE_SIZE), LINE_WIDTH)
    # Vertical lines
    pygame.draw.line(screen, LINE_COLOR, (SQUARE_SIZE, 0), (SQUARE_SIZE, HEIGHT), LINE_WIDTH)
    pygame.draw.line(screen, LINE_COLOR, (2 * SQUARE_SIZE, 0), (2 * SQUARE_SIZE, HEIGHT), LINE_WIDTH)

def draw_figures():
    for row in range(BOARD_ROWS):
        for col in range(BOARD_COLS):
            if board[row][col] == 'O':
                pygame.draw.circle(screen, CIRCLE_COLOR, (col * SQUARE_SIZE + SQUARE_SIZE // 2, row * SQUARE_SIZE + SQUARE_SIZE // 2), CIRCLE_RADIUS, CIRCLE_WIDTH)
            elif board[row][col] == 'X':
                pygame.draw.line(screen, CROSS_COLOR, (col * SQUARE_SIZE + SPACE, row * SQUARE_SIZE + SQUARE_SIZE - SPACE), (col * SQUARE_SIZE + SQUARE_SIZE - SPACE, row * SQUARE_SIZE + SPACE), CROSS_WIDTH)
                pygame.draw.line(screen, CROSS_COLOR, (col * SQUARE_SIZE + SPACE, row * SQUARE_SIZE + SPACE), (col * SQUARE_SIZE + SQUARE_SIZE - SPACE, row * SQUARE_SIZE + SQUARE_SIZE - SPACE), CROSS_WIDTH)

def mark_square(row, col, player):
    global player1_score, player2_score

    if player == 'X':
        player_name = player1_name
        player_score = player1_score
    else:
        player_name = player2_name
        player_score = player2_score

    board[row][col] = player

    # Update the player's score
    player_score += 1
    if player == 'X':
        player1_score = player_score
    else:
        player2_score = player_score

    print(f"{player_name}'s score: {player_score}")

    # Save scores to file
    save_scores()

def available_square(row, col):
    return board[row][col] == ''

def is_board_full():
    for row in range(BOARD_ROWS):
        for col in range(BOARD_COLS):
            if board[row][col] == '':
                return False
    return True

def check_win(player):
    # Check rows
    for row in range(BOARD_ROWS):
        if board[row][0] == board[row][1] == board[row][2] == player:
            draw_winning_line(row, 0, row, 2)
            return True
    # Check columns
    for col in range(BOARD_COLS):
        if board[0][col] == board[1][col] == board[2][col] == player:
            draw_winning_line(0, col, 2, col)
            return True
    # Check diagonals
    if board[0][0] == board[1][1] == board[2][2] == player:
        draw_winning_line(0, 0, 2, 2)
        return True
    if board[0][2] == board[1][1] == board[2][0] == player:
        draw_winning_line(0, 2, 2, 0)
        return True
    return False

def draw_winning_line(row1, col1, row2, col2):
    pygame.draw.line(screen, LINE_COLOR, (col1 * SQUARE_SIZE + SQUARE_SIZE // 2, row1 * SQUARE_SIZE + SQUARE_SIZE // 2), (col2 * SQUARE_SIZE + SQUARE_SIZE // 2, row2 * SQUARE_SIZE + SQUARE_SIZE // 2), LINE_WIDTH)

def declare_draw():
    draw_text("It's a draw!", (WIDTH // 2, HEIGHT // 2))

def draw_text(text, position):
    text_surface = font.render(text, True, (0, 0, 0))
    screen.blit(text_surface, (position[0] - text_surface.get_width() // 2, position[1] - text_surface.get_height() // 2))
    pygame.display.update()

def start_screen():
    global player1_name, player2_name

    screen.fill(GRAY)
    draw_lines()
    start_text = font.render("Press 'S' to start the game", True, (0, 0, 0))
    screen.blit(start_text, (WIDTH // 2 - start_text.get_width() // 2, HEIGHT // 2 - start_text.get_height() // 2))

    # Instructions for entering player names
    player1_text = font.render("Enter Player 1's name:", True, (0, 0, 0))
    player2_text = font.render("Enter Player 2's name:", True, (0, 0, 0))
    screen.blit(player1_text, (WIDTH // 2 - player1_text.get_width() // 2, HEIGHT // 4))
    screen.blit(player2_text, (WIDTH // 2 - player2_text.get_width() // 2, HEIGHT // 4 + 50))

    pygame.display.update()

    # Input for Player 1's name
    input_rect1 = pygame.Rect(WIDTH // 2 - 100, HEIGHT // 4 + 30, 200, 30)
    pygame.draw.rect(screen, (255, 255, 255), input_rect1)
    pygame.display.update()

    # Input for Player 2's name
    input_rect2 = pygame.Rect(WIDTH // 2 - 100, HEIGHT // 4 + 80, 200, 30)
    pygame.draw.rect(screen, (255, 255, 255), input_rect2)
    pygame.display.update()

    player1_input = ""
    player2_input = ""

    while True:
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                pygame.quit()
                sys.exit()
            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_RETURN:
                    if player1_input and player2_input:
                        player1_name = player1_input
                        player2_name = player2_input
                        return True
                elif event.key == pygame.K_BACKSPACE:
                    if input_rect1.collidepoint(pygame.mouse.get_pos()):
                        player1_input = player1_input[:-1]
                    elif input_rect2.collidepoint(pygame.mouse.get_pos()):
                        player2_input = player2_input[:-1]
                else:
                    if input_rect1.collidepoint(pygame.mouse.get_pos()):
                        player1_input += event.unicode
                    elif input_rect2.collidepoint(pygame.mouse.get_pos()):
                        player2_input += event.unicode

        # Render the input texts
        player1_surface = font.render(player1_input, True, (0, 0, 0))
        screen.blit(player1_surface, (input_rect1.x + 5, input_rect1.y + 5))
        pygame.draw.rect(screen, (0, 0, 0), input_rect1, 2)

        player2_surface = font.render(player2_input, True, (0, 0, 0))
        screen.blit(player2_surface, (input_rect2.x + 5, input_rect2.y + 5))
        pygame.draw.rect(screen, (0, 0, 0), input_rect2, 2)

        pygame.display.update()

# Main loop
game_started = False
turn = 'X'
game_over = False

while not game_started:
    if start_screen():
        game_started = True

screen.fill(GRAY)
draw_lines()

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()

        if event.type == pygame.MOUSEBUTTONDOWN and not game_over:
            mouseX = event.pos[0] 
            mouseY = event.pos[1]

            clicked_row = mouseY // SQUARE_SIZE
            clicked_col = mouseX // SQUARE_SIZE

            if available_square(clicked_row, clicked_col):
                mark_square(clicked_row, clicked_col, turn)
                if check_win(turn):
                    game_over = True
                    winner = player1_name if turn == 'X' else player2_name
                    winner_text = font.render(f"{winner} wins!", True, (0, 0, 0))
                    screen.blit(winner_text, (WIDTH // 2 - winner_text.get_width() // 2, HEIGHT // 2 - winner_text.get_height() // 2))
                elif is_board_full():
                    game_over = True
                    declare_draw()  # Call declare_draw function
                else:
                    if turn == 'X':
                        turn = 'O'
                    else:
                        turn = 'X'
                draw_figures()

        if event.type == pygame.KEYDOWN:
            if event.key == pygame.K_r:
                restart_game()

    pygame.display.update()
