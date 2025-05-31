# Crazy-Taxi 🚖
This project is a hardware-based recreation of the classic arcade game "Crazy Taxi" (inspired by the version on CoolMathGames). Written entirely from scratch in C and deployed on an FPGA, this game showcases direct hardware control for graphics (VGA display), user input (PS/2 keyboard), and real-time score output (seven-segment HEX display).

## 🚕 Game Overview
Crazy Taxi challenges players to steer a taxi down a two-lane road, dodging randomly appearing obstacles by switching lanes or timing precise jumps. Points are awarded for each obstacle successfully avoided or jumped over. The game continues until the taxi collides with an obstacle, at which point the player can choose to restart and chase a higher score.

The system is divided into five core functional blocks, as illustrated in the updated block diagram below:  

![Block_Diagram](https://github.com/user-attachments/assets/09e07905-5e79-49d4-81ef-db98e22eea28)

1. ⌨️ **Input Processing**
   - Device: PS/2 Keyboard
   - Function: Handles real-time input from the user, detecting left/right lane changes, jumps (spacebar), game start (Enter), and restart (P).
   - Implementation: Keyboard scan codes are read directly from the PS/2 data register. Debouncing and validity are checked on each frame, allowing instant and accurate reaction to player actions.

2. 🧠 **Game Logic**
   - Module: Main State Machine
   - Function: Central hub coordinating game flow (title, play, game over, restart), invoking obstacle generation, collision detection, score tracking, and animation updates.

3. 🖥️ **Rendering Engine**
   - Components:
     - Taxi Animation
     - Obstacle Animation
     - Lane Generator
    - Function: Draws all game graphics (taxi, road, obstacles, backgrounds, title, and game over screens) directly to VGA frame buffers using double buffering for flicker-free visuals.
    - Technical Details:
      - Pixel Plotting: Every pixel is drawn individually by writing a 16-bit color value to the exact frame buffer memory address.
      - Double Buffering: Two memory buffers are maintained (front/back), the display buffer is swapped on each vertical sync (vsync) to ensure smooth animation.
      - Sprite Rendering: Game objects (taxi, obstacles) and full-screen backgrounds (title, game over) are defined as static C arrays. Sprites are drawn by iterating through these arrays.
      - Background Layers: The road, sky, and desert are re-rendered each frame for scrolling effects and to erase old frames.
      - Perspective & Animation: The road and lanes are drawn with gradually widening geometry to create a pseudo-3D effect. Lane markers are animated to scroll down, enhancing the sense of speed.

4. 🛣️ **Obstacle Generator & Collision Detection**
   - Obstacle Generator:
     - Random Timing: Obstacles are generated at random intervals using a frame timer, with each obstacle randomly assigned to one of the two lanes (and only one per lane at a time).
     - Scaling & Movement: Obstacles start at the top of the screen, moving downward each frame. Their scale increases as they approach, giving the illusion of depth/perspective—this is achieved by multiplying sprite coordinates by a scaling factor during rendering.
     - Removal: Obstacles disappear if they leave the screen or are cleared by a successful jump.
    - Collision Detection:
      - Real-Time Checks: Each frame, the game checks if the taxi's bounding box overlaps with any active obstacle in the same lane. This only triggers a collision if the taxi is not currently jumping.
      - Jump Logic: When an obstacle enters the "jump zone" in front of the taxi, a correctly timed spacebar press will clear the obstacle, increase the score, and animate a jump.
      - Game Over: If an obstacle collides with the taxi (no jump), the game enters the "game over" state.

5. 🏆 **Score Tracker**
   - Device: Seven-Segment HEX Display
   - Function: Real-time score updates where every point scored is written to a memory-mapped register, immediately updating the on-board seven-segment display.

## Watch the Demo below!
[![Watch the demo](https://github.com/user-attachments/assets/b4d2ff41-2874-4889-8e48-dd73f41a2811)](https://drive.google.com/file/d/1OEAnlNtGfj88Up0lkzl5VQ6RkIEBhvfp/view?usp=drive_link)

# Academic Integrity Notice
This project was created as coursework for ECE243 at the University of Toronto. The code and documentation are provided for reference only and are not licensed for use, modification, or distribution. All rights reserved.


