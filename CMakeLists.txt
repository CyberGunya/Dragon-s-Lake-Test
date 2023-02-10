cmake_minimum_required (VERSION 3.6)
project(Game)

add_executable(${PROJECT_NAME} game.cpp)
 
add_subdirectory(Framework)


include_directories(${PROJECT_NAME} PRIVATE ./Framework/inc)
#include_directories(${PROJECT_NAME} PRIVATE ./data)

target_link_libraries(${PROJECT_NAME} FrameworkRelease)
FILE(COPY 
	./data/01-Breakout-Tiles.png
	./data/03-Breakout-Tiles.png  
	./data/07-Breakout-Tiles.png  
	./data/09-Breakout-Tiles.png 
	./data/41-Breakout-Tiles.png
	./data/42-Breakout-Tiles.png
	./data/43-Breakout-Tiles.png
	./data/47-Breakout-Tiles.png 
	./data/61-Breakout-Tiles.png
	./data/62-Breakout-Tiles.png     
	./data/Turret.png
	./data/slow-ability.png
	./data/fast-ability.png
	./data/Win.png
	./data/Lose.png
	DESTINATION "${CMAKE_BINARY_DIR}")