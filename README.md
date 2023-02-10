# Dragon-s-Lake-Test
  This project was made as a part of a test for internship in Dragon's Lake.
  The main goal was to create a simple arkanoid-like game with several conditions, using provided framework:
  
  1. There is an enemy turret, which randomly moves and shoots a player. Bullets spawned by a turret should pass through blocks and not destroy them. Game ends if the player is hit by a bullet. 
  
  2. Blue blocks are moving left-right or top-down with a constant speed, so it is harder to hit them. Moving blocks should not intersect with other blocks. You can use any color you want.
  
  3. The first ability (positive) increases and the second one (negative) decreases the speed of the platform by 40%. Ability stays active for 20 seconds. Abilities are “stackable”, it means that it is possible to have multiple active instances of the same ability. Minimum speed is 50%, maximum is 200% of a default speed.
  
  4. Abilities are dropped when some random blocks are destroyed. A random ability is spawned in the position of a destroyed block. Every ability has a collision, so it can fall down if there is no block below. The player should “catch” an ability with the platform.

  
