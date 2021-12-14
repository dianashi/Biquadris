# Biquadris
A two player Tetris based Biquadris game. 

## Introduction
This project is build with two other people for the final project of an OOP course at the University of Waterloo. With the implementations of multiple bonus features, our group earned 110% for this project. Compared to Tetris, Biquadris is not a real-time game. Players have as much time as they want to decide where to place the next block. Players will take turns dropping blocks, one at a time [if special actions are not triggered]. During a player's turn, the blocks that the opponent will have to play next is already displayed at the top of the opponent's board. 

<img width="463" alt="截屏2021-12-14 下午2 20 32" src="https://user-images.githubusercontent.com/38364680/146088570-6c3c180e-b130-4c18-aeb1-e55379303b64.png">

## Design
Instead of having different modules taking charge of inputting and outputting, we designed a main function that implements command line arguments, takes/interprets all input commands, reads sequence/command files, and directs standard output and error. It creates a Controller class that initializes and stores two pointers to players’ Grids, a pointer to TextDisplay as well as another pointer to GraphicsDisplay. Thus, the main function calls commands on the controller, which then dissects the commands if necessary and calls commands on their corresponding Grid. The Grids therefore manage current blocks and generate next blocks. Specific implementations on essential modules are as follows:
- ### Displays
  We utilized the [Observer design pattern](https://en.wikipedia.org/wiki/Observer_pattern) and created an abstract Observer class which manages all the displays. Two children classes, TextDisplay and GraphicsDisplay, inherit from Observer and override functions in Observer. Grid stores Observer pointers and notify its Observers whenever it changes.
  
  Thus, if we were to create a new display, we could write a new class that inherits from Observer and store the pointer to that new display class in Grid. By doing this, besides implementing the new display, attaching another pointer to Grid would be the only change needed to create a new display; Grid could still call the virtual notify in Observer to make any changes. 
- ### Blocks
  Firstly, we changed the return value for the virtual makeBlock(Grid &, CellType) function from CellType to std::shared_ptr<Block>. Thus, every time we try to call this function in the Gird class, we could simply return a pointer of a Block instead of using a switch statement and make a block based on the given CellType.
  
  We also added a reference to the Grid as a private field, so the concrete Baseblock class takes responsibility for checking if it is valid to move or rotate the current block. Since the blocks are vectors of Point structures, we noticed that all types of blocks except for O and I are rotated in order as a 2 X 3 grid where a set of specific Points have their state being true based on the current block’s CellType. Hence, it is necessary to have a getter function of the current block’s point, so the grid could have access to the field and check if it should notify the Cell to turn on or not.
  
  In addition, we added some public functions for the Heavy feature since it is applied to individual blocks. To satisfy the scoring scheme, we also have an addCount(): Void function. When one of the cells of a block is cleared, we call the addCount() to increment the number of cleared cells in a block. When it reaches 4, we know the entire block has been cleared so we could add specific scores based on the level we were in when the block was generated.
- ### NextBlock & Levels
  Similar to the BaseBlock class, Levels are concrete subclasses that inherit from the NextBlock class. Each level is defined in its own subclasses with override method makeNextBlock(Grid & grid): std::shared_ptr<Block>. Once the user changes the level, we create a new class for Nextblock in Grid and call the makeNextBlock(Grid & grid) function on the class, we could then update the next block after the shown next block (which is not generated yet) accordingly.

- ### Special Actions
  Special Actions used the [Decorator design pattern](https://en.wikipedia.org/wiki/Wikipedia:What_Wikipedia_is_not#GUIDE) and it acts like a wrapper on the BaseBlock. It enables behaviours to be applied to the BaseBlock without adding new functions or fields to the BaseBlock; instead, when certain commands are called on Block, it first applies some effects on the BaseBlock before calling the commands on BaseBlock.
  
  For example, for the special action, Heavy, it makes the opponent's current Block heavy (falls by two rows whenever left or right is called). We achieved this by overriding Block’s moveLeft and moveRight functions. We modified them to call down on the BaseBlock twice before calling moveLeft or moveRight.
  
  We also designed each special action as concrete classes inherited from the abstract Decorator class. Thus, if we want to create a new special action, no changes are needed to make to other special actions classes.
  
## High Cohesion and Low Coupling
  Our program followed the high cohesion and low coupling designing principle. We tried to structure a module so that each of the elements in that module has functionalities that belong together and serve the same purpose (High Cohesion). We also ensured that we keep dependencies between modules as few as possible (Low Coupling).

[Demo.pdf](https://github.com/dianashi/Biquadris/files/7715186/Demo.pdf)

