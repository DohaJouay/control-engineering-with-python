% Ordinary Differential Equations
% Sébastien Boisgérault, Mines ParisTech

Preamble
--------------------------------------------------------------------------------

::: slides :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

    from numpy import *
    from numpy.linalg import *
    from matplotlib.pyplot import *
    from mpl_toolkits.mplot3d import *

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::: notebook :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

    from numpy import *
    import matplotlib; matplotlib.use("nbAgg")
    %matplotlib inline
    from matplotlib.pyplot import *

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::


::: hidden :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

    # Python 3.x Standard Library
    import gc
    import os

    # Third-Party Packages
    import numpy as np; np.seterr(all="ignore")
    import numpy.linalg as la
    import scipy.misc
    import matplotlib as mpl; mpl.use("Agg")
    import matplotlib.pyplot as pp
    import matplotlib.axes as ax
    import matplotlib.patches as pa


    #
    # Matplotlib Configuration & Helper Functions
    # --------------------------------------------------------------------------
    
    # TODO: also reconsider line width and markersize stuff "for the web
    #       settings".
    fontsize = 35

    rc = {
        "text.usetex": True,
        "pgf.preamble": [r"\usepackage{amsmath,amsfonts,amssymb}"], 
        #"font.family": "serif",
        "font.serif": [],
        #"font.sans-serif": [],
        "legend.fontsize": fontsize, 
        "axes.titlesize":  fontsize,
        "axes.labelsize":  fontsize,
        "xtick.labelsize": fontsize,
        "ytick.labelsize": fontsize,
        #"savefig.dpi": 300,
        #"figure.dpi": 300,
    }
    mpl.rcParams.update(rc)

    # Web target: 160 / 9 inches (that's ~45 cm, this is huge) at 90 dpi 
    # (the "standard" dpi for Web computations) gives 1600 px.
    width_in = 160 / 9 

    def save(name):
        cwd = os.getcwd()
        root = os.path.dirname(os.path.realpath(__file__))
        os.chdir(root)
        pp.savefig(name + ".svg")
        os.chdir(cwd)

    def set_ratio(ratio=1.0, bottom=0.1, top=0.1, left=0.1, right=0.1):
        height_in = (1.0 - left - right)/(1.0 - bottom - top) * width_in / ratio
        pp.gcf().set_size_inches((width_in, height_in))
        pp.gcf().subplots_adjust(bottom=bottom, top=1.0-top, left=left, right=1.0-right)

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::


Introduction
================================================================================

Vector Field
--------------------------------------------------------------------------------

Let $n \in \mathbb{N}^*$ and $f:\mathbb{R}^n \to \mathbb{R}^n$. 

Visualize $f(x)$ as an arrow with origin the point $x$.

In the plane ($n=2$), use [quiver](https://matplotlib.org/3.1.1/api/_as_gen/matplotlib.pyplot.quiver.html) (Matplotlib).


Q Helper
--------------------------------------------------------------------------------

    def Q(f, xs, ys):
        X, Y = meshgrid(xs, ys)
        fx = vectorize(lambda x, y: f([x, y])[0])
        fy = vectorize(lambda x, y: f([x, y])[1])
        return X, Y, fx(X, Y), fy(X, Y)

<i class="fa fa-eye"></i> Vector Field / Rotation
--------------------------------------------------------------------------------

Consider $f(x,y) = (-y, x).$

    def f(xy):
        x, y = xy
        return array([-y, x])

<i class="fa fa-area-chart"></i>
--------------------------------------------------------------------------------

    figure()
    x = y = linspace(-1.0, 1.0, 20)
    gca().set_aspect(1.0); grid(True)
    quiver(*Q(f, x, y)) 

::: hidden :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

    save("images/test_Q")

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

--------------------------------------------------------------------------------

::: slides :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

![](images/test_Q.svg)

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

Ordinary Differential Equations (ODEs)
--------------------------------------------------------------------------------

A solution of $\dot{x} = f(x)$ is

- a function $x:I \to \mathbb{R}^n$,

- defined on a (possibly unbounded) interval $I$ of $\mathbb{R}$,

- such that for every $t \in I,$

  $$\dot{x}(t) = dx(t)/dt = f(x(t)).$$

<i class="fa fa-area-chart"></i>
--------------------------------------------------------------------------------

    figure()
    x = y = linspace(-1.0, 1.0, 20)
    gca().set_aspect(1.0); grid(True)
    streamplot(*Q(f, x, y), color="k") 


::: hidden :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

    save("images/test_Q2")

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

--------------------------------------------------------------------------------

::: slides :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

![](images/test_Q2.svg)

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::


Initial Value Problem (IVP)
--------------------------------------------------------------------------------

Solutions $x(t)$, for $t\geq t_0$, of

$$
\dot{x} = f(x)
$$

such that

$$
x(t_0) = x_0 \in \mathbb{R}^n.
$$


--------------------------------------------------------------------------------

The **initial condition** $(t_0, x_0)$ is made of 

  - the **initial time** $t_0 \in \mathbb{R}$ and 

  - the **initial value** or **initial state** $x_0 \in \mathbb{R}^n$.
 
The set $\mathbb{R}^n$ is the **state space**,  
$x(t)$ the **state at time** $t$.


Higher-Order ODEs
--------------------------------------------------------------------------------

Scalar differential equations structured as

  $$
  x^{(n)}(t) = f(x, \dot{x}, \ddot{x}, \dots, x^{(n-1)})
  $$

can be converted to the standard form with the state
 
  $$
  y = (x, \dot{x}, \ddot{x}, \dots, x^{(n-1)}) \in \mathbb{R^n}
  $$

--------------------------------------------------------------------------------

  $$
  \begin{array}{ccl}
  \dot{y}_1 &=& y_2 \\
  \dot{y}_2 &=& y_3 \\
  \vdots &\vdots& \vdots \\
  \dot{y}_n &=& f(y_1, y_2, \dots, y_{n-1})
  \end{array}
  $$


<i class="fa fa-eye"></i> Pendulum
--------------------------------------------------------------------------------

![](images/static/pendulum.svg)

<i class="fa fa-question-circle-o"></i> -- Model / Pendulum
--------------------------------------------------------------------------------

  - [</i><i class="fa fa-gear"></i>, </i><i class="fa fa-superscript"></i>] 
    Establish the equations governing the pendulum dynamics 
    when the mechanical energy of the system is constant.  

  - [</i><i class="fa fa-gear"></i>, </i><i class="fa fa-superscript"></i>] 
    Generalize the dynamics when there is a friction torque
    $c = -b \dot{\theta}$ for some $b \geq 0$.

### <i class="fa fa-key"></i> -- Result

$$
m \ell^2 \ddot{\theta} + b \dot{\theta} + m g \ell \sin \theta = 0
$$

--------------------------------------------------------------------------------

Introduce the rotational frequency $\omega = \dot{\theta}$ in rad/sec. 

The pendulum dynamics is equivalent to:

  $$
  \begin{array}{lll}
  \dot{\theta} &=& \omega \\
  \dot{\omega} &=& - (b/m\ell^2) \omega -(g /\ell) \sin \theta  
  \end{array}
  $$

--------------------------------------------------------------------------------

    m=1.0; b=0.0; l=1.0; g=9.81
    def f(theta_d_theta):
        theta, d_theta = theta_d_theta
        J = m * l * l
        d2_theta  = - b / J * d_theta 
        d2_theta += - g / l * sin(theta)
        return array([d_theta, d2_theta])
            

<i class="fa fa-area-chart"></i>
--------------------------------------------------------------------------------

    figure()
    theta = linspace(-1.5 * pi, 1.5 * pi, 100)
    d_theta = linspace(-5.0, 5.0, 100)
    grid(True)
    xticks([-pi, 0, pi], [r"$-\pi$", "$0$", r"$\pi$"])
    streamplot(*Q(f, theta, d_theta), color="k") 


::: hidden :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

    save("images/streamplot_pendulum")

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

--------------------------------------------------------------------------------

::: slides :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

![](images/streamplot_pendulum.svg)

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

<i class="fa fa-question-circle-o"></i> -- Model / Pendulum
--------------------------------------------------------------------------------

  - [<i class="fa fa-flask"></i>, <i class="fa fa-area-chart"></i>] 
    Determine an approximation of the least possible angular velocity 
    $\omega_0 > 0$ such that when $\theta(0) = 0$ and 
    $\dot{\theta}(0) = \omega_0$, the pendulum reaches (or overshoots)
    $\theta(t) = \pi$ for some $t>0$.

  - [<i class="fa fa-lightbulb-o"></i>, <i class="fa fa-superscript"></i>] 
    Answer the same question analytically.

Numerical Solution (Basic)
--------------------------------------------------------------------------------

Given a finite **time span** $[t_0, t_f]$ and a small enough **time step**
$\Delta t > 0$, we can use the approximation:

  $$
  \begin{split}
  x(t + \Delta t) 
    & \simeq x(t) + \Delta t \times \dot{x}(t) \\
    & = x(t) + \Delta t \times f(x(t)) \\
  \end{split}
  $$

to compute a sequence of states $x_k \in \mathbb{R}^n$ such that:

  $$
  x(t = t_0 + k \Delta t) \simeq x_k.
  $$


Euler Scheme
--------------------------------------------------------------------------------

(Fixed-step & explicit version) 

    def basic_solve_ivp(f, t0, x0, dt, t_f):
        ts, xs = [t0], [x0]
        while ts[-1] < t_f:
            t, x = ts[-1], xs[-1]
            t_next, x_next = t + dt, x + dt * f(x)
            ts.append(t_next); xs.append(x_next)
        return (array(ts), array(xs).T)

--------------------------------------------------------------------------------

Why the final transposition (`.T`)? 

So that when

`t, x = basic_solve_ivp(...)`,

`x[i]` refers to the values of the `i`th component of `x`.

(without the `.T`, it would be `x[:][i]`)



<i class="fa fa-eye"></i> Rotation / Solution
--------------------------------------------------------------------------------

  $$
  \left|
  \begin{split}
  \dot{x} &= -y \\
  \dot{y} &= +x
  \end{split}
  \right.,
  \; \mbox{ with } \;
  \left|
  \begin{array}{l}
  x(0) = 1\\
  y(0) = 0
  \end{array}
  \right.
  $$


--------------------------------------------------------------------------------

    def f(xy):
        x, y = xy
        return array([-y, x])
    t0, x0 = 0.0, array([1.0, 0.0])
    dt, tf = 0.001, 5.0
    t, x = basic_solve_ivp(f, t0, x0, dt, tf)

<i class="fa fa-area-chart"></i> Trajectories
--------------------------------------------------------------------------------

    figure()
    plot(t, x[0])
    plot(t, x[1])
    grid(True)


::: hidden :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

    tight_layout()
    save("images/rotation")

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::: slides :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

--------------------------------------------------------------------------------

![](images/rotation.svg)    

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

<i class="fa fa-area-chart"></i> Trajectories (State Space)
--------------------------------------------------------------------------------

Represent solutions fragments in the background

    figure()
    xs = ys = linspace(-1.5, 1.5, 50)
    streamplot(*Q(f, xs, ys), color="lightgrey")

<i class="fa fa-area-chart"></i> ...
--------------------------------------------------------------------------------

    plot(x[0], x[1], "k")
    plot(x0[0], x0[1], "ko")
    dx = x[0][-1] - x[0][-2]
    dy = x[1][-1] - x[1][-2]
    arrow(x[0][-1], x[1][-1], dx, dy, width=0.02, color="k")
    grid()
    axis("equal")

::: hidden :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

    tight_layout()
    save("images/rotation2")

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::: slides :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

--------------------------------------------------------------------------------

![](images/rotation2.svg)    

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::




Beyond the Basic Solver
--------------------------------------------------------------------------------

Many issues that our basic solver doesn't address:

  - time-dependent vector field,

  - error control,

  - dense outputs,

  - and more ...

--------------------------------------------------------------------------------

Instead, use:

    from scipy.integrate import solve_ivp

Documentation: [`solve_ivp`](https://docs.scipy.org/doc/scipy/reference/generated/scipy.integrate.solve_ivp.html) (Scipy)


<i class="fa fa-eye"></i> IVP / Rotation
--------------------------------------------------------------------------------

Compute the solution $x(t)$ for $t\in[0,2\pi]$ of the IVP:

  $$
  \left|
  \begin{split}
  \dot{x}_1 &= -x_2 \\
  \dot{x}_2 &= +x_1
  \end{split}
  \right.
  \; \mbox{ with } \;
  \left|
  \begin{array}{l}
  x_1(0) = 1\\
  x_2(0) = 0
  \end{array}
  \right.
  $$


<i class="fa fa-eye"></i> Solver Interface / Rotation
--------------------------------------------------------------------------------

    def fun(t, y):
        x1, x2 = y
        return array([-x2, x1])
    t_span = [0.0, 2*pi]
    y0 = [1.0, 0.0]
    result = solve_ivp(fun=fun, t_span=t_span, y0=y0)

Result Structure
--------------------------------------------------------------------------------

The `result` is a dictionary-like object (a "bunch").

Its fields:

  - `t` : array, time points, shape `(n_points,)`,

  - `y` : array, values of the solution at t, shape `(n, n_points)`,

  - ...

  (See [`solve_ivp` documentation](https://docs.scipy.org/doc/scipy/reference/generated/scipy.integrate.solve_ivp.html))


Non-Autonomous Systems
--------------------------------------------------------------------------------

<i class="fa fa-warning"></i> The solver may apply to
systems that are not time-invariant

  $$
  \dot{y} = f(t, y)
  $$ 

The `t` argument in the definition of `fun` is mandatory, 
even if the returned value doesn't depend on it (time-invariant system).

--------------------------------------------------------------------------------

    r_t = result["t"]
    x_1 = result["y"][0]
    x_2 = result["y"][1]

<i class="fa fa-area-chart"></i>
--------------------------------------------------------------------------------

    figure()
    t = linspace(0, 2*pi, 1000)
    plot(t, cos(t), "k--")
    plot(t, sin(t), "k--")
    bold = {"lw": 2.0, "ms": 10.0}
    plot(r_t, x_1, ".-", label="$x_1(t)$", **bold)
    plot(r_t, x_2, ".-", label="$x_2(t)$", **bold)
    xlabel("$t$"); grid(); legend()

--------------------------------------------------------------------------------

::: hidden :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

    tight_layout()
    save("images/solve_ivp_1")

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::: slides :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::


![](images/solve_ivp_1.svg)    

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::


Variable Step Size
--------------------------------------------------------------------------------

The step size is:

  - variable: $t_{n+1} - t_n$ may not be constant, 

  - automatically selected by the algorithm,

The solver shall meet the user specification,
but should select the largest step size to do so 
to minimize the number of computations.

Optionally, you can specify a `max_step` (default: $+\infty$).


Error Control
--------------------------------------------------------------------------------

We generally want to control the (local) error $e(t)$:   
the difference between the numerical solution and the exact one.

  - `atol` is the **absolute tolerance** (default: $10^{-6}$),

  - `rtol` is the **relative tolerance** (default: $10^{-3}$).

The solver ensures (approximately) that at each step:

  $$
  |e(t)| \leq \mathrm{atol} + \mathrm{rtol} \times |x(t)|
  $$

--------------------------------------------------------------------------------

    options = {
        # at least 20 data points
        "max_step": 2*pi / 20, 
        # standard absolute tolerance
        "atol"    : 1e-6,        
        # very large relative tolerance
        "rtol"    : 1e9 
    }

--------------------------------------------------------------------------------

    result = solve_ivp(
        fun=fun, t_span=t_span, y0=y0, 
        **options
    )
    r_t = result["t"]
    x_1 = result["y"][0]
    x_2 = result["y"][1]

<i class="fa fa-area-chart"></i>
--------------------------------------------------------------------------------

    figure()
    t = linspace(0, 2*pi, 20)
    plot(t, cos(t), "k--")
    plot(t, sin(t), "k--")
    bold = {"lw": 2.0, "ms": 10.0}
    plot(r_t, x_1, ".-", label="$x_1(t)$", **bold)
    plot(r_t, x_2, ".-", label="$x_2(t)$", **bold)
    xlabel("$t$"); grid(); legend()

--------------------------------------------------------------------------------

::: hidden :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

    tight_layout()
    save("images/solve_ivp_2")

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::: slides :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::


![](images/solve_ivp_2.svg)    

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

Dense Output
--------------------------------------------------------------------------------

Using a small `max_step` is usually the wrong way to "get more data points" 
since this will trigger many (potentially expensive) evaluations of `fun`.

Instead, use dense outputs: the solver may return  
the discrete data `result["t"]` and `result["y"]` 
**and** an approximate 
solution `result["sol"]` **as a function of `t`**
with little extra computations.

--------------------------------------------------------------------------------

    options = {
        "dense_output": True
    }

--------------------------------------------------------------------------------

    result = solve_ivp(
        fun=fun, t_span=t_span, y0=y0, 
        **options
    )
    r_t = result["t"]
    x_1 = result["y"][0]
    x_2 = result["y"][1]
    sol = result["sol"]

--------------------------------------------------------------------------------

    figure()
    t = linspace(0, 2*pi, 1000)
    plot(t, cos(t), "k--")
    plot(t, sin(t), "k--")
    bold = {"lw": 2.0, "ms": 10.0}
    plot(t, sol(t)[0], "-", label="$x_1(t)$", **bold)
    plot(t, sol(t)[1], "-", label="$x_2(t)$", **bold)
    plot(r_t, x_1, ".", color="C0", **bold)
    plot(r_t, x_2, ".", color="C1", **bold)
    xlabel("$t$"); grid(); legend()

--------------------------------------------------------------------------------

::: hidden :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

    tight_layout()
    save("images/solve_ivp_3")

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::: slides :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::


![](images/solve_ivp_3.svg)    

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::


<!--

--------------------------------------------------------------------------------

    def explicit_euler_step(fun, t, y, step):
        return y + step * fun(t, y)

Example
--------------------------------------------------------------------------------


----

    class ODEScheme:
        def __init__(self, fun, t0, y0, max_step, tf):
            self.fun = fun
            self.t = t0
            self.y = y0
            self.max_step = max_step
            self.tf = tf

----

    class ExplicitEuler(ODEScheme):
        def step(self):
            t_next = min(self.tf, \
                         self.t + self.max_step)
            step = t_next - self.t
            y_next = explicit_euler_step(self.fun, \
                                         self.t,   \
                                         self.y,   \
                                         step      )
            self.t = t_next
            self.y = y_next


Interpolation
--------------------------------------------------------------------------------

    from scipy.interpolate import interp1d
    def interpolate(ti, yi):
        def function(t):
            options = {"axis": 0, \
                       "fill_value": "extrapolate"}
            return interp1d(ti, yi, **options)(t).T
        return function

Example
--------------------------------------------------------------------------------

    ti, yi = array([0, 1, 2]), array([1, 2, 1])
    f = interpolate(ti, yi)
    t = linspace(-1, 3, 100)
    y = f(t)
    figure()
    plot(ti, yi, "ko")
    plot(t, y, "k--")

::: hidden :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

    save("images/interpolation")

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::: slides :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

Result
--------------------------------------------------------------------------------

![](images/interpolation.svg)    

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

-----

    def solve_ivp(fun, t_span, y0, \
                  max_step=1e-5, \
                  dense_output=False, \
                  **options):
        t0, tf = t_span
        scheme = ExplicitEuler(fun, t0, y0, max_step, tf)

        result = {}
        t, y = [t0], [y0]
        try:
            while t[-1] < tf:
                scheme.step()
                t.append(scheme.t)
                y.append(scheme.y)
            result["success"] = True
        except: # too wide. What can happen here ? nan stuff ?
            result["success"] = False
        result["t"] = t = array(t)
        result["y"] = y = array(y)
        if dense_output:
            result["sol"] = interpolate(t, y)
        else:
            result["sol"] = None
        return result  

-----

    def fun(t, y):
        return y

    t0, tf, y0 = 0.0, 5.0, array([1.0])
    result = solve_ivp(fun, (t0, tf), y0, \
                       max_step=0.25, \
                       dense_output=True)

------

    td, yd = result["t"], result["y"]
    t = linspace(t0, tf, 1000)
    y = result["sol"](t)
    figure()
    plot(t, exp(t), "k")
    plot(t, y.T, "k--")
    plot(td, yd, "k+")


::: hidden :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

    save("images/exp")

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::: slides :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

Results
--------------------------------------------------------------------------------

![](images/exp.svg)

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::


-->


Well-Posedness
================================================================================

Well-Posedness of an IVP
--------------------------------------------------------------------------------

**Well-Posedness** = Existence + Uniqueness + Continuity

A set of properties that ensures that common problems with IVPs cannot happen.

We are going to detail each property


Preamble: Local vs Global
--------------------------------------------------------------------------------

So far, we have only dealt with **global** solutions $x(t)$ of IVPs,
defined for any $t \geq t_0$.


This concept is sometimes too stringent.

--------------------------------------------------------------------------------

Consider for example:

$\dot{x} = x^2$ and $x(0)=1.$

-----

    def fun(t, y):
        return y * y
    t0, tf, y0 = 0.0, 3.0, array([1.0])
    result = solve_ivp(fun, t_span=[t0, tf], y0=y0)
    figure()
    plot(result["t"], result["y"][0], "k")
    xlim(t0, tf); xlabel("$t$"); ylabel("$x(t)$")


::: hidden :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

    tight_layout()
    save("images/finite-time-blowup")

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::: slides :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

--------------------------------------------------------------------------------

![](images/finite-time-blowup.svg)

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

--------------------------------------------------------------------------------

Ouch.

There is actually no **global** solution. 

However there is **local** solution $x(t)$, 
defined for $t \in \left[t_0, \tau\right[$
for some $\tau > t_0$.

--------------------------------------------------------------------------------

Indeed, the function

$$
x(t) = \frac{1}{1 - t}
$$

satisfies

$$
\dot{x}(t) = \frac{d}{dt} x(t) = -\frac{-1}{(1 - t)^2} 
=  (x(t))^2.
$$

But it's defined only for $t<1.$


<i class="fa fa-area-chart"></i>
--------------------------------------------------------------------------------

    tf = 1.0
    result = solve_ivp(fun, t_span=[t0, tf], y0=y0, max_step=0.01)
    figure()
    plot(result["t"], result["y"][0], "k")
    ylim(0.0, 10.0); grid(); xlabel("$t$"); ylabel("$x(t)$")

::: hidden :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

    tight_layout()
    save("images/finite-time-blowup-2")

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::: slides :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

--------------------------------------------------------------------------------

![](images/finite-time-blowup-2.svg)

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

--------------------------------------------------------------------------------

This local solution is also **maximal**:

You cannot extend this solution beyond $\tau=1.0$.

Maximal Solution
--------------------------------------------------------------------------------

A solution $x :[0, \tau[$ to an IVP is **maximal** if there is no other solution
 
  - defined on $[0, \tau'[$ with $\tau' > \tau$,
  
  - whose restriction to $[0, \tau[$ is $x$.

<i class="fa fa-question-circle-o"></i> -- Local/Maximal Solution
--------------------------------------------------------------------------------

  - [<i class="fa fa-superscript"></i>] Find a local solution $x(t)$
    of $\dot{x} = x^2$ such that $x(0) = x_0$ under the assumption
    that $x(t) \neq 0$ when $t\geq 0$.

    <i class="fa fa-key"></i> **Hint:** compute $d(1/x(t))/dt.$

  - \[<i class="fa fa-lightbulb-o"></i>\] Find for every $x_0 \neq 0$
    a maximal solution. When is it global?

Bad News (1/3)
--------------------------------------------------------------------------------

Sometimes things get worse than simply having no global solution.

<i class="fa fa-eye"></i> -- No Local Solution
--------------------------------------------------------------------------------

Consider the scalar IVP with initial value $x(0) = (0,0)$ and right-hand side

  $$
  f(x_1,x_2) = 
  \left|
  \begin{array}{rl}
  (+1,0) & \mbox{if } \; x_1< 0 \\
  (-1,0) & \mbox{if } \; x_1 \geq 0.
  \end{array}
  \right.
  $$

<i class="fa fa-area-chart"></i> -- No Local Solution
--------------------------------------------------------------------------------

    def f(x1x2):
        x1, x2 = x1x2
        dx1 = 1.0 if x1 < 0.0 else -1.0
        return array([dx1, 0.0])
    figure()
    x1 = x2 = linspace(-1.0, 1.0, 20)
    gca().set_aspect(1.0); grid(True)
    quiver(*Q(f, x1, x2), color="k") 


::: hidden :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

    tight_layout()
    save("images/discont")

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::: slides :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

--------------------------------------------------------------------------------

![](images/discont.svg)    

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::



<i class="fa fa-lightbulb-o"></i> -- No Local Solution
--------------------------------------------------------------------------------

This system has no solution, not even a local one, when $x(0) = (0,0)$.

Proof
--------------------------------------------------------------------------------

  - Assume that $x: [0, \tau[ \to \mathbb{R}$ is a local solution.

  - Since $\dot{x}(0) = -1 < 0$,
    for some small enough $0 < \epsilon < \tau$ and any 
    $t \in \left]0, \epsilon\right]$, we have $x(t) < 0$. 

  - Consequently, $\dot{x}(t) = +1$ and thus by integration

    $$
    x(\epsilon) =  x(0) + \int_0^{\epsilon} \dot{x}(t) \, dt = \epsilon > 0,
    $$
  
    which is a contradiction.  
    
Good News (1/3)
--------------------------------------------------------------------------------

However, a local solution exists under very mild assumptions.


Existence
--------------------------------------------------------------------------------

If $f$ is continuous, 

  - There is a (at least one) **local** solution to the IVP

    $\dot{x} = f(x)$ and $x(t_0) = x_0$.

  - Any local solution on some $\left[t_0, \tau \right[$ 
    can be extended to a (at least one) **maximal** one 
    on some $\left[t_0, t_{\infty}\right[$.

**Note:** a maximal solution is **global** iff $t_{\infty} = +\infty$.

Theorem -- Characterization of Maximal Solutions
--------------------------------------------------------------------------------

A solution on $\left[t_0, \tau \right[$ is maximal if and only if either

  - $\tau = +\infty$ : the solution is global, or

  - $\tau < +\infty$ and $\displaystyle \lim_{t \to \tau} \|x(t)\| = +\infty.$

In plain words : a non-global solution cannot be extended further in time 
if and only if it "blows up".

Corollary
--------------------------------------------------------------------------------

Let's assume that a local maximal solution exists.

You wonder if this solution is defined in $[t_0, t_f[$ or blows up before $t_f$.

For example, you wonder if a solution is global  
(if $t_f = +\infty$ or $t_f < +\infty$.)


<i class="fa fa-lightbulb-o"></i> Prove existence on $[0, t_f[$
--------------------------------------------------------------------------------

**TODO.** Show that any solution which defined on some
sub-interval $[t_0, \tau]$ with $\tau < t_f$ would is bounded.

Then, no solution can be maximal on any
such $[0, \tau[$ (since it doesn't blow up !). Since a maximal
solution does exist, its domain is $[0, t_{\infty}[$ with $t_{\infty} \geq t_f$.

**$\Rightarrow$ a solution is defined on $[t_0, t_f[$.**

<!--
If any **potential** local solution $x(t)$ defined on 
$\left[t_0, \tau\right[$ is **locally bounded** on this interval(*)
then such a solution exists.  

(*): bounded on any $[t_0, s] \subset \left[t_0, \tau \right[$.
-->

<i class="fa fa-question-circle-o"></i> -- Existence / Sigmoid
--------------------------------------------------------------------------------


Consider

  $$
  \dot{x} = \frac{1}{1 + e^{-x}} \mbox{ with } x(0) = x_0 \in \mathbb{R}
  $$


  - [</i><i class="fa fa-superscript"></i>] 
    Show that there is a (at least one) maximal solution.

  - [<i class="fa fa-superscript"></i>] 
    Show that any such solution is global.

<i class="fa fa-question-circle-o"></i> -- Existence / Pendulum
--------------------------------------------------------------------------------

Consider the pendulum, subject to a torque $c$

$$
ml^2 \ddot{\theta} + b \dot{\theta} + mg \ell \sin \theta = c
$$

We assume that the torque provides a bounded power:

$$
P(t) = c(t) \dot{\theta}(t) \leq M < +\infty.
$$

--------------------------------------------------------------------------------

  - [</i><i class="fa fa-superscript"></i>] Show that for any initial condition
    $t_0$, $\theta(t_0) = \theta_0$ and $\dot{\theta}(t_0) = \omega_0$, there is
    a solution to the IVP which is global.

    <i class="fa fa-key"></i> **Hint.** Compute the derivative with respect to $t$ of
    $$
    E = \frac{1}{2} m\ell^2 \dot{\theta}^2 - m g \ell \cos \theta
    $$

<i class="fa fa-question-circle-o"></i> -- Existence / Linear Systems
--------------------------------------------------------------------------------

Let $A \in \mathbb{R}^{n \times n}$ and $x_0 \in \mathbb{R}^n$. Consider
  $$
  \dot{x} = A x \; \mbox{ and } \; x(0) = x_0
  $$

<i class="fa fa-bullseye"></i> **Aim.** Show that any maximal solution is global.


--------------------------------------------------------------------------------

  - [</i><i class="fa fa-superscript"></i>] 
    Show that $y(t) = \|x(t)\|^2$ is differentiable and 
    satisfies $y(t) \geq 0$ and 
    $\dot{y}(t) \leq \alpha y(t)$ for some $\alpha \geq 0$.

  - [</i><i class="fa fa-superscript"></i>]
    Compute the derivative of $y(t) e^{-\alpha t}$ and conclude that
    $0 \leq y(t) \leq y(0) e^{\alpha t}$.

  - [</i><i class="fa fa-superscript"></i>]
    Prove that any maximal solution $x(t)$ of the initial IVP is global.

Bad News (2/3)
--------------------------------------------------------------------------------

Uniqueness of solutions, even the maximal ones, is not granted either.

(Note: why does uniqueness of local solution does not make sense?)


<i class="fa fa-eye"></i> -- Non-Uniqueness
--------------------------------------------------------------------------------

The IVP 

$\dot{x} = \sqrt{x}$, $x(0) = 0$ 

has several maximal (global) solutions. 

Proof
--------------------------------------------------------------------------------

For any $\tau \geq 0$, $x_{\tau}$ is a solution:

$$
x_{\tau}(t) = 
\left| 
\begin{array}{ll}
0 & \mbox{if} \; t \leq \tau, \\
1/4 \times (t-\tau)^2 & \mbox{if} \; t > \tau.
\end{array}
\right.
$$

Good News (2/3)
--------------------------------------------------------------------------------

However, uniqueness of maximal solution holds under mild assumptions.


Notation -- Jacobian Matrix
--------------------------------------------------------------------------------

Let $x=(x_1, \dots, x_n)$ and $f(x) = (f_1(x), \dots, f_n(x))$.   
The **Jacobian matrix** of $f$ is defined as

$$
\frac{\partial f}{\partial x}
:=
\left[
  \begin{array}{ccc}
  \frac{\partial f_1}{\partial x_1} & \cdots & \frac{\partial f_1}{\partial x_n} \\
  \vdots & \vdots & \vdots \\
  \frac{\partial f_n}{\partial x_1} & \cdots & \frac{\partial f_n}{\partial x_n} \\
  \end{array}
\right]
$$

(when all the partial derivatives are defined).

Uniqueness
--------------------------------------------------------------------------------

If $\partial f/\partial x$ exists and is continuous,  
the maximal solution is unique.

Bad News (3/3)
--------------------------------------------------------------------------------

An infinitely small error in the initial value could result in a finite
error in the solution, even in finite time.

That would undermine the utility of any approximation method.


Definition -- Continuity
--------------------------------------------------------------------------------

<!--
Assume the existence an uniqueness of (local maximal) solutions.
-->

Instead of denoting $x(t)$ the solution, use $x(t, x_0)$ to emphasize the
dependency w.r.t. the initial state.

**Continuity w.r.t. the initial state** means that
if $x(t, x_0)$ is defined on $[t_0, \tau]$ and $t\in [t_0, \tau]$:

$$
x(t, y) \to x(t, x_0) \; \mbox{when} \; y \to x_0
$$

and that this convergence is uniform w.r.t. $t$.

Good News (3/3)
--------------------------------------------------------------------------------

However, continuity wrt the initial value holds under mild assumptions.


Continuity
--------------------------------------------------------------------------------

Assume that $\partial f / \partial x$ exists and is continuous.  

Then the dynamical system is continous w.r.t. the initial state.


<i class="fa fa-eye"></i> Continuity / Prey-Predator
--------------------------------------------------------------------------------

    alpha = 2 / 3; beta = 4 / 3; delta = gamma = 1.0
    def f(t, y):
        x, y = y 
        u = alpha * x - beta * x * y
        v = delta * x * y - gamma * y
        return array([u, v])

--------------------------------------------------------------------------------

    tf = 3.0
    result = solve_ivp(f, t_span=[0.0, tf], y0=[1.5, 1.5], max_step=0.01)
    x = result["y"][0]
    y = result["y"][1]


<i class="fa fa-area-chart"></i>
--------------------------------------------------------------------------------

    figure(); ax = gca()
    xr = yr = linspace(0.0, 2.0, 1000)
    streamplot(*Q(lambda y: f(0,y), xr, yr), color="grey")
    for xy in zip(x, y):
        x_, y_ = xy
        ax.add_artist(Circle((x_, y_), 0.2, color="#d3d3d3"))
    ax.add_artist(Circle((x[0], y[0]), 0.1, color="#808080"))
    plot(x, y, "k")

<i class="fa fa-area-chart"></i>
--------------------------------------------------------------------------------

    result = solve_ivp(f, t_span=[0.0, tf], y0=[1.5, 1.575], max_step=0.01)
    x = result["y"][0]
    y = result["y"][1]
    plot(x, y, "k--")
    axis([0,2,0,2]); axis("square")

::: hidden :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

    save("images/continuity")

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::: slides :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

--------------------------------------------------------------------------------

![](images/continuity.svg)  

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::


--------------------------------------------------------------------------------

### <i class="fa fa-question-circle-o"></i> -- Continuity / Initial Value

Let $h \geq 0$ and $x^h(t)$ be the solution of $\dot{x} = x$ 
and $x^h(0) = 1+ h$.

  - [<i class="fa fa-superscript"></i>]. Let $\epsilon > 0$ and $\tau \geq 0$; 
    find the smallest  $\delta > 0$ such that 
    $|h| < \delta$ ensures that
    $$\mbox{for any $t \in [t_0, \tau]$}, |x^{h}(t) - x^0(t)| < \epsilon$$

--------------------------------------------------------------------------------

  - [<i class="fa fa-superscript"></i>]. What is the behavior of
    $\delta$ when $\tau$ goes to infinity?


<i class="fa fa-question-circle-o"></i> -- Continuity / Initial Value
--------------------------------------------------------------------------------

Consider $\dot{x} = \sqrt{|x|}$, $x(0)=x_0$.

  - [<i class="fa fa-laptop"></i>]
    Solve numerically this IVP for $t \in [0,1]$ and $x_0 = 0$. 
    Then, solve it again for $x_0 = 0.1$, $x_0=0.01$, etc.

  - [<i class="fa fa-flask"></i>]
    Does the solution seem to be continuous with respect to the initial value?

  - [<i class="fa fa-lightbulb-o"></i>] 
    Explain this experimental result.


<i class="fa fa-question-circle-o"></i> -- Well-Posedness / Prey-Predator
--------------------------------------------------------------------------------

Let

$$
\begin{array}{rcl}
\dot{x} &=& \alpha x - \beta xy \\
\dot{y} &=& \delta x y - \gamma y \\
\end{array}
$$

with $\alpha = 2 / 3$, $\beta = 4 / 3$, $\delta = \gamma = 1.0$.

--------------------------------------------------------------------------------

  - [<i class="fa fa-lightbulb-o"></i>] Show that the system is well-posed.

  - [<i class="fa fa-lightbulb-o"></i>, <i class="fa fa-superscript"></i>]
    Show that if $x(0) > 0$ and $y(0) > 0$, the maximal solution is global.
    
    <i class="fa fa-key"></i> **Hint.** Compute
    $$
    d/dt(\delta x - \gamma \ln x +\beta y - \alpha \ln y)
    $$

Asymptotic Behavior
================================================================================

--------------------------------------------------------------------------------

Asymptotic = Long-Term: when $t \to + \infty$

(note: from now on, all systems are well-posed.)

Lorenz System
--------------------------------------------------------------------------------

  $$
  \begin{array}{lll}
  \dot{x} &=& \sigma (y - x) \\
  \dot{y} &=& x (\rho - z) \\
  \dot{z} &=& xy - \beta z 
  \end{array}
  $$

--------------------------------------------------------------------------------

[![](images/static/lorenz-attractor.png)](https://portsmouth.github.io/fibre/?settings=eyJSIjp7InJheUJhdGNoIjoxMjgsIm1heFRpbWVTdGVwcyI6MjI5LCJtYXhJdGVyYXRpb25zIjoxMDAsImludGVncmF0aW9uVGltZSI6MiwiaW50ZWdyYXRlRm9yd2FyZCI6dHJ1ZSwiZ3JpZFNwYWNlIjowLjAzMzA4MjA1NjczMzUyMTgwNCwidHViZVdpZHRoIjowLjAxMzIzMjgyMjY5MzQwODcyMywidHViZVNwcmVhZCI6ZmFsc2UsInJlY29yZF9yZWFsdGltZSI6dHJ1ZSwieG1pbiI6MTguMTI3NTIxNDk3MTg5NjI2LCJ4bWF4IjoyNi43NDEyMjM2ODMyMjY3NzgsInltaW4iOi0xNS4xMDMyMzE2NzQyNjQ5NDUsInltYXgiOi04LjczODI1MzU0Njc5NDE0Mywiem1pbiI6MTkuMjU3MzA2NjExNDgwNjYsInptYXgiOjIxLjU4ODgyMDMyODM4NTQ2MywiY2xpcFRvQm91bmRzIjpmYWxzZSwic2hvd0JvdW5kcyI6dHJ1ZSwiZXhwb3N1cmUiOi0wLjc1MzM5MjIwMzMxNjgyMSwiZ2FtbWEiOjAuOTkyNDYxNzAyMDA1NjU2NSwiY29udHJhc3QiOjEuMTIyMzM0Njk3MzkzOTI2Mywic2F0dXJhdGlvbiI6MS4zODY5OTExNjk1MDI0NzcsInN1YnRyYWN0aXZlQ29sb3IiOmZhbHNlLCJiZ0NvbG9yIjpbMCwwLDBdLCJoYWlyU2hhZGVyIjpmYWxzZSwic3BlY1NoaW5lIjoyMS45NzI4NjIzNzM0NjU0MzIsInNwZWNDb2xvciI6WzAuOTAxOTUzMjc1MDg4MDg4MSwwLjg5MzEwOTkxODYyMzQ5MjEsMC44OTMxMDk5MTg2MjM0OTIxXSwibGlnaHQxX2NvbG9yIjpbMSwwLjksMC44XSwibGlnaHQyX2NvbG9yIjpbMC44LDAuOSwxXSwibGlnaHQxX2RpciI6WzAuNTc3MzUwMjY5MTg5NjI1OCwwLjU3NzM1MDI2OTE4OTYyNTgsMC41NzczNTAyNjkxODk2MjU4XSwibGlnaHQyX2RpciI6Wy0wLjU3NzM1MDI2OTE4OTYyNTgsLTAuNTc3MzUwMjY5MTg5NjI1OCwtMC41NzczNTAyNjkxODk2MjU4XSwiZGVwdGhUZXN0Ijp0cnVlLCJkYXNoX3NwYWNpbmciOjAuNTQwMzQwMjU5OTgwODU3NCwiZGFzaF9zcGVlZCI6MzMuMDgyMDU2NzMzNTIxODksImRhc2hfc2l6ZSI6MC45MTUyNzAyMzYyOTQxMDU1LCJkYXNoZXMiOmZhbHNlLCJzdWJ0cmFjdGl2ZV9jb2xvciI6ZmFsc2UsImhhaXJTaGluZSI6MTAsImhhaXJTcGVjQ29sb3IiOlsxLDEsMV19LCJDIjp7InBvcyI6Wy03LjQyNjkzNjMzNzgzOTcxLC02Ljk5ODI1Mjk3MjIzMzY1OSwxMTcuMTMyNDU3OTg4NDA0MDddLCJ0YXIiOlsxLjE4NTQzMDE1NTUyNDU3NCwtMS4zMzUzOTk3MDQyNDcyNDA1LDI3Ljk0Nzk3NzA0NDE1MDk1N10sIm5lYXIiOjAuMDM0NjQxMDE2MTUxMzc3NTUsImZhciI6MzQ2NDEuMDE2MTUxMzc3NTQ2fSwiRSI6eyJjb2RlIjoiXG4vLy8vLy8vLy8vLy8vLy8vLy8vLy8vLy8vLy8vLy8vLy8vLy8vLy8vLy8vXG4vLyBMb3JlbnogYXR0cmFjdG9yXG4vLy8vLy8vLy8vLy8vLy8vLy8vLy8vLy8vLy8vLy8vLy8vLy8vLy8vLy8vXG5cbmNvbnN0IGZsb2F0IGthcHBhID0gZmxvYXQoMS44NCk7XG5jb25zdCBmbG9hdCBhbHBoYSA9IGZsb2F0KDYuNyk7XG5cbiNkZWZpbmUgcmdiIHZlYzNcblxuY29uc3QgdmVjMyBjb2xMbyA9IHJnYigyNTQsNDUsNzMpIC8gMjU1LjA7XG5jb25zdCB2ZWMzIGNvbEhpID0gcmdiKDUsMTM4LDI1NSkgLyAyNTUuMDtcbmNvbnN0IGZsb2F0IG1hZ1NjYWxlID0gZmxvYXQoMC42KTtcblxuY29uc3QgZmxvYXQgcmhvICAgPSBmbG9hdCgyOC4wKTsgICAgIFxuY29uc3QgZmxvYXQgc2lnbWEgPSBmbG9hdCgxMC4wKTtcbmNvbnN0IGZsb2F0IGJldGEgID0gZmxvYXQoOC4wKS9mbG9hdCgzLjApO1xuXG5cbiB2ZWMzIHZlbG9jaXR5KHZlYzMgcCwgZmxvYXQgdClcbiB7XG4gICAgIHZlYzMgdjtcbiAgICAgZmxvYXQgeCA9IHAueDtcbiAgICAgZmxvYXQgeSA9IHAueTtcbiAgICAgZmxvYXQgeiA9IHAuejtcbiAgICAgdi54ID0gc2lnbWEqKHkgLSB4KTtcbiAgICAgdi55ID0geCoocmhvIC0geik7XG4gICAgIHYueiA9IHgqeSAtIGJldGEqejtcbiAgICAgcmV0dXJuIHY7XG4gfSAgICBcbiBcbiBcbnZlYzMgY29sb3IodmVjMyBwLCBmbG9hdCB0KVxue1xuICAgIHZlYzMgdiA9IHZlbG9jaXR5KHAsIHQpO1xuICBcdGZsb2F0IG1hZzIgPSB0L21hZ1NjYWxlO1xuICAgIGZsb2F0IGxlcnAgPSBtYWcyLygxLjArbWFnMik7XG4gICAgcmV0dXJuICgxLjAtbGVycCkqY29sTG8gKyBsZXJwKmNvbEhpO1xufSAgXG4ifX0%3D)

Visualized with [Fibre](https://github.com/portsmouth/fibre)


Hadley System
--------------------------------------------------------------------------------

  $$
  \begin{array}{lll}
  \dot{x} &=& -y^2 - z^2 - ax + af\\
  \dot{y} &=& xy - b xz - y + g \\
  \dot{z} &=& bxy + xz - z
  \end{array}
  $$

--------------------------------------------------------------------------------

[![](images/static/hadley-attractor.png)](https://portsmouth.github.io/fibre/?settings=eyJSIjp7InJheUJhdGNoIjoxMjgsIm1heFRpbWVTdGVwcyI6MzE5LCJtYXhJdGVyYXRpb25zIjoxMDAsImludGVncmF0aW9uVGltZSI6MjAsImludGVncmF0ZUZvcndhcmQiOnRydWUsImdyaWRTcGFjZSI6MC4wMDUsInR1YmVXaWR0aCI6MC4wMTg3NDc4NDI2MTk1ODA5MzIsInR1YmVTcHJlYWQiOmZhbHNlLCJyZWNvcmRfcmVhbHRpbWUiOnRydWUsInhtaW4iOi0xLjg0MjQzOTkxMzY3MjEzMjEsInhtYXgiOi0xLjY5NzQwMDg2MTM5ODUwMjUsInltaW4iOi0wLjc1MDc5Mzc1MTk1Njc2MDIsInltYXgiOi0wLjI5OTU4ODE4NTU3MDA5MzE1LCJ6bWluIjotMC44MTQyMDU1NzE1Mzg3ODkzLCJ6bWF4IjotMC43NTYwODExMDg1OTM5NzI5LCJjbGlwVG9Cb3VuZHMiOmZhbHNlLCJzaG93Qm91bmRzIjp0cnVlLCJleHBvc3VyZSI6LTEuMTc3NDg1ODI2MDc5NTYxLCJnYW1tYSI6MS4wMjU2MTcyNzI3MTgyNTEsImNvbnRyYXN0IjoxLjI5MDI5MjY5NzkzNTg2NDIsInNhdHVyYXRpb24iOjEuNDU1NzE0ODM4Njk2ODcyMywic3VidHJhY3RpdmVDb2xvciI6ZmFsc2UsImJnQ29sb3IiOlswLDAsMF0sImhhaXJTaGFkZXIiOmZhbHNlLCJzcGVjU2hpbmUiOjMxLjg5NzQ4MDA3NzUzNjA3Nywic3BlY0NvbG9yIjpbMSwxLDFdLCJsaWdodDFfY29sb3IiOlsxLDAuOSwwLjhdLCJsaWdodDJfY29sb3IiOlswLjgsMC45LDFdLCJsaWdodDFfZGlyIjpbMC41NzczNTAyNjkxODk2MjU4LDAuNTc3MzUwMjY5MTg5NjI1OCwwLjU3NzM1MDI2OTE4OTYyNThdLCJsaWdodDJfZGlyIjpbLTAuNTc3MzUwMjY5MTg5NjI1OCwtMC41NzczNTAyNjkxODk2MjU4LC0wLjU3NzM1MDI2OTE4OTYyNThdLCJkZXB0aFRlc3QiOnRydWUsImRhc2hfc3BhY2luZyI6MC41NDAzNDAyNTk5ODA4NTc0LCJkYXNoX3NwZWVkIjozMy4wODIwNTY3MzM1MjE4OSwiZGFzaF9zaXplIjowLjkxNTI3MDIzNjI5NDEwNTUsImRhc2hlcyI6ZmFsc2UsInN1YnRyYWN0aXZlX2NvbG9yIjpmYWxzZSwiaGFpclNoaW5lIjoxMCwiaGFpclNwZWNDb2xvciI6WzEsMSwxXX0sIkMiOnsicG9zIjpbLTQuMjgwNDkyMTM4MTY3OTY3NSwwLjg4NDU1MjA5Nzk1NTE3MSwtMy40OTI3MzM4Njc0MzM3ODFdLCJ0YXIiOlswLjQ0MzkwMDY5MjE1MTE1OTUsMC4xMDk3ODc2ODUzODYxNzI1MywtMC4xNDE1NTE2NDc4Mzk3MTEzXSwibmVhciI6MC4wMzQ2NDEwMTYxNTEzNzc1NSwiZmFyIjozNDY0MS4wMTYxNTEzNzc1NDZ9LCJFIjp7ImNvZGUiOiJcbi8vLy8vLy8vLy8vLy8vLy8vLy8vLy8vLy8vLy8vLy8vLy8vLy8vLy8vLy9cbi8vIEhhZGxleSBhdHRyYWN0b3Jcbi8vLy8vLy8vLy8vLy8vLy8vLy8vLy8vLy8vLy8vLy8vLy8vLy8vLy8vLy9cblxuY29uc3QgZmxvYXQgYSA9IGZsb2F0KDAuMik7XG5jb25zdCBmbG9hdCBiID0gZmxvYXQoNC4wKTtcbmNvbnN0IGZsb2F0IGYgPSBmbG9hdCg4LjApO1xuY29uc3QgZmxvYXQgZyA9IGZsb2F0KDEuMCk7XG5cbiNkZWZpbmUgcmdiIHZlYzNcblxuY29uc3QgdmVjMyBjb2xMbyA9IHJnYigyNTUsNjEsODcpIC8gMjU1LjA7XG5jb25zdCB2ZWMzIGNvbEhpID0gcmdiKDg2LDE3NiwyNTQpIC8gMjU1LjA7XG5jb25zdCBmbG9hdCBtYWdTY2FsZSA9IGZsb2F0KDYpO1xuXG52ZWMzIHZlbG9jaXR5KHZlYzMgcCwgZmxvYXQgdClcbntcbiAgICB2ZWMzIHY7XG4gICAgZmxvYXQgeCA9IHAueDtcbiAgICBmbG9hdCB5ID0gcC55O1xuICAgIGZsb2F0IHogPSBwLno7XG4gICAgdi54ID0gLXkqeSAteip6IC1hKnggKyBhKmY7XG4gICAgdi55ID0geCp5IC0gYip4KnogLSB5ICsgZztcbiAgICB2LnogPSBiKngqeSArIHgqeiAtIHo7XG4gICAgcmV0dXJuIHY7XG59ICAgIFxuIFxudmVjMyBjb2xvcih2ZWMzIHAsIGZsb2F0IHQpXG57XG4gICAgdmVjMyB2ID0gdmVsb2NpdHkocCwgdCk7XG4gICAgZmxvYXQgbWFnMiA9IHQvbWFnU2NhbGU7XG4gICAgZmxvYXQgbGVycCA9IG1hZzIvKDEuMCttYWcyKTtcbiAgICByZXR1cm4gKDEuMC1sZXJwKSpjb2xMbyArIGxlcnAqY29sSGk7XG59ICBcbiJ9fQ%3D%3D)

Visualized with [Fibre](https://github.com/portsmouth/fibre)



Equilibrium
--------------------------------------------------------------------------------

An **equilibrium** of system $\dot{x} = f(x)$ is a state $x_e$
such that the maximal solution of this system such that $x(0) = x_e$ 
is $x(t) = x_e$ for any $t > 0$.

The state $x_e$ is an equilibrium if and only if $f(x_e) = 0$.


--------------------------------------------------------------------------------

### <i class="fa fa-question-circle-o"></i> -- Equilibrium / Pendulum

**Reminder:** the pendulum is governed by the equation

  $$
  m \ell^2 \ddot{\theta} + b \dot{\theta} + m g \ell \sin \theta = 0
  $$

  - [<i class="fa fa-superscript"></i>] 
    Find the equilibriums of this dynamics.




Stability
--------------------------------------------------------------------------------

About the long-term behavior of solutions.

  - "Stability" subtle concept,

  - "Asymptotic Stability" simpler (and stronger),

  - "Attractivity" simpler yet, (but often too weak).


Attractivity
--------------------------------------------------------------------------------

Consider a well-posed ODE $\dot{x} = f(x)$.

An equilibrium $x_e$ is: 

  - **globally attractive** if for every $x_0$,
    the maximal solution $x(t)$ of the IVP with $x(0)=x_0$ 
    exists for any $t\geq 0$ and
      $$
      \lim_{t \to +\infty} x(t) = x_e.
      $$

  - **locally attractive** if this property holds when $x_0$ 
    is sufficiently close to the equilibrium $x_e$.

--------------------------------------------------------------------------------

    def f(xy):
        x, y = xy
        dx = -2*x + y
        dy = -2*y + x
        return array([dx, dy])

<i class="fa fa-area-chart"></i>
--------------------------------------------------------------------------------

    figure()
    x = y = linspace(-5.0, 5.0, 1000)
    streamplot(*Q(f, x, y), color="k") 
    plot([0], [0], "k.", ms=10.0)

Globally Attractive
--------------------------------------------------------------------------------

::: hidden :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

    axis("square")
    tight_layout()
    save("images/globally-attractive")

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::: slides :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

![](images/globally-attractive.svg)    

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

--------------------------------------------------------------------------------

    def f(xy):
        x, y = xy
        dx = -2*x + y**3
        dy = -2*y + x**3
        return array([dx, dy])


<i class="fa fa-area-chart"></i>
--------------------------------------------------------------------------------

    figure()
    x = y = linspace(-5.0, 5.0, 1000)
    streamplot(*Q(f, x, y), color="k") 
    plot([0], [0], "k.", ms=10.0)

Locally Attractive
--------------------------------------------------------------------------------

::: hidden :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

    axis("square")
    tight_layout()
    save("images/locally-attractive")

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::: slides :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

![](images/locally-attractive.svg)    

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

        

--------------------------------------------------------------------------------

### <i class="fa fa-question-circle-o"></i> -- Equilibrium / Stability

Consider a pendulum with a coefficient of friction $b$.

  - [<i class="fa fa-gears"></i>] 
    Is any equilibrium globally attractive?

  - [<i class="fa fa-flask"></i>, <i class="fa fa-area-chart"></i>] 
    
    - Assume that $b>0$.
      Determine graphically the equilibriums which are locally attractive.

    - What happens when $b=0$?

--------------------------------------------------------------------------------

  - [<i class="fa fa-gears"></i>, <i class="fa fa-superscript"></i>] 
    Prove that no equilibrium is locally attractive when $b=0$.

    <i class="fa fa-key"></i> **Hint:** study the evolution in time
      $$
      E = J \dot{\theta}^2 / 2 - m g\ell \cos \theta,
      $$
    with $J = m\ell^2$, the total mechanical energy of the pendulum.


Attractivity
--------------------------------------------------------------------------------

The equilibrium $x_e$ is globally attractive iff:

  - for any $x_0$ and for any $\epsilon > 0$, 
    there is a $\tau \geq 0$ such that the maximal solution $x(t)$ 
    such that $x(0) = x_0$ exists for all $t \geq 0$ and 
    satisfies:

    $$
    \|x(t) - x_e\| \leq \epsilon \; \mbox{ when } \; t \geq \tau.
    $$

<i class="fa fa-warning"></i>
--------------------------------------------------------------------------------

  - Very close values of $x(0)$ could lead to very different 
    "speed of convergence" towards the equilibrium.

  - This is not contradictory with the well-posedness assumption:
    continuity w.r.t. the initial condition only works with finite time spans.


Example
--------------------------------------------------------------------------------

  $$
  \left|
  \begin{array}{lll}
  \dot{x} &=& x + xy - (x + y)\sqrt{x^2 + y^2} \\
  \dot{y} &=& y - x^2 + (x - y) \sqrt{x^2 + y^2}
  \end{array}
  \right.
  $$

--------------------------------------------------------------------------------

Equivalently, in polar coordinates:

  $$
  \left|
  \begin{array}{lll}
  \dot{r} &=& r (1 - r) \\
  \dot{\theta} &=& r (1 - \cos \theta)
  \end{array}
  \right.
  $$

Stream Plot
--------------------------------------------------------------------------------

    def f(xy):
        x, y = xy
        r = sqrt(x*x + y*y)
        dx = x + x * y - (x + y) * r
        dy = y - x * x + (x - y) * r
        return [dx, dy]

--------------------------------------------------------------------------------

    figure()
    x = y = linspace(-2.0, 2.0, 1000)
    streamplot(*Q(f, x, y), color="k") 
    plot([1], [0], "k.", ms=10.0)

--------------------------------------------------------------------------------

::: hidden :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

    axis("square")
    tight_layout()
    save("images/attractive2")

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::: slides :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

![](images/attractive2.svg)    

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

--------------------------------------------------------------------------------

First, make sure that the right-hand side is time-dependent:

    def fun(t, y):
        return f(y)

--------------------------------------------------------------------------------

Then, pick a large time span and an initial state 
just above the equilibrium $(1.0, 0.0)$:

    t_span = (0.0, 600.0)
    theta_0 = 2 * pi / 1000
    y0 = [cos(theta_0), sin(theta_0)]

--------------------------------------------------------------------------------

    y = solve_ivp(fun, t_span, y0=y0, dense_output=True).sol
    t = linspace(t_span[0], t_span[-1], 1000)

<i class="fa fa-area-chart"></i>
--------------------------------------------------------------------------------

Plot the distance to the equilibrium as a function of time.

    figure()
    x1, x2 = y(t)[0], y(t)[1]
    plot(t, (sqrt((x1-1.0)**2 + x2**2)))
    grid()
    xlabel("$t$")

--------------------------------------------------------------------------------

::: hidden :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

    #axis("square")
    tight_layout()
    save("images/attractive-time")

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::: slides :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

![](images/attractive-time.svg)    

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::



Asymptotic Stability
--------------------------------------------------------------------------------

Asymptotic stability is a stronger version of attractivity 
which is by definition robust with respect to the choice of
the initial state.

Global Asymptotic Stability
--------------------------------------------------------------------------------

The equilibrium $x_e$ is **globally asymptotically stable** if:

  - for any $x_0$ and for any $\epsilon > 0$, 
    there is a $\tau \geq 0$ **and a $r > 0$
    such that if $\|x_1 - x_0\| \leq r$**, 
    the maximal solution $x(t)$ 
    **such that $x(0) = x_1$** exists for all $t \geq 0$ and 
    satisfies:

    $$
    \|x(t) - x_e\| \leq \epsilon \; \mbox{ when } \; t \geq \tau.
    $$

::: notes ::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

Global asymptotic stability is requirement which is very similar to attractivity.
The only difference is that it requires the time $\tau$ that we should wait
to be sure that the distance between $x(t)$ and the equilibrium is less that
$\varepsilon$ should be valid not only for the initial condition $x(0) = x_0$ 
but also for any other initial condition in an (arbitrary small) neighbourhood
of $x_0$. There is a common profile for the "speed of convergence" towards the equilibrium between an initial condition and its neighbors ; 
this condition is not always met if we merely have an attractive equilibrium.

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

--------------------------------------------------------------------------------

Equivalently: 

  - **for any $r>0$**, for any $\epsilon > 0$, 
    there is a $\tau \geq 0$ such the maximal solution $x(t)$ 
    **such that $\|x(0) - x_e\| \leq r$** exists for all $t \geq 0$ and 
    satisfies:

    $$
    \|x(t) - x_e\| \leq \epsilon \; \mbox{ when } \; t \geq \tau.
    $$

G.A.S. in Plain Words
--------------------------------------------------------------------------------

  - Pick any bounded set $B$ in $\mathbb{R}^n$.

  - Consider each point of the set as an initial value $x_0$, 
    compute $x(t, x_0)$.  Define $B_t$ as the set of all such $x(t, x_0)$:

    $$
    B_t = \{ x(t, x_0) \; | \; x_0 \in B\}
    $$

  - The set $B_t$ will converge to the equilibrium $x_e$:
    
    $$
    \sup \, \{d(x_e, x) \; | \; x \in B_t\} \to 0 \, \mbox{ when } \, t \to +\infty.
    $$


Local Asymptotic Stability
--------------------------------------------------------------------------------

A variant of the global asymptotic stability: the property may hold
only for small enough balls.

  - **for some $r>0$**, for any $\epsilon > 0$, 
    there is a $\tau \geq 0$ such that any maximal solution $x(t)$ 
    **such that $\|x(0) - x_e\| \leq r$** exists for all $t \geq 0$ and 
    satisfies:

    $$
    \|x(t) - x_e\| \leq \epsilon \; \mbox{ when } \; t \geq \tau.
    $$   


<i class="fa fa-question-circle-o"></i> -- Asymptotic Stability / Vinograd
--------------------------------------------------------------------------------        

Consider the ODE with right-hand side:

    def f(xy):
        x, y = xy
        q = x**2 + y**2 * (1 + (x**2 + y**2)**2) 
        dx = (x**2 * (y - x) + y**5) / q
        dy = y**2 * (y - 2*x) / q
        return array([dx, dy])


<i class="fa fa-area-chart"></i>
--------------------------------------------------------------------------------

    figure()
    x = y = linspace(-1.0, 1.0, 1000)
    streamplot(*Q(f, x, y), color="k") 
    xticks([-1, 0, 1])
    plot([0], [0], "k.", ms=10.0)

::: hidden :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

    axis("square")
    tight_layout()
    save("images/vinograd")

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

::: slides :::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

--------------------------------------------------------------------------------

![](images/vinograd.svg)    

::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

--------------------------------------------------------------------------------

  - [<i class="fa fa-flask"></i>] 
    Does the origin of the system look attractive ?

  - [<i class="fa fa-flask"></i>, <i class="fa fa-lightbulb-o"></i>] 
    Does it seem to be asymptotically stable ?

    <i class="fa fa-key"></i> **Hint.** 
    
      - Show if the origin is asymptotically stable, then it is 
        **stable**, that is: for any $r>0$, there is a $\rho \leq r$ 
        such that if $|x(0)| \leq \rho$, then $|x(t)| \leq r$ for
        any $t\geq 0$.

      - Is the system stable (graphically)?



<style>

.reveal section img {
  border:0;
  height:50vh;
  width:auto;

}

.reveal section img.medium {
  border:0;
  max-width:50vh;
}

.reveal section img.icon {
  display:inline;
  border:0;
  width:1em;
  margin:0em;
  box-shadow:none;
  vertical-align:-10%;
}

.reveal code {
  font-family: Inconsolata, monospace;
}

.reveal pre code {
  font-size: 1.5em;
  line-height: 1.5em;
  /* max-height: 80wh; won't work, overriden */
}

input {
  font-family: "Source Sans Pro", Helvetica, sans-serif;
  font-size: 42px;
  line-height: 54.6px;
}

</style>

<link href="https://fonts.googleapis.com/css?family=Inconsolata:400,700" rel="stylesheet"> 

<link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/4.7.0/css/font-awesome.css" rel="stylesheet">