import pygame
import json
import os

# 초기 설정
pygame.init()
WIDTH, HEIGHT = 800, 600
screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("자동차 레이싱 게임")
clock = pygame.time.Clock()

# 색상
WHITE = (255, 255, 255)
RED = (255, 0, 0)
BLUE = (0, 0, 255)

# 자동차 정보
CARS = {
    "default": {"color": WHITE, "speed": 5},
    "sports": {"color": RED, "speed": 8, "price": 10000},
    "suv": {"color": BLUE, "speed": 6, "price": 10000},
}

# 플레이어 상태
player = {
    "x": WIDTH // 2,
    "y": HEIGHT - 100,
    "car": "default",
    "jewels": 10000,
    "owned_cars": ["default"]
}

# 저장 파일
SAVE_FILE = "game_data.json"

# 저장된 데이터 불러오기
if os.path.exists(SAVE_FILE):
    with open(SAVE_FILE, "r") as f:
        player.update(json.load(f))

# 자동차 구매 함수
def buy_car(car_name):
    if car_name in player["owned_cars"]:
        print(f"{car_name}은 이미 구매한 차량입니다.")
    elif player["jewels"] >= CARS[car_name]["price"]:
        player["jewels"] -= CARS[car_name]["price"]
        player["owned_cars"].append(car_name)
        save_game()
        print(f"{car_name}을(를) 구매했습니다!")
    else:
        print("보석이 부족합니다.")

# 데이터 저장 함수
def save_game():
    with open(SAVE_FILE, "w") as f:
        json.dump(player, f)

# 게임 루프
running = True
while running:
    screen.fill((0, 0, 0))

    # 이벤트 처리
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    # 키 입력 (자동차 이동)
    keys = pygame.key.get_pressed()
    if keys[pygame.K_LEFT] and player["x"] > 0:
        player["x"] -= CARS[player["car"]]["speed"]
    if keys[pygame.K_RIGHT] and player["x"] < WIDTH - 50:
        player["x"] += CARS[player["car"]]["speed"]

    # 자동차 그리기
    pygame.draw.rect(screen, CARS[player["car"]]["color"], (player["x"], player["y"], 50, 30))

    # 화면 업데이트
    pygame.display.flip()
    clock.tick(30)

pygame.quit()
