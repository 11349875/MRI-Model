# MRI-Model - Max Bongers

The github repository consists of several files:

**Data map**
- Every csv file here is data from the experiment that was run.
- The json files contain the cell membrane data, where each wall is defined. The way the walls are defined in these files are important. Every wall is made out of two points, connecting each end. Currently, the collision logic is done assuming the walls are defined clock-wise (point 1 connecting to point 2 in a clockwise fasion, where '12' on the clock would be the paralell to the positive Y-axis, and '6' on the clock would be paralell to the negative Y-axis.) It is possible to change this, but you would need to change some of the angles in the collision detection.

The collision detection is somewhat seperate from the collision detection, so you could also implement your own, and only use the MRI part.

**CLS_Thesis_Max_Bongers**\
The thesis on this model

**Analytical dmipy**\
Contains code that uses a package to analytically determine signal loss given restricted diffusion. This can be used to compare it to the model outcome.

**Analytical tests model**\
Contains code to determine the signal loss given restricted diffusion using the model. This code is outdated, although it will work fine for testing signal loss in an impermeable infinite cillinder. This code should not be used for other purposes than this.

**Significance tests**\
Code used to test significance of results of my thesis.

**MRI Monte Carlo model**

Contains the main MRI model. Most of the code is commented and the use is straightforward, but I will give a short tutorial here.

The main function that is used for simulations is 'simulate', which takes many parameters, some of which can be defined by the user. The b-value cannot be given directly, instead it is defined by three input parameters: gradient strength, gradient duration and gradient interval.
Running the function can be done with free diffusion or not, aditionally the gradient can be turned on or off. The function currently runs only until TE, where the signal attenuation is measured, both TE and the length of the simulation can be extended by simply changing the corresponding values. After running the simulate function with the chosen parameters, the program will automatically distribute the task over the available cores on your system. This can also be done with a 
single core, although this will be considerably slower. 

The function outputs the transversal and longitudinal magnetisation as a list of values, each time step has 1 value. The code can also be slightly adapted to include the trajectory of each spin, however this will make the code extremely slow, which is why the function simply returns a '_' in the place of it. Using the magnetisation values, the b-value or diffusion coefficient is estimated by calculating the signal attenuation. Currently the simulation only works for a single PGSE sequence, it is possible to run a simulation with multiple RF pulses, but the results will be wrong.

For understanding the code and some choices made, I would refer to the third chapter of my thesis. However, below is some extra information and thoughts on the code, in no particular order.
-  The environment used for the notebooks are in Anaconda3 2022.05, using python 3.9.
-  The code is designed for linux systems, meaning some commands are used that are linux only (mainly, to check how many cores are available for multithreading), but it should work fine on windows if you manually put in the amount of cores.
-  Some of the packages that are imported are not used, somewhere during my project I did use them, but never removed them from the import list.
-  Currently the program runs everything on CPU, there are a lot of matrix operations, so perhaps running it on a GPU could be faster (but probably not)
-  Some of the cell collision logic might seem strange, this is due to an early implementation that was never changed.
-  Broadly collision with cell membranes works as follows:
1. Check whether or not a spin is inside a cell
2. If it is then only check the walls of the cell for collision
3. If it's not, check the walls close to the spin for collision
4. Reflect the spin if its step makes it collide with a wall, this reflection changes dependent on whether
you're inside or outside of a cell.

-  There are comments inside check_cell functions, which you can uncomment to plot the collisions of spins (recommended to only run with 1 spin), this was mostly used to bugfix during collision testing.
-  The logic for checking whether or not the gradient has to be turned on at a certain time point is very convoluted in the current code. This is because I made it for different TRs (multiple RF pulses), however this does currently not entirely work, the resulting signal is not what it is supposed to be. The time that the RF pulses and gradients happen are correct however.
-  My idea for multiple RF pulses was as follows; Every spin gets the rotation of the RF pulse applied each TR, relaxation rotates the spin back to its equilibrium position. Important here to keep the rotation around Z into account when rotating around Y.
-  Any questions you can contact me at maxbongers@hotmail.nl

