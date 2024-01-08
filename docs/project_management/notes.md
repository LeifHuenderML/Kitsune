alpha go zero differs in a few ways
- trained only by selp play reinforcement learning, starting from random play, without any hubman data
- only used the black and white stones as input features
- uses only 1 neural network
- simpler tree search that relies on this single neural network, to evaluate positions and samplue moves
- no monte carlo rollouts
- uses new reinforcement learning algorithm that does lookahead search inside the training loop
    - this results in a more rapid improvement and precise stable learaning

reinforcement learning
- takes in board representation, with position, and history
- outputs move probabilities and value
- move probabilities is a vector, (includes pass)
- the value is a scalar evaluation that estimates current player probability of winning prom position s
- consist of many residual blocks of convolutionnal layers, with batch normalization, and rectifier nonlinearities
- the residual blocks help avoid vanishing gradients
- convolutional layers are used to capture the spatial representation
- batch normalization improves speed and performance and stability by normalizing the inputs to a layer for each mini-batch, which results in reduced training epochs
- ReLU activation function is a non linear operation that allows the model to learn more complex patterns from the data
- Zero is trained through self play
- in each position s a monte carlo tree search is executed that is guided by the neural network
- the mcts outputs probabilities of playing each move
    - this ends up selecting much stronger moves probabilities that nthe raw move probabilites p o the nn
- the mcts is a powerful polisy improvement operator

monte carlo search tree
- is a heuristic search algorithm for som kind of decision processes
- a powerful algorithm for making desions related to playing board games due to the nature of it
- in a generalized sense it uses random sampling, combined with a tree search to build possible moves, it uses the results of its exploration to decide the most promising move to make

alpha go zero mcts
- in relation to alpha zero it is guided by the nn instead of random sampling 
- each edge (s,a) in the search tree stroes a prior probability P(s,a) , a visit count N(s,a) and an action-value Q(s,a)
- each simulation starts from the root state and iteratively chooses moves that maximize an upper confidience bound Q(s,a) + U(s,a) where U(s,a) is proportional to the ratio of P(s,a)/(1+N(s,a))
- this happens until a leaf node is encountered
- the leaf node is expanded and evaluated just once by the network to generate both prior probabilities and evaluation
- then each edge (s,a) traversed in the simulation updates its visit count, and updates its action-value to the mean evaluation over the simulations
- the mcts is essential a self play algorithm that given nn params and position s computes a vestor of search probabilities reccomendingmoves to play 

zero nn
- trained by the self play algorithm using mcts to make each move
- initialized with random weights
- during each iteration games of selp play are generated
- a game terminates when both players pass, when the search value drops below a resignation threshold, or when the game exceeds a max lenght
- the game is then scored for a final reward 
- the data for each time step is stored as (s_t, pi_t, z_t) where z_t = + or - r_t which is the game winner from the perspective of the current player at time t
- in a parralel process new nn parameters are trained from data (s,pi,z) sampled uniformly among all time steps of the last iterations of selp play
- the nn is adjusted to minimise the error between the predicted value and the selp play winner z, and to maximize the similarity of the neural network move probabilites p to the search probabilites pi,
- these parameters are adjusted by gradient descent on a loss function l that sums over the mse and crossentropy loss 

training zero nn
- took 3 days, 4.9 million games of self play, using 1600 simulations for each mcts
- params were updated from 700,000 min batches of 2,048 positions, 
- the nn contained 20 residual blocks
- uses Tromp-Taylor scoring during mcts simulationss, however all tournaments it played in were scroed using chinese rules
- terminates game after 19*19*2 moves are made 
- started with random initial training paramers
- 