# Personal Project: Design Notebook

Welcome to my design notebook! Below are my realizations and design changes, listed per-assignment and in the order I considered and resolved them.

## P1: Design

**Breakpoint 1: Adding annotation.** After further considering the existing multiplayer Minesweeper apps, I decided that some "Annotating" functionality would be quite helpful and allow for easier collaboration, in support of my goal to make the game more cooperative. [Minesweeper Together](https://store.steampowered.com/app/3550060/Minesweeper_Together/) was a comparable app I considered in Exercise 1, and it allows users to draw on the board to communicate/solve together when they are stuck. I am a fan of the easy-to-use annotations offered on [Chess.com](https://www.chess.com/) which allow you to highlight squares and draw paths, so I think incorporating a similar highlighting feature would be a good choice. In terms of concept design, I also think this is separate from game functionality, rooming, etc. as I introducted in my Exercise, so it could be organized into its own general concept `Annotating [User, Item]`. I still need to decide how the user would place down annotations, since both left-click and right-click already have standard functionality for game-playing.

<hr>

**Breakpoint 2: Splitting into concepts.** After deciding key features, I got a bit stuck on trying to split functionalities into individual modular concepts. 

I decided `RoomJoining [User, Game]` could definitely be its own concept, as the functionality for creating, joining, and closing a room was orthogonal to playing a game, and creating/starting the game can be triggered via reactions.

Then, I considered how to encode gameplay into concepts. I decided one large `MinesweeperPlaying` concept was the best way to group game creation, cell reveals, cell flags, game state, and time/statistics; this is because all such actions will depend on one shared board state, and splitting into different concepts would create complex dependencies. I was partially considering against this design decision because it would require one lengthy concept, but since the Minesweeper game/moves are largely self-contained and not subject to change, it would make sense to encode them together in this way.

Beyond gameplay functionality, storing/ranking statistics is a separate functionality I want to incorporate, since it is a useful performance indicator on [Minesweeper.online](https://minesweeper.online/). Storing and ranking games requires no specific structure on the game or its associated fields, so I think `PerformanceRanking [Item, Metric]` would be sufficient, i.e. each Item is tied to a list of Metrics it can be filtered/ranked by.

Lastly, I considered incorporating Annotation as a concept, and I wanted to stick with my initial generic `Annotating [User, Item]` idea which can associate an item to an annotation made by a user, or remove/manage such annotations. 

<hr>

**Breakpoint 3: Type parameters revision.** I decided `PerformanceRanking [Item, Metric]` wasn't generic enough for the purpose of ranking, because I also want players to be able to filter by room, board type, and possibly any other additional properties. So, I am expanding to `PerformanceRanking [Item, Scope, Category, Metric]`, where `Scope` will be a coarse filter applied (ex. filtering by room), which can be subdivided by `Category` (ex. board size). Each `Metric` will still maintain its same functionality, where we can view/rank by its value.

I also decided `RoomJoining [User, Game]` only needs to be `RoomJoining [Game]`, because part of my goal is to make games easily shareable without a player needing to have an established user identity. Instead, Rooms will just have sets of Participants each with their own display name.

<hr>

**Breakpoint 4: 3BV in Minesweeper state.** I decided against including board-derived metrics like 3BV in the `MinesweeperPlaying` state, because although they are properties of the board, they are not essential to the "playing" functionality and instead pertain more to `PerformanceRanking`. I chose to expose a `_getResult` query action which can compute and return all performance metrics based on the stored state of the game, which a reaction can retrieve all at once and feed into `PerformanceRanking`. I think this cleanly isolates the functionality of generating statistics from the functionality of playing.

<hr>

**Breakpoint 5: Create vs. reveal actions.** I was debating where exactly the action of "starting" a game should go, because there are a couple steps required to begin playing. I was first considering making the `create` action place mines and start the timer, and subsequent `reveal`s proceed on a created board. But, typically Minesweeper boards place mines on cells distinct from your first click location (to ensure you don't instantly lose), and the timer starts at your first click. Instead, I made the `reveal` action also place mines and start the timer on the first click, and the `create` action just initializes the Game object. This also creates a good separation for the lobby + game UI's, since `create` can move players to the game screen, and the game will only actually start at the first `reveal`.

<hr>

**Breakpoint 6: Room closing and hosting.** While thinking through my `RoomJoining` concept, I was considering how whole-group actions like starting the game should occur, which I figured would be handled by a host (person who creates the room). But it is also possible for the host to leave, so I chose to add arbitrary host reassignment for when this happens. I'm not sure whether this is the best option right now (perhaps a new player doesn't want to host), but I think it would be easy to modify within the `RoomJoining` concept if I want to change this logic later. I also decided that a room would close automatically when everyone leaves, which makes sense as there is no longer a host or any active participants.

<hr>

**Breakpoint 7: UI design choices.** When working on the UI sketches, I was unsure where exactly to place each game-related functionality, since I also needed to incorporate annotation, and actions for game restart. I think a UI that has as few additional/unfamiliar buttons as possible is preferred, so I chose to integrate both of these just into the Minesweeper board itself. A center-click would add annotations, and the host would be able to click the top bar of the board to start a new game (also a function in popular Minesweeper sites). I was alternatively considering having a "toggle annotation mode" button at the side of the board, but I think this would be inconvenient for players who just want to quickly reference a part of the board.
