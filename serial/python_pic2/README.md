Skeleton 2D Electrostatic Particle-in-Cell (PIC) codes
by Viktor K. Decyk
copyright 1994-2013, regents of the university of california

This program contains sample codes for illustrating the basic structure
of a 2D Electrostatic Particle-in-Cell (PIC) code in Python.  The codes have no diagnosics except for initial and final
energies.  Their primary purpose is to provide example codes for
physical science students learning about PIC codes.  They are also
intended as benchmark reference codes to aid in developing new codes and
in evaluating new computer architectures.

PIC codes are widely used in plasma physics.  They model plasmas as
particles which interact self-consistently via the electromagnetic
fields they themselves produce.  PIC codes generally have three
important procedures in the main iteration loop.  The first is the
deposit, where some particle quantity, such as a charge, is accumulated
on a grid via interpolation to produce a source density.  The second
important procedure is the field solver, which solves Maxwell�s equation
or a subset to obtain the electric and/or magnetic fields from the
source densities.  Finally, once the fields are obtained, the particle
forces are found by interpolation from the grid, and the particle
co-ordinates are updated, using Newton�s second law and the Lorentz
force.  The particle processing parts dominate over the field solving
parts in a typical PIC application. 

More details about PIC codes can be found in the texts by C. K. Birdsall
and A. B. Langdon, Plasma Physics via Computer Simulation, 1985,
R. W. Hockney and J. W. Eastwood, Computer Simulation Using Particles,
1981, and John M. Dawson, "Particle simulation of plasmas", Rev. Mod.
Phys. 55, 403 (1983).  Details about the mathematical equations and
units used in this code is given in the companion article,
"Description of Electrostatic Spectral Code from the UPIC Framework" by
Viktor K. Decyk, UCLA, in the file ESModels.pdf.

No warranty for proper operation of this software is given or implied.
Software or information may be copied, distributed, and used at own
risk; it may not be distributed without this notice included verbatim
with each file.  If use of these codes results in a publication, an
acknowledgement is requested.

The code here uses the simplest force, the electrostatic Coulomb
interaction, obtained by solving a Poisson equation.  A spectral method
using Fast Fourier Transforms (FFTs) is used to solve the Poisson
equation.  A real to complex FFT is used, and the data in Fourier space
is stored in a packed format, where the input and output sizes are the
same.  The boundary conditions are periodic, only electron species are
included, and linear interpolation is used.

Particles are initialized with a uniform distribution in space and a
gaussian distribution in velocity space.  This describes a plasma in
thermal equilibrium.  The inner loop contains a charge deposit, an add
guard cell procedure, a scalar FFT, a Poisson solver, a vector FFT, a
copy guard cell procedure, a particle push, and a particle sorting
procedure.  The final energy and timings are printed.  A sample output
file for the default input parameters is included in the file output.

In more detail, the inner loop of the code contains the following
procedures in Fortran (C):

Deposit section:
   deposit2: deposit charge density
   guard_cells: add charge density guard cells

Field solve section:
   fourier_rho: FFT charge density to fourier space
   fourier_force: calculate smoothed longitudinal electric field in
                     fourier space.
   real_force: FFT smoothed electric field to real space

Particle Push section:
   guard_force: fill in guard cells for smoothed electric field
   push_particles: update particle co-ordinates with smoothed
                       electric field:
                       x(t)->x(t+dt); v(t-dt/2)->v(t+dt/2)

The inputs to the code are the grid parameters indx, indy, the particle
number parameters npx, npy, the time parameters tend, dt, the velocity
parameters vtx, vty, vx0, vy0, and the sorting parameter sortime.

In more detail:
indx = exponent which determines length in x direction, nx=2**indx.
indy = exponent which determines length in y direction, ny=2**indy.
   These ensure the system lengths are a power of 2.
npx = number of electrons distributed in x direction.
npy = number of electrons distributed in y direction.
   The total number of particles in the simulation is npx*npy.
tend = time at end of simulation, in units of plasma frequency.
dt = time interval between successive calculations.
   The number of time steps the code runs is given by tend/dt.
   dt should be less than .2 for the electrostatic code.
vtx/vty = thermal velocity of electrons in x/y direction
   a typical value is 1.0.
vx0/vy0 = drift velocity of electrons in x/y direction.
sortime = number of time steps between electron sorting.
   This is used to improve cache performance.  sortime=0 to suppress.

Require Python libraries:

numpy
matplotlib
numba

The major program files contained here include:
Pic2.py     Python main program 
push.py     Python procedure Library


To run the program, type

Python Pic2

into command prompt.