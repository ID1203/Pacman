# Multi-Man

// package idi135;
// import java.awt.*;
// import java.awt.event.*;
// import java.util.ArrayList;
// import java.util.HashSet;
// import java.util.Random;
// import java.util.List;
// import java.util.ArrayList;

// import javax.swing.*;

// import com.google.firebase.database.DataSnapshot;
// import com.google.firebase.database.DatabaseError;
// import com.google.firebase.database.ValueEventListener;

// import idi135.FirebaseService;  
// import idi135.PlayerScore;      

// //extends Jpanele = GUI
// public class PacMan extends JPanel implements ActionListener, KeyListener {

//     class Block {
//         int x;
//         int y;
//         int width;
//         int height;
//         Image image;

//         int startX;
//         int startY;
//         char direction = 'U'; 
//         int velocityX = 0;
//         int velocityY = 0;

//         Block(Image image, int x, int y, int width, int height) {
//             this.image = image;
//             this.x = x;
//             this.y = y;
//             this.width = width;
//             this.height = height;
//             this.startX = x;
//             this.startY = y;
//         }

//         void updateDirection(char direction) {
//             char prevDirection = this.direction;
//             this.direction = direction;
//             updateVelocity();
//             //Keeps Pacman moving in direction afrer hitting wall
//             for (Block wall : walls) {
//                 if (collision(this, wall)) {
//                     this.x -= this.velocityX;
//                     this.y -= this.velocityY;
//                     this.direction = prevDirection;
//                     updateVelocity();
//                 }
//             }
//         } 

//         void updateVelocity() {
//             if (this.direction == 'U') {
//                 this.velocityX = 0;
//                 this.velocityY = -tileSize/4;
//             }
//             else if (this.direction == 'D') {
//                 this.velocityX = 0;
//                 this.velocityY = tileSize/4;
//             }
//             else if (this.direction == 'L') {
//                 this.velocityX = -tileSize/4;
//                 this.velocityY = 0;
//             }
//             else if (this.direction == 'R') {
//                 this.velocityX = tileSize/4;
//                 this.velocityY = 0;
//             }
//         }

//         void reset() {
//             this.x = this.startX;
//             this.y = this.startY;
//         }
//     }
    
//     private String playerName;
//     private int rowCount = 21;
//     private int columnCount = 19;
//     private int tileSize = 32;
//     private int boardWidth = columnCount * tileSize;
//     private int boardHeight = rowCount * tileSize;

//     private Image wallImage;
//     private Image blueGhostImage;
//     private Image orangeGhostImage;
//     private Image pinkGhostImage;
//     private Image redGhostImage;
//     private Image cherryImage;
//     private Image foodImage;

//     private Image pacmanUpImage;
//     private Image pacmanDownImage;
//     private Image pacmanLeftImage;
//     private Image pacmanRightImage;

//     //Make every element/square uniqiue stopping duplicates
//     HashSet<Block> walls;
//     HashSet<Block> foods;
//     HashSet<Block> ghosts;
//     Block pacman;

//     Timer gameLoop;
//     char[] directions = {'U', 'D', 'L', 'R'}; //up down left right
//     Random random = new Random();
//     int score = 0;
//     int lives = 3;
//     boolean gameOver = false;

//     //X = wall, O = skip, P = pac man, ' ' = food
//     //Ghosts: b = blue, o = orange, p = pink, r = red
//     private String[] tileMap = {
//         "XXXXXXXXXXXXXXXXXXX",
//         "X        X        X",
//         "X XX XXX X XXX XX X",
//         "X                 X",
//         "X XX X XXXXX X XX X",
//         "X    X       X    X",
//         "XXXX XXXX XXXX XXXX",
//         "OOOX X       X XOOO",
//         "XXXX X XXrXX X XXXX",
//         "O       bpo       O",
//         "XXXX X XXXXX X XXXX",
//         "OOOX X       X XOOO",
//         "XXXX X XXXXX X XXXX",
//         "X        X        X",
//         "X XX XXX X XXX XX X",
//         "X  X     P     X  X",
//         "XX X X XXXXX X X XX",
//         "X    X   X   X    X",
//         "X XXXXXX X XXXXXX X",
//         "X                 X",
//         "XXXXXXXXXXXXXXXXXXX" 
//     };


//     PacMan() {
        
//         setPreferredSize(new Dimension(boardWidth, boardHeight));
//         setBackground(Color.BLACK);
//         addKeyListener(this);
//         setFocusable(true);

//         wallImage = new ImageIcon(getClass().getResource("/wall.png")).getImage();
//         blueGhostImage = new ImageIcon(getClass().getResource("/blueGhost.png")).getImage();
//         orangeGhostImage = new ImageIcon(getClass().getResource("/orangeGhost.png")).getImage();
//         pinkGhostImage = new ImageIcon(getClass().getResource("/pinkGhost.png")).getImage();
//         redGhostImage = new ImageIcon(getClass().getResource("/redGhost.png")).getImage();
//         cherryImage = new ImageIcon(getClass().getResource("/cherry.png")).getImage();
//         foodImage = new ImageIcon(getClass().getResource("/powerFood.png")).getImage();

//         pacmanUpImage = new ImageIcon(getClass().getResource("/pacmanUp.png")).getImage();
//         pacmanDownImage = new ImageIcon(getClass().getResource("/pacmanDown.png")).getImage();
//         pacmanLeftImage = new ImageIcon(getClass().getResource("/pacmanLeft.png")).getImage();
//         pacmanRightImage = new ImageIcon(getClass().getResource("/pacmanRight.png")).getImage();

//         loadMap();
//         for (Block ghost : ghosts) {
//             char newDirection = directions[random.nextInt(4)];
//             ghost.updateDirection(newDirection);
//         }
//         //how long it takes to start timer, milliseconds gone between frames
//         gameLoop = new Timer(50, this); //20fps (1000/50)
//         gameLoop.start();
        

//     }

//     public void loadMap(){
//         walls = new HashSet<Block>();
//         foods = new HashSet<Block>();
//         ghosts = new HashSet<Block>();

//         //Loop through rows and colums in map
//         for(int r = 0; r < rowCount; r++){
//             for(int c = 0; c < columnCount; c++){
//                 String row = tileMap[r];
//                 //The character in the square on the map
//                 char tileMapChar = row.charAt(c);

//                 int x = c*tileSize;
//                 int y = r*tileSize;

//                 if (tileMapChar == 'X') { //block wall
//                     Block wall = new Block(wallImage, x, y, tileSize, tileSize);
//                     walls.add(wall);
//                 }
//                 else if (tileMapChar == 'b') { //blue ghost
//                     Block ghost = new Block(blueGhostImage, x, y, tileSize, tileSize);
//                     ghosts.add(ghost);
//                 }
//                 else if (tileMapChar == 'o') { //orange ghost
//                     Block ghost = new Block(orangeGhostImage, x, y, tileSize, tileSize);
//                     ghosts.add(ghost);
//                 }
//                 else if (tileMapChar == 'p') { //pink ghost
//                     Block ghost = new Block(pinkGhostImage, x, y, tileSize, tileSize);
//                     ghosts.add(ghost);
//                 }
//                 else if (tileMapChar == 'r') { //red ghost
//                     Block ghost = new Block(redGhostImage, x, y, tileSize, tileSize);
//                     ghosts.add(ghost);
//                 }
//                 else if (tileMapChar == 'P') { //pacman
//                     pacman = new Block(pacmanRightImage, x, y, tileSize, tileSize);
//                 }
//                 else if (tileMapChar == ' ') { //food 
//                     Block food = new Block(foodImage, x + 14, y + 14, 4, 4);
//                     foods.add(food);
//                 }
//             }
//         }
//     }
//     // Invoke the function of the same name using super and draws the map
//     public void paintComponent(Graphics g) {
//         super.paintComponent(g);
//         draw(g);
//     }

//     public void draw(Graphics g) {
//         //Diaplayes the pacman image
//         g.drawImage(pacman.image, pacman.x, pacman.y, pacman.width, pacman.height, null);

//         for (Block ghost : ghosts) {
//             g.drawImage(ghost.image, ghost.x, ghost.y, ghost.width, ghost.height, null);
//         }

//         for (Block wall : walls) {
//             g.drawImage(wall.image, wall.x, wall.y, wall.width, wall.height, null);
//         }

//         for (Block food : foods) {
//             g.drawImage(food.image, food.x, food.y, food.width, food.height, null);
//         }

//         g.setFont(new Font("Arial", Font.PLAIN, 18));
//         if (gameOver) {
//             g.drawString("Game Over: " + String.valueOf(score), tileSize/2, tileSize/2);
//         }
//         else if (foods.isEmpty()) {
//             g.drawString("Winner: " + String.valueOf(score), tileSize/2, tileSize/2);
//         } else {
//             g.drawString("x" + String.valueOf(lives) + " Score: " + String.valueOf(score), tileSize/2, tileSize/2);
//         }
        
//     }

//     public void move() {
//         pacman.x += pacman.velocityX;
//         pacman.y += pacman.velocityY;

//         if (pacman.x < 0) {  // Teleport from left to right
//             pacman.x = boardWidth - tileSize;
//         } else if (pacman.x + pacman.width > boardWidth) {  // Teleport from right to left
//             pacman.x = 0;
//         }

//         for (Block wall : walls) {
//             if (collision(pacman, wall)) {
//                 pacman.x -= pacman.velocityX;
//                 pacman.y -= pacman.velocityY;
//                 break;
//             }
//         }

//         //check ghost collisions
//         for (Block ghost : ghosts) {
//             ghost.x += ghost.velocityX;
//             ghost.y += ghost.velocityY;

//             if (collision(ghost, pacman)) {
//                 lives -= 1;
//                 if (lives == 0) {
//                     gameOver();
//                     return;
//                 }
//                 resetPositions();
//             }
//             if (ghost.y == tileSize*9 && ghost.direction != 'U' && ghost.direction != 'D') {
//                 ghost.updateDirection('U');
//             }
//             for (Block wall : walls) {
//                 if (collision(ghost, wall) || ghost.x <= 0 || ghost.x + ghost.width >= boardWidth) {
//                     ghost.x -= ghost.velocityX;
//                     ghost.y -= ghost.velocityY;
//                     char newDirection = directions[random.nextInt(4)];
//                     ghost.updateDirection(newDirection);
//                 }
//             }
//         }

//         //check food collision 
//         Block foodEaten = null;
//         for (Block food : foods) {
//             if (collision(pacman, food)) {
//                 foodEaten = food;
//                 score += 10;
//             }
//         }
//         foods.remove(foodEaten);

//         if (foods.isEmpty()) {
//             loadMap();
//             resetPositions();
//         }


//     }

//     public boolean collision(Block a, Block b) {
//         return  a.x < b.x + b.width &&
//                 a.x + a.width > b.x &&
//                 a.y < b.y + b.height &&
//                 a.y + a.height > b.y;
//     }

//     public void resetPositions() {
//         pacman.reset();
//         pacman.velocityX = 0;
//         pacman.velocityY = 0;
//         for (Block ghost : ghosts) {
//             ghost.reset();
//             char newDirection = directions[random.nextInt(4)];
//             ghost.updateDirection(newDirection);
//         }
//     }

//     private void showLeaderboard() {
//     // Display the leaderboard
//     FirebaseService.getLeaderboard(new ValueEventListener() {
//         @Override
//         public void onDataChange(DataSnapshot dataSnapshot) {

//             String leaderboardMessage = "Leaderboard:\n";
//             List<PlayerScore> leaderboard = new ArrayList<>();
//             for (DataSnapshot snapshot : dataSnapshot.getChildren()) {
//                 PlayerScore playerScore = snapshot.getValue(PlayerScore.class);
//                 leaderboard.add(playerScore);
//             }

//             // Sort the leaderboard by score (highest to lowest)
//             leaderboard.sort((p1, p2) -> Integer.compare(p2.score, p1.score));

//             // Build the leaderboard message
//             for (int i = 0; i < 5; i++) {
//                 PlayerScore playerScore = leaderboard.get(i);
//                 leaderboardMessage += (i + 1) + ". " + playerScore.name + " - " + playerScore.score + " points\n";
//             }

//             // Show leaderboard in a dialog box
//             JOptionPane.showMessageDialog(PacMan.this, leaderboardMessage, "Leaderboard", JOptionPane.INFORMATION_MESSAGE);
//         }

//         @Override
//         public void onCancelled(DatabaseError databaseError) {
//             JOptionPane.showMessageDialog(PacMan.this, "Error loading leaderboard.", "Error", JOptionPane.ERROR_MESSAGE);
//         }
//     });
//     }

    
//     public void gameOver() {
//     gameOver = true;
//     gameLoop.stop();  
  
//     String playerName = JOptionPane.showInputDialog("Enter your username:");
//         if (playerName == null || playerName.trim().isEmpty()) {
//             playerName = "Player" + new Random().nextInt(1000); 
//         }
    
//     if (playerName != null && !playerName.trim().isEmpty()) {
//         FirebaseService.writeLeaderboard(playerName, score);
//     }
//         showLeaderboard();
//     }


//     @Override
//     public void actionPerformed(ActionEvent e) {
//         move();
//         repaint();
       

//     }


//     @Override
//     public void keyTyped(KeyEvent e) {}

//     @Override
//     public void keyPressed(KeyEvent e) {
        

//         if(e.getKeyCode() == KeyEvent.VK_SPACE){
//             loadMap();
//             resetPositions();
//             loadMap();
//             resetPositions();
//             lives = 3;
//             score = 0;
//             gameOver = false;
//             gameLoop.start();

//         }
//         if (e.getKeyCode() == KeyEvent.VK_UP) {
//             pacman.updateDirection('U');
//         }
//         else if (e.getKeyCode() == KeyEvent.VK_DOWN) {
//             pacman.updateDirection('D');
//         }
//         else if (e.getKeyCode() == KeyEvent.VK_LEFT) {
//             pacman.updateDirection('L');
//         }
//         else if (e.getKeyCode() == KeyEvent.VK_RIGHT) {
//             pacman.updateDirection('R');
//         }

//         if (pacman.direction == 'U') {
//             pacman.image = pacmanUpImage;
//         }
//         else if (pacman.direction == 'D') {
//             pacman.image = pacmanDownImage;
//         }
//         else if (pacman.direction == 'L') {
//             pacman.image = pacmanLeftImage;
//         }
//         else if (pacman.direction == 'R') {
//             pacman.image = pacmanRightImage;
//         }
//     }

//     @Override
//     public void keyReleased(KeyEvent e) {}

    




// }