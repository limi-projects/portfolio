---
title: "Monte Carlo"
---

# Monte Carlo Simulations

Monte Carlo simulations use (pseudo)random selection to aproach an accurate solution. 
Since the sampling is randomised, the accuracy of a Monte Carlo simulation increases with the number of samples, at the cost of an increased computational load.

## Example: ${\pi}$
The value of ${\pi}$ may be approximated using a Monte Carlo simulation. 

1. Consider a circle with a radius of ${r}$, bounded by a square.
   The ratio of the area of the circle and the area of the square is:
   ${\frac{\pi r^{2}}{4r^{2}} = \frac{\pi}{4}}$.
2. Symmetry reduce the problem to a quadrant, enclosed by a smaller square of width r.
   The ${\frac{\pi}{4}}$ ratio still holds in this configuration.
4. Consider that the circle and square are centred about an origin (0,0) and that ${r=1}.
5. Sample randomly within this space, recording the coordinates.
6. Via the Pythagorean theorem (${a^{2} + b^{2} = c^{2}}$).
   Samples that have a disance from the origin of less than, or equal to, 1 must be within the quadrant.
   Categorise each sample accordingly.
7. Divide the number of samples within the quadrant by the total number of samples.
   This is effectively producing the same circle/square division calculation, as above,
   albeit using pointwise sampling of the area.
8. Multiply the result by 4, to estimate ${\pi}$.

### Representation

![montecarlo_plot.png](montecarlo_plot.png)

### Analysis 
As shown, the approximation improves as the number of samples is increased, at the cost of an increased duration of the calculation. The green line represents an accurate and precise value of ${\pi}$, the standard to compare each estimate.

![montecarlo_analyse.png](montecarlo_analyse.png)

### Python code

#### ```montecarlo.py```
```python
from random import uniform

def estimate_pi(samples):
    '''Estimate a value of pi for a given number of samples.'''
    
    inside = 0
    x_values = []
    y_values = []
    for sample in range(samples):
        x, y = uniform(0,1), uniform(0,1)
        x_values.append(x)
        y_values.append(y)
        if x**2 + y**2 <= 1:
            inside += 1
    
    estimate = (inside/samples)*4

    return estimate, x_values, y_values
```

#### ```montecarlo_plot.py```
```python
from montecarlo import estimate_pi 
from numpy import pi, cos, sin, linspace
import matplotlib.pyplot as plt

plt.style.use('dark_background')
fig, ax = plt.subplots()

radius = 1
arc = pi/2
theta = linspace(0, arc, 50)
cx, cy = radius*cos(theta), radius*sin(theta)

est, x, y = estimate_pi(1_000)
ax.scatter(x, y, color = 'white', marker = 'x')
ax.plot(cx, cy, color = 'red')
ax.set_title(f'Estimate = {est}')
plt.savefig('montecarlo_plot.png')
plt.show()
```

#### ```montecarlo_analyse.py```
```python
from montecarlo import estimate_pi
import matplotlib.pyplot as plt
from math import log10
from numpy import pi
from time import strftime

sample_totals = [10, 100, 1_000, 10_000, 100_000, 1_000_000, 10_000_000]
log_sample_totals = [log10(samples) for samples in sample_totals]
pi_line = [pi for samples in sample_totals]

estimates, timestamps = [], []
for samples in sample_totals:
    estimates.append((estimate_pi(samples)[0]))
    timestamps.append(strftime('%S'))

plt.style.use('dark_background')
fig, (ax1, ax2) = plt.subplots(2, 1, sharex=True)

ax1.set_xlabel('log10(Number of samples)')
ax1.set_ylabel('Estimate')
ax1.plot(log_sample_totals, estimates, color = 'red', marker = '.')
ax1.plot(log_sample_totals, pi_line, color = 'green')

ax2.set_xlabel('log10(Number of samples)')
ax2.set_ylabel('Simulation Duration / s')
ax2.plot(log_sample_totals, timestamps, color = 'blue', marker = '.')

plt.savefig('montecarlo_analyse.png')
plt.show()
```

## Example: Special Quasi-Random Structures
The thermodynamic properties of an alloy may be simulated using a Monte Carlo simulation. 
The simulation involves using a _cluster expansion_ to create a model crystal structure that represents the thermodynamic properties of the alloy.
The key assumption of this method is that the real alloy is, and is always, a completely homogeneous mixture. This may not always be true, but is sufficient in many circumstances.

In the cluster expansion method, groups of atoms that compose the alloy are randomly selected and combined in various configurations.
Each configuration can be compared with an abstract, ideal confirguration by using correlation functions, to rank their representation of the real alloy.
Every possible configuration within the cluster can then be considered and the size of the cluster can be gradually increased until the necessary level of accuracy is obtained.
Whilst this method is sure to converge to a solution, it is often more expedient to randomly sample configurations and cluster sizes via a Monte Carlo simulation; improving the cluster model as it gradulally exap
  
