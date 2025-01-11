Bilinear interpolation is a method used to estimate the value of a function at a point within a 2D grid based on the values of the function at the grid's surrounding points. It is a straightforward extension of linear interpolation to two dimensions.

---

### How It Works
Suppose you have a rectangular grid with known values at four corners, and you want to find the interpolated value at a point inside this rectangle.

1. **Grid and Points**:
   - Let the four known points on the grid be $(x_1, y_1), (x_2, y_1), (x_1, y_2), (x_2, y_2)$, with values $Q_{11}, Q_{21}, Q_{12}, Q_{22}$, respectively.
   - The point where you want to interpolate the value is $(x, y)$, where $x_1 \leq x \leq x_2 \) and \( y_1 \leq y \leq y_2$.

2. **Two-Step Process**:

   - **Step 1: Interpolate along the x-direction** at fixed $ y_1$ and $y_2$:
     - At $y_1$: Interpolate between $Q_{11}$ and $Q_{21}$:
  
$$
       \[
       Q_{x1} = \frac{(x_2 - x)}{(x_2 - x_1)} Q_{11} + \frac{(x - x_1)}{(x_2 - x_1)} Q_{21}
       \]
$$

     - At $y_2$: Interpolate between $Q_{12}$ and $Q_{22}$:

$$
       \[
       Q_{x2} = \frac{(x_2 - x)}{(x_2 - x_1)} Q_{12} + \frac{(x - x_1)}{(x_2 - x_1)} Q_{22}
       \]
$$

   - **Step 2: Interpolate along the y-direction** between $Q_{x1}$ and $Q_{x2}$:

$$
     \[
     Q(x, y) = \frac{(y_2 - y)}{(y_2 - y_1)} Q_{x1} + \frac{(y - y_1)}{(y_2 - y_1)} Q_{x2}
     \]
$$

---

### Key Features

- **Linear Interpolation in Two Directions**: The method performs linear interpolation first in one direction (x) and then in the other direction (y).
- 
- **Smooth Transition**: Bilinear interpolation gives a smooth result, as it is based on weighted averages of nearby points.
- 
- **Grid Constraints**: It assumes a rectangular grid structure and requires the function values at four corners of the rectangle enclosing the interpolation point.

---

### Example

If you have the grid:

$$
\begin{array}{c|c|c}
      & x_1 = 0 & x_2 = 1 \\
\hline
y_1 = 0 & Q_{11} = 10 & Q_{21} = 20 \\
y_2 = 1 & Q_{12} = 30 & Q_{22} = 40 \\
\end{array}
$$

To find the value at $(x, y) = (0.5, 0.5)$:
1. Interpolate along $x$ for $y = 0$: $Q_{x1} = (10 + 20)/2 = 15$.
2. Interpolate along $x$ for $y = 1$: $Q_{x2} = (30 + 40)/2 = 35$.
4. Interpolate along $y$ : $Q(0.5, 0.5) = (15 + 35)/2 = 25$.

---

Bilinear interpolation is widely used in image processing, computer graphics, and numerical simulations for tasks like resizing images or filling missing data.