# Plasma Profiles

## Overview

In `PROCESS` the density, temperature and current profiles of the plasma for electrons and ions can take two forms depending on the switch value for `ipedestal`. Either without a [pedestal](http://fusionwiki.ciemat.es/wiki/Pedestal), `ipedestal == 0` or with a pedestal `ipedestal == 1`.  `ipedestal == 0` is better suited for modelling L-mode plasmas, while `ipedestal == 1` is better suited for modelling [H-mode](https://en.wikipedia.org/wiki/High-confinement_mode) plasmas.

The files responsible for calculating and storing the profiles are `plasma_profiles.py` and `profiles.py`. A central plasma profile object is created from the [`PlasmaProfile`](plasma_profiles.md#plasma-profile-class-plasmaprofile) class that contains attributes for the plasma density and temperature. The density and temperature profiles are in themselves objects of the [`Profile`](./plasma_profiles_abstract_class.md) abstract base class. [`Profile`](./plasma_profiles_abstract_class.md), [`NProfile`](plasma_density_profile.md) and [`TProfile`](./plasma_temperature_profile.md) are all defined in `profiles.py`. [`PlasmaProfile`](plasma_profiles.md#plasma-profile-class-plasmaprofile) is exclusively in `plasma_profiles.py` 

<figure markdown>
![UML of profiles](./uml_classes_PlasmaProfile.png){height="1000px"}
<figcaption>Figure 1: UML class breakdown of the plasma profiles</figcaption>
</figure>


## Parabolic Profile | L-mode

If `ipedestal == 0` then no pedestal is present and the function describing the profiles is given by:

$$\begin{aligned}
\mbox{Density : } n(\rho) & = n_0 \left( 1 - \rho^2 \right)^{\alpha_n} \\
\mbox{Temperature : } T(\rho) & = T_0 \left( 1 - \rho^2 \right)^{\alpha_T} \\
\mbox{Current : } J(r) & = J_0 \left( 1 - \rho^2 \right)^{\alpha_J}
\end{aligned}$$

where $\rho = r/a$, and $a$ is the plasma minor radius. This gives
volume-averaged values $\langle n \rangle = n_0 / (1+\alpha_n)$, and
 approximate line-averaged values of $\bar{n} \approx n_0 / \sqrt{(1+\alpha_n)}$, the full equation used can be seen [here](./plasma_profiles.md#parabolic_paramterisation).  These
volume- and line-averages are used throughout the code along with the profile
indices $\alpha$, in the various physics models, many of which are fits to
theory-based or empirical scalings. Thus, the plasma model in PROCESS may
be described as 1/2-D.  The relevant profile index variables are
`alphan`, `alphat` and `alphaj`, respectively. For full derivation and description of the core and line averaged values please see the [parabolic_paramterisation()](plasma_profiles.md#parabolic_paramterisation) section which calculates these values for both temperature and density. A table of the the associated variables can be seen below

| Profile parameter                | Density   | Temperature | Current  |
|----------------------------------|-----------|-------------|----------------|
| Plasma centre value              | `ne0`, $n_0$         | `te0`, $T_0$        |  N/A, $J_0$        |
| Profile index/ peaking parameter | `alphan`, $\alpha_n$ | `alphat`, $\alpha_T$    |  `alphaj`, $\alpha_J$    |

???+ note "Plasma current profile"

    While PROCESS assumes a standard parabolic profile to be the shape of the current profile as per the 1989 ITER physics guidelines[^2] , it does not calculate its values. The profile peaking factor `alphaj` is calculated in the plasma current calculation relating to `iprofile` found [here](../plasma_current.md). Only the temeprature and density profiles are calculated fully withing the `PlasmaProfiles` class. 


The graph below is for a standard parabolic profile. You can vary the core value (`n0`) and the profile index (`alphan`) to see how the function behaves

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Bokeh Plot</title>
    <style>
      html, body {
        box-sizing: border-box;
        display: flow-root;
        height: 100%;
        margin: 0;
        padding: 0;
      }
    </style>
    <script type="text/javascript" src="https://cdn.bokeh.org/bokeh/release/bokeh-3.5.0.min.js"></script>
    <script type="text/javascript" src="https://cdn.bokeh.org/bokeh/release/bokeh-widgets-3.5.0.min.js"></script>
    <script type="text/javascript" src="https://cdn.bokeh.org/bokeh/release/bokeh-mathjax-3.5.0.min.js"></script>
    <script type="text/javascript">
        Bokeh.set_log_level("info");
    </script>
  </head>
  <body>
    <div id="c88c8d4a-f84c-4092-bcb7-50f7679b6099" data-root-id="p1054" style="display: contents;"></div>
  
<script type="application/json" id="c6de83b9-37b6-40bd-8911-646598c82205">
    {"796ea875-8ea6-4ec3-adf5-a00d41568ae5":{"version":"3.5.0","title":"Bokeh Application","roots":[{"type":"object","name":"Row","id":"p1054","attributes":{"children":[{"type":"object","name":"Figure","id":"p1004","attributes":{"width":400,"height":400,"x_range":{"type":"object","name":"Range1d","id":"p1014"},"y_range":{"type":"object","name":"Range1d","id":"p1015","attributes":{"end":10}},"x_scale":{"type":"object","name":"LinearScale","id":"p1016"},"y_scale":{"type":"object","name":"LinearScale","id":"p1017"},"title":{"type":"object","name":"Title","id":"p1007","attributes":{"text":"Parabolic Profile | L-mode"}},"renderers":[{"type":"object","name":"GlyphRenderer","id":"p1047","attributes":{"data_source":{"type":"object","name":"ColumnDataSource","id":"p1001","attributes":{"selected":{"type":"object","name":"Selection","id":"p1002","attributes":{"indices":[],"line_indices":[]}},"selection_policy":{"type":"object","name":"UnionRenderers","id":"p1003"},"data":{"type":"map","entries":[["x",{"type":"ndarray","array":{"type":"bytes","data":"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"},"shape":[500],"dtype":"float64","order":"little"}],["y",{"type":"ndarray","array":{"type":"bytes","data":"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"},"shape":[500],"dtype":"float64","order":"little"}]]}}},"view":{"type":"object","name":"CDSView","id":"p1048","attributes":{"filter":{"type":"object","name":"AllIndices","id":"p1049"}}},"glyph":{"type":"object","name":"Line","id":"p1044","attributes":{"x":{"type":"field","field":"x"},"y":{"type":"field","field":"y"},"line_color":"#1f77b4","line_alpha":0.6,"line_width":3}},"nonselection_glyph":{"type":"object","name":"Line","id":"p1045","attributes":{"x":{"type":"field","field":"x"},"y":{"type":"field","field":"y"},"line_color":"#1f77b4","line_alpha":0.1,"line_width":3}},"muted_glyph":{"type":"object","name":"Line","id":"p1046","attributes":{"x":{"type":"field","field":"x"},"y":{"type":"field","field":"y"},"line_color":"#1f77b4","line_alpha":0.2,"line_width":3}}}}],"toolbar":{"type":"object","name":"Toolbar","id":"p1013","attributes":{"tools":[{"type":"object","name":"PanTool","id":"p1028"},{"type":"object","name":"WheelZoomTool","id":"p1029","attributes":{"renderers":"auto"}},{"type":"object","name":"BoxZoomTool","id":"p1030","attributes":{"overlay":{"type":"object","name":"BoxAnnotation","id":"p1031","attributes":{"syncable":false,"line_color":"black","line_alpha":1.0,"line_width":2,"line_dash":[4,4],"fill_color":"lightgrey","fill_alpha":0.5,"level":"overlay","visible":false,"left":{"type":"number","value":"nan"},"right":{"type":"number","value":"nan"},"top":{"type":"number","value":"nan"},"bottom":{"type":"number","value":"nan"},"left_units":"canvas","right_units":"canvas","top_units":"canvas","bottom_units":"canvas","handles":{"type":"object","name":"BoxInteractionHandles","id":"p1037","attributes":{"all":{"type":"object","name":"AreaVisuals","id":"p1036","attributes":{"fill_color":"white","hover_fill_color":"lightgray"}}}}}}}},{"type":"object","name":"SaveTool","id":"p1038"},{"type":"object","name":"ResetTool","id":"p1039"},{"type":"object","name":"HelpTool","id":"p1040"}]}},"left":[{"type":"object","name":"LinearAxis","id":"p1023","attributes":{"ticker":{"type":"object","name":"BasicTicker","id":"p1024","attributes":{"mantissas":[1,2,5]}},"formatter":{"type":"object","name":"BasicTickFormatter","id":"p1025"},"axis_label":"Density, $$n_e$$","major_label_policy":{"type":"object","name":"AllLabels","id":"p1026"}}}],"below":[{"type":"object","name":"LinearAxis","id":"p1018","attributes":{"ticker":{"type":"object","name":"BasicTicker","id":"p1019","attributes":{"mantissas":[1,2,5]}},"formatter":{"type":"object","name":"BasicTickFormatter","id":"p1020"},"axis_label":"Normalized Radius, $$ \\rho $$","major_label_policy":{"type":"object","name":"AllLabels","id":"p1021"}}}],"center":[{"type":"object","name":"Grid","id":"p1022","attributes":{"axis":{"id":"p1018"}}},{"type":"object","name":"Grid","id":"p1027","attributes":{"dimension":1,"axis":{"id":"p1023"}}}]}},{"type":"object","name":"Column","id":"p1053","attributes":{"children":[{"type":"object","name":"Slider","id":"p1050","attributes":{"js_property_callbacks":{"type":"map","entries":[["change:value",[{"type":"object","name":"CustomJS","id":"p1052","attributes":{"args":{"type":"map","entries":[["source",{"id":"p1001"}],["n0",{"id":"p1050"}],["alpha",{"type":"object","name":"Slider","id":"p1051","attributes":{"js_property_callbacks":{"type":"map","entries":[["change:value",[{"id":"p1052"}]]]},"title":"Profile Index  | alphan","start":0.01,"end":10,"value":2,"step":0.01}}]]},"code":"\n    const A = n0.value\n    const B = alpha.value\n\n    const x = source.data.x\n    const y = Array.from(x, (x) =&gt; A*(1-x**2)**B)\n    source.data = { x, y }\n"}}]]]},"title":"Plasma centre value | n0","start":0.1,"end":10,"value":5,"step":0.1}},{"id":"p1051"}]}}]}}]}}
</script>
<script type="text/javascript">
    (function() {
    const fn = function() {
        Bokeh.safely(function() {
        (function(root) {
            function embed_document(root) {
            const docs_json = document.getElementById('c6de83b9-37b6-40bd-8911-646598c82205').textContent;
            const render_items = [{"docid":"796ea875-8ea6-4ec3-adf5-a00d41568ae5","roots":{"p1054":"c88c8d4a-f84c-4092-bcb7-50f7679b6099"},"root_ids":["p1054"]}];
            root.Bokeh.embed.embed_items(docs_json, render_items);
            }
            if (root.Bokeh !== undefined) {
            embed_document(root);
            } else {
            let attempts = 0;
            const timer = setInterval(function(root) {
                if (root.Bokeh !== undefined) {
                clearInterval(timer);
                embed_document(root);
                } else {
                attempts++;
                if (attempts > 100) {
                    clearInterval(timer);
                    console.log("Bokeh: ERROR: Unable to run BokehJS code because BokehJS library is missing");
                }
                }
            }, 10, root)
            }
        })(window);
        });
    };
    if (document.readyState != "loading") fn();
    else document.addEventListener("DOMContentLoaded", fn);
    })();
</script>
  </body>
</html>

--------

## Pedestal Profile | H-mode

If `ipedestal == 1` there is now a pedestal present in the profile and they follow the form shown below:


$$\begin{aligned}
\mbox{Density:} \qquad n(\rho) = \left\{ 
\begin{aligned}
    & \text{n}_{\text{ped}} + (n_0 - \text{n}_{\text{ped}}) \left( 1 -
    \frac{\rho^2}{\rho_{\text{ped},n}^2}\right)^{\alpha_n}
   &  0 \leq \rho \leq \rho_{\text{ped},n} \\
   & \text{n}_{\text{sep}} + (\text{n}_{\text{ped}} - \text{n}_{\text{sep}})\left( \frac{1- \rho}{1-\rho_{\text{ped},n}}\right)
   &  \rho_{\text{ped},n} < \rho \leq 1
\end{aligned}
\right.
\end{aligned}$$


$$\begin{aligned}
\mbox{Temperature:} \ T(\rho) = \left\{ 
\begin{aligned}
   & \text{T}_{\text{ped}} + (T_0 - \text{T}_{\text{ped}}) \left( 1 - \frac{\rho^{\beta_T}}
    {\rho_{\text{ped},T}^{\beta_T}}\right)^{\alpha_T}  &  0 \leq \rho \leq \rho_{\text{ped},T} \\
   & \text{T}_{\text{sep}} + (\text{T}_{\text{ped}} - \text{T}_{\text{sep}})\left( \frac{1- \rho}{1-\rho_{\text{ped},T}}\right)
   &  \rho_{\text{ped},T} < \rho \leq 1
\end{aligned}
\right.
\end{aligned}$$ 

Subscripts $0$, $\text{ped}$ and $\text{sep}$, denote values at the centre ($\rho = 0$), the
pedestal ($\rho = \rho_{\text{ped}}$) and the separatrix ($\rho=1$),
respectively. The density and temperature peaking parameters $\alpha_n$ and a
$\alpha_T$ as well as the second exponent $\beta_T$ (input parameter
`tbeta`, not to be confused with the plasma beta) in the temperature
profile can be chosen by the user, as can the pedestal heights and the values
at the separatrix (`neped, nesep` for the electron density, and
`teped, tesep` for the electron temperature); the ion equivalents are
scaled from the electron values by the ratio of the volume-averaged values).

!!! warning " Pedestal setting"
    If `ipedestal == 1` then the pedestal density `neped` is set as a fraction `fgwped` of the 
    Greenwald density (providing `fgwped` >= 0).  The default value of `fgwped` is 0.8[^2]. 

A table of the the associated variables can be seen below


| Profile parameter                | Density   |        Temperature |                
|----------------------------------|-----------|-------------|
| Pedestal radius (r/a)            | `rhopedn`, $\rho_{\text{ped},n}$ |   `rhopedt`, $\rho_{\text{ped},T}$   |  
| Plasma centre value              | `ne0`, $n_0$      |           `te0`, $T_0$       |           
| Pedestal value                   | `neped`, $n_{\text{ped}}$    |       `teped`, $T_{\text{ped}}$     |       
| Separatrix value                 | `nesep`, $n_{\text{sep}}$   |        `tesep`, $T_{\text{sep}}$     |       
| Profile index/ peaking parameter | `alphan`, $\alpha_n$  |       `alphat`, $\alpha_T$    |      
| Profile index $\beta$            |           |                 `tbeta`, $\beta_T$     |

The graph below is for a standard pedestal profile. You can vary its attributes given in the table above to see how the function behaves.

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Bokeh Plot</title>
    <style>
      html, body {
        box-sizing: border-box;
        display: flow-root;
        height: 100%;
        margin: 0;
        padding: 0;
      }
    </style>
    <script type="text/javascript" src="https://cdn.bokeh.org/bokeh/release/bokeh-3.5.0.min.js"></script>
    <script type="text/javascript" src="https://cdn.bokeh.org/bokeh/release/bokeh-widgets-3.5.0.min.js"></script>
    <script type="text/javascript" src="https://cdn.bokeh.org/bokeh/release/bokeh-mathjax-3.5.0.min.js"></script>
    <script type="text/javascript">
        Bokeh.set_log_level("info");
    </script>
  </head>
  <body>
    <div id="e8b0b6a8-04d8-4375-b82f-70e118610adf" data-root-id="p1067" style="display: contents;"></div>
  
    <script type="application/json" id="d331a871-1598-4902-a748-20176e3865c2">
</script>
<script type="text/javascript">
    (function() {
    const fn = function() {
        Bokeh.safely(function() {
        (function(root) {
            function embed_document(root) {
            const docs_json = document.getElementById('d331a871-1598-4902-a748-20176e3865c2').textContent;
            const render_items = [{"docid":"e9ba0c74-a895-4c3a-93b3-becb3c2931f0","roots":{"p1067":"e8b0b6a8-04d8-4375-b82f-70e118610adf"},"root_ids":["p1067"]}];
            root.Bokeh.embed.embed_items(docs_json, render_items);
            }
            if (root.Bokeh !== undefined) {
            embed_document(root);
            } else {
            let attempts = 0;
            const timer = setInterval(function(root) {
                if (root.Bokeh !== undefined) {
                clearInterval(timer);
                embed_document(root);
                } else {
                attempts++;
                if (attempts > 100) {
                    clearInterval(timer);
                    console.log("Bokeh: ERROR: Unable to run BokehJS code because BokehJS library is missing");
                }
                }
            }, 10, root)
            }
        })(window);
        });
    };
    if (document.readyState != "loading") fn();
    else document.addEventListener("DOMContentLoaded", fn);
    })();
</script>
  </body>
</html>

!!! warning " Un-realistic profiles"

    If setting `ipedestal == 1` it is highly recommended to make sure constraint equation 81 (icc=81) is active. This enforces solutions in which $n_0$ has to be greater than $n_{\text{ped}}$. 
    Negative $n_0$ values can also arise during iteration, so it is important to be weary on how low the lower bound for $n_{\text{e}} (\mathtt{dene})$ is set. More info can be found [here](plasma_profiles.md#pedestal-density-upper-limit)

--------

## Plasma Profile Class | `PlasmaProfile` 
### Initialization | `__init__()`
The parent plasma profile class is `PlasmaProfile`. Initialization sets the profile class size and `neprofile` and `teprofile` to [`NProfile`](plasma_density_profile.md) & [`TProfile`](plasma_temperature_profile.md) objects from `profiles.py`

???+ Note

    Profile sizes are set to 501 point by default. this can be varied in the `__init__` of `PlasmaProfile`. Changing this will affect the values when doing Simpsons rule integration on the profiles. 

----------

### Runner function | `run()`
This function is used to run the appropriate function relating to the calculation of the PlasmaProfile class. At the moment it only runs the [`pramterise_plasma()`](plasma_profiles.md#plasma-parameterization-parameterise_plasma) function

------------

### Plasma Parameterization | `parameterise_plasma()`
The input variable `tratio` can be used to define the ratio between the volume averaged ion and electron temperatures. By default it is set so that the volume averaged ion and electron temperatures are equal. The value of $T_{\text{i}}$ is set such that:

$$
T_{\text{i}} = \mathtt{tratio}\times T_{\text{e}}
$$

Depending on the value of `ipedestal` different functions will be ran, the different run cases are given below:

#### Parabolic Profile | `ipedestal == 0`

##### `parabolic_paramterisation()`

If pedestal profile values are set they are reset to have values that agree with the original form of the parabolic profiles. Such that $\rho_{\text{ped}} = 1$ and that pedestal and separatrix densities and temepratures are zero. This will then warn the user in the terminal.

The density and temperature profile runner function [`TProfile/NProfile.run()`](plasma_density_profile.md#runner-function-run) is then called to re-calculate the profile and core values. 


Ratio of density-weighted to volume-averaged temperature factor is calculated:

$$
\mathtt{pcoef} = \frac{(1+\alpha_n)(1+\alpha_T)}{(1+\alpha_T + \alpha_n)}
$$

--------

The line averaged density is then calculated for the profile paramaters

###### Line averaged density derivation
Line averaged electron density is calculated by integrating the profile across the normalised width of the profile and then dividing by the width of the integration bounds

$$
\mathtt{dnla}\  | \ \bar{n_{\text{e}}} = \frac{\int^1_0 n_0(1-\rho^2)^{\alpha_n} \ d\rho}{\rho}
$$

This can be more easily represented in radial coordinates and returning $\rho = \frac{r}{a}$

$$
\mathtt{dnla} \  | \ \bar{n_{\text{e}}} = \frac{\int^a_0 n_0(1-r^2/a^2)^{\alpha_n} \ dr}{a}
$$

$$
\mathtt{dnla} \  | \ \bar{n_{\text{e}}} = \frac{\left[\frac{an_0}{2}\frac{\Gamma(1/2)\Gamma(\alpha_n+1)}{\Gamma(\alpha_n+3/2)}\right]}{a}
$$

$\Gamma$ is the [gamma function](https://en.wikipedia.org/wiki/Gamma_function) and is calculated in the code with [scipy.special.gamma()](https://docs.scipy.org/doc/scipy/reference/generated/scipy.special.gamma.html)


$$
\mathtt{dnla} / \bar{n_{\text{e}}} = \frac{n_0}{2}\frac{\Gamma(1/2)\Gamma(\alpha_n+1)}{\Gamma(\alpha_n+3/2)} 
$$

This is in agreement with the derivation from the ITER Physics Design 1989 [^2]

$\blacksquare$

------

The density weighted temperatures are set:


$$\begin{aligned}
\mathtt{ten} = \mathtt{pcoef} \times T_\text{e} \\
\mathtt{tin} = \mathtt{pcoef}\times T_\text{i}
\end{aligned}$$

------

The central values for temperature and density is then calculated for the profile paramaters

###### Core temperature & density derivation

We calculate the volume integrated profile and then divide by the volume of integration to get the volume average density $\langle n \rangle$. If we assume the plasma to be a torus of circular cross-section then we can use spherical coordinates. We can simplify the problem by representing the torus as a cylinder of height equal to the circumference of the torus equal to $2\pi R$ where $R$ is the major radius of the torus.

The cylindrical volume element is given by:

$$
V = \int \int \int dV = \int^{2\pi R}_0 \int^{2\pi}_0 \int^a_0 r \ dr \ d\theta \ dz
$$

Inserting our density function in the form where $\rho$ is expanded as $\rho = r/a$ we get:

$$
\int^{2\pi R}_0 \int^{2\pi}_0 \int^a_0     r  \left(n_0(1-r^2/a^2)^{\alpha_n}\right) \ dr \ d\theta \ dz
$$

Since our density function is only a function of $r$, and the torus is symmetric around its center, the integration simplifies to integrating over $r$ and the $d\theta ,\ dz$ integrals are solved to give values for the full poloidal angle and cylindrical height and torus length, leading to:

$$
4\pi^2R \int^a_0     r  \left(n_0(1-r^2/a^2)^{\alpha_n}\right) \ dr  
$$

In the form of volume average density where the volume integrated density function has to be divided by the volume of the cylinder / torus we get:

$$
\langle n \rangle =  \frac{4\pi^2R \int^a_0     r  \left(n_0(1-r^2/a^2)^{\alpha_n}\right) \ dr}{2\pi^2Ra^2}  
$$

Simplify:

$$
\langle n \rangle =  \frac{2 \int^a_0     r  \left(n_0(1-r^2/a^2)^{\alpha_n}\right) \ dr}{a^2}  
$$

Integrate:

$$
\langle n \rangle =  \frac{2}{a^2} \frac{a^2n_0}{2(\alpha_n+1)}
$$

$$
\therefore \langle n \rangle =   \frac{n_0}{\alpha_n+1}
$$

This is in agreement with the derivation from the ITER Physics Design 1989. [^2]

Since all parabolic profiles are of the same form this proof holds for the density and temperature.

A similar derivation is found in normalised coordinates, $\rho$ if the line averaged integral is swept around the poloidal circumference then divided by the poloidal area.

$$
\langle n \rangle =  \frac{2\pi \int^1_0     \rho  \left(n_0(1-\rho^2)^{\alpha_n}\right) \ d\rho}{\pi\rho^2}  
$$


$\blacksquare$

------

The core values for the temperature and density are then calculated for the electrons and ions

$$\begin{aligned}
\mathtt{te0} = T_\text{e} \times (\alpha_T+1) \\
\mathtt{ti0} = T_\text{i} \times (\alpha_T+1)
\end{aligned}$$

$$\begin{aligned}
\mathtt{ne0} = n_{\text{e}} \times (\alpha_n+1) \\
\mathtt{ni0} = \mathtt{dnitot} \times (\alpha_n+1)
\end{aligned}$$

-----


##### `calculate_profile_factors()`

The central plasma pressure is calculated from the ideal gas law.

$$
p_0 = (\mathtt{ne0} \times \mathtt{te0}+\mathtt{ni0}\times \mathtt{ti0})\times (1000 \times \text{e})
$$

With the coefficients used to turn the temperature from $\text{keV}$ back to Joules.

Since we multiply the density and temperature profiles together to get the pressure we can find the pressure profile factor by adding the profile factors for temperature and density.
$$
\alpha_p = \alpha_n + \alpha_T
$$

???+ note "Pressure profile factor"

    The calculation of $\alpha_p$ is only valid assuming a parabolic profile case. The calculatio of $p_0$ is still true as the core values are calculated independantly for each profile type. 

    $p_0$ is NOT equal to $\langle p \rangle \times (1 + \alpha_p)$, but $p(\rho) = n(\rho)T(\rho)$ and $\langle p \rangle = \langle n \rangle$ $T_n$, where $T_n$ is the
    density-weighted temperature.

------

##### `calculate_parabolic_profile_factors()`

This is used by the stellerator module to find temperature gradients.
We first need to find the normalised position in the plasma where the change in the pressure gradient is at a minima or maxima. This is just when the second derivative of the profile is zero. Using the density profile as an example, though this is true for temperature also.

$$
T(\rho) = n_{0} \left(1 - {\rho}^{2}\right)^{\alpha_T}
$$

$$
\frac{{\mathrm{d}T}}{\mathrm{d}\rho} = -2T_{0} {\alpha_T}{\rho} \left(1 - {\rho}^{2}\right)^{{\alpha_T} - 1}
$$

$$
\frac{\mathrm{d}^2T}{\mathrm{d}\rho^2} = \frac{2T_{0} {\alpha_T} \left(1 - {\rho}^{2}\right)^{\alpha_T} \left(\left(2{\alpha_T} - 1\right) {\rho}^{2} - 1\right)}{\left({\rho}^{2} - 1\right)^{2}}
$$

The positive root for the 2nd derivative above:

$$
\mathtt{rho\_te\_max} = \frac{1}{\sqrt{2{\alpha_T} - 1}}
$$

Substituting the root above into the first derivative as $\rho$ to get the minima or maxima gradient value:

$$
-2T_0\alpha_T\left(\frac{1}{\sqrt{2{\alpha_T} - 1}}\right)\left(1-\left(\frac{1}{\sqrt{2{\alpha_T} - 1}}\right)^2\right)^{\alpha_T-1}
$$

$$
\frac{2T_{0} {\alpha_T} \left(1 - \frac{1}{2{\alpha_T} - 1}\right)^{{\alpha_T} - 1}}{\sqrt{2{\alpha_T} - 1}}
$$

Simplify fully:

$$
\mathtt{dtdrho\_max}= -T_0\alpha_T(2^{\alpha_T})(-1+\alpha_T)^{(-1+\alpha_T)} (-1+2\alpha_T)^{(-0.5-\alpha_T)}
$$

Find the temperature on the profile where the gradient change is largest by substituting into the original profile function:

$$
\mathtt{te\_max} = T_0\left(1-\mathtt{rho\_te\_max}^2\right)^{\alpha_T}
$$

This solution for the profile gradient only holds true if $\alpha_T \ge 1$.

In the region $0 \le \alpha_T \le 1$ when substituting the roots of the second derivative into the first derivative the function diverges into the complex number solution space.

To overcome this we can assume the second derivative root to be a value. In this case we assume a default value of $\mathtt{rho\_te\_max}$ = 0.9.
Then we just substitute this value into the first derivative to 

$$
-2T_0\alpha_T(0.9)(1-(0.9)^2)^{-1+\alpha_T}
$$

The value of $\mathtt{rho\_te\_max}$ can be manually changed in the code depending on the derivative profile required.
You can use the slider in the graph below to experiment with the value of $\mathtt{rho\_te\_max}$ and how this changes the profile solutions.

<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>Bokeh Plot</title>
    <style>
      html, body {
        box-sizing: border-box;
        display: flow-root;
        height: 100%;
        margin: 0;
        padding: 0;
      }
    </style>
    <script type="text/javascript" src="https://cdn.bokeh.org/bokeh/release/bokeh-3.5.0.min.js"></script>
    <script type="text/javascript" src="https://cdn.bokeh.org/bokeh/release/bokeh-widgets-3.5.0.min.js"></script>
    <script type="text/javascript" src="https://cdn.bokeh.org/bokeh/release/bokeh-mathjax-3.5.0.min.js"></script>
    <script type="text/javascript">
        Bokeh.set_log_level("info");
    </script>
  </head>
  <body>
    <div id="c2d8216c-5737-4b83-8212-83eb87b9cecf" data-root-id="p1075" style="display: contents;"></div>
  
    <script type="application/json" id="e39cfdff-a985-4502-8ac6-7fabfc15f74c">
      {"251ccf3b-11cd-4d8c-9ce1-ee8ba19b059a":{"version":"3.5.0","title":"Bokeh Application","roots":[{"type":"object","name":"Column","id":"p1075","attributes":{"children":[{"type":"object","name":"Figure","id":"p1004","attributes":{"height":400,"x_range":{"type":"object","name":"Range1d","id":"p1014","attributes":{"end":2}},"y_range":{"type":"object","name":"Range1d","id":"p1015","attributes":{"start":-5,"end":0}},"x_scale":{"type":"object","name":"LinearScale","id":"p1016"},"y_scale":{"type":"object","name":"LinearScale","id":"p1017"},"title":{"type":"object","name":"Title","id":"p1007","attributes":{"text":"Comparison of the profile 1st derivative solution for a fixed 2nd derivative root for \u03b1 &lt; 1 "}},"renderers":[{"type":"object","name":"GlyphRenderer","id":"p1047","attributes":{"data_source":{"type":"object","name":"ColumnDataSource","id":"p1001","attributes":{"selected":{"type":"object","name":"Selection","id":"p1002","attributes":{"indices":[],"line_indices":[]}},"selection_policy":{"type":"object","name":"UnionRenderers","id":"p1003"},"data":{"type":"map","entries":[["x",{"type":"ndarray","array":{"type":"bytes","data":"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"},"shape":[500],"dtype":"float64","order":"little"}],["y",{"type":"ndarray","array":{"type":"bytes","data":"AAAAAAAA+H8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/AAAAAAAA+P8AAAAAAAD4/wAAAAAAAPj/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"},"shape":[500],"dtype":"float64","order":"little"}],["y2",{"type":"ndarray","array":{"type":"bytes","data":"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"},"shape":[500],"dtype":"float64","order":"little"}]]}}},"view":{"type":"object","name":"CDSView","id":"p1048","attributes":{"filter":{"type":"object","name":"AllIndices","id":"p1049"}}},"glyph":{"type":"object","name":"Line","id":"p1044","attributes":{"x":{"type":"field","field":"x"},"y":{"type":"field","field":"y"},"line_color":"blue","line_alpha":0.6,"line_width":3}},"nonselection_glyph":{"type":"object","name":"Line","id":"p1045","attributes":{"x":{"type":"field","field":"x"},"y":{"type":"field","field":"y"},"line_color":"blue","line_alpha":0.1,"line_width":3}},"muted_glyph":{"type":"object","name":"Line","id":"p1046","attributes":{"x":{"type":"field","field":"x"},"y":{"type":"field","field":"y"},"line_color":"blue","line_alpha":0.2,"line_width":3}}}},{"type":"object","name":"GlyphRenderer","id":"p1058","attributes":{"data_source":{"id":"p1001"},"view":{"type":"object","name":"CDSView","id":"p1059","attributes":{"filter":{"type":"object","name":"AllIndices","id":"p1060"}}},"glyph":{"type":"object","name":"Line","id":"p1055","attributes":{"x":{"type":"field","field":"x"},"y":{"type":"field","field":"y2"},"line_color":"red","line_alpha":0.6,"line_width":3}},"nonselection_glyph":{"type":"object","name":"Line","id":"p1056","attributes":{"x":{"type":"field","field":"x"},"y":{"type":"field","field":"y2"},"line_color":"red","line_alpha":0.1,"line_width":3}},"muted_glyph":{"type":"object","name":"Line","id":"p1057","attributes":{"x":{"type":"field","field":"x"},"y":{"type":"field","field":"y2"},"line_color":"red","line_alpha":0.2,"line_width":3}}}},{"type":"object","name":"GlyphRenderer","id":"p1068","attributes":{"data_source":{"type":"object","name":"ColumnDataSource","id":"p1062","attributes":{"selected":{"type":"object","name":"Selection","id":"p1063","attributes":{"indices":[],"line_indices":[]}},"selection_policy":{"type":"object","name":"UnionRenderers","id":"p1064"},"data":{"type":"map","entries":[["x",[0,1,1,0]],["y",[-5,-5,0,0]]]}}},"view":{"type":"object","name":"CDSView","id":"p1069","attributes":{"filter":{"type":"object","name":"AllIndices","id":"p1070"}}},"glyph":{"type":"object","name":"Patch","id":"p1065","attributes":{"x":{"type":"field","field":"x"},"y":{"type":"field","field":"y"},"line_color":"gray","line_alpha":0.3,"fill_alpha":0.3,"hatch_color":"gray","hatch_alpha":0.3}},"nonselection_glyph":{"type":"object","name":"Patch","id":"p1066","attributes":{"x":{"type":"field","field":"x"},"y":{"type":"field","field":"y"},"line_color":"gray","line_alpha":0.1,"fill_alpha":0.1,"hatch_color":"gray","hatch_alpha":0.1}},"muted_glyph":{"type":"object","name":"Patch","id":"p1067","attributes":{"x":{"type":"field","field":"x"},"y":{"type":"field","field":"y"},"line_color":"gray","line_alpha":0.2,"fill_alpha":0.2,"hatch_color":"gray","hatch_alpha":0.2}}}}],"toolbar":{"type":"object","name":"Toolbar","id":"p1013","attributes":{"tools":[{"type":"object","name":"PanTool","id":"p1028"},{"type":"object","name":"WheelZoomTool","id":"p1029","attributes":{"renderers":"auto"}},{"type":"object","name":"BoxZoomTool","id":"p1030","attributes":{"overlay":{"type":"object","name":"BoxAnnotation","id":"p1031","attributes":{"syncable":false,"line_color":"black","line_alpha":1.0,"line_width":2,"line_dash":[4,4],"fill_color":"lightgrey","fill_alpha":0.5,"level":"overlay","visible":false,"left":{"type":"number","value":"nan"},"right":{"type":"number","value":"nan"},"top":{"type":"number","value":"nan"},"bottom":{"type":"number","value":"nan"},"left_units":"canvas","right_units":"canvas","top_units":"canvas","bottom_units":"canvas","handles":{"type":"object","name":"BoxInteractionHandles","id":"p1037","attributes":{"all":{"type":"object","name":"AreaVisuals","id":"p1036","attributes":{"fill_color":"white","hover_fill_color":"lightgray"}}}}}}}},{"type":"object","name":"SaveTool","id":"p1038"},{"type":"object","name":"ResetTool","id":"p1039"},{"type":"object","name":"HelpTool","id":"p1040"}]}},"left":[{"type":"object","name":"LinearAxis","id":"p1023","attributes":{"ticker":{"type":"object","name":"BasicTicker","id":"p1024","attributes":{"mantissas":[1,2,5]}},"formatter":{"type":"object","name":"BasicTickFormatter","id":"p1025"},"axis_label":"Value of the profile 1st derivative","major_label_policy":{"type":"object","name":"AllLabels","id":"p1026"}}}],"below":[{"type":"object","name":"LinearAxis","id":"p1018","attributes":{"ticker":{"type":"object","name":"BasicTicker","id":"p1019","attributes":{"mantissas":[1,2,5]}},"formatter":{"type":"object","name":"BasicTickFormatter","id":"p1020"},"axis_label":"$$ \\alpha $$","major_label_policy":{"type":"object","name":"AllLabels","id":"p1021"}}}],"center":[{"type":"object","name":"Grid","id":"p1022","attributes":{"axis":{"id":"p1018"}}},{"type":"object","name":"Grid","id":"p1027","attributes":{"dimension":1,"axis":{"id":"p1023"}}},{"type":"object","name":"Legend","id":"p1050","attributes":{"location":"bottom_right","title":"Legend","items":[{"type":"object","name":"LegendItem","id":"p1051","attributes":{"label":{"type":"value","value":"Profile first derivative solution for \u03b1 &gt;= 1"},"renderers":[{"id":"p1047"}]}},{"type":"object","name":"LegendItem","id":"p1061","attributes":{"label":{"type":"value","value":"Profile first derivative solution for \u03b1 &lt; 1"},"renderers":[{"id":"p1058"}]}},{"type":"object","name":"LegendItem","id":"p1071","attributes":{"label":{"type":"value","value":"Complex number solutions region for \u03b1 &gt;= 1"},"renderers":[{"id":"p1068"}]}}]}}]}},{"type":"object","name":"Row","id":"p1074","attributes":{"sizing_mode":"stretch_width","children":[{"type":"object","name":"Slider","id":"p1072","attributes":{"js_property_callbacks":{"type":"map","entries":[["change:value",[{"type":"object","name":"CustomJS","id":"p1073","attributes":{"args":{"type":"map","entries":[["source",{"id":"p1001"}],["alpha",{"id":"p1072"}]]},"code":"\n    const B = alpha.value\n\n    const x = source.data.x\n    const y2 = Array.from(x, (x) =&gt; -2*x*B*(1-B**2)**(x-1))\n    const y = source.data.y\n    source.data = { x, y, y2 }\n"}}]]]},"title":"Profile 2nd derivative root value","start":0.01,"end":1.0,"value":0.9,"step":0.001}}]}}]}}]}}
  </script>
  <script type="text/javascript">
    (function() {
      const fn = function() {
        Bokeh.safely(function() {
          (function(root) {
            function embed_document(root) {
            const docs_json = document.getElementById('e39cfdff-a985-4502-8ac6-7fabfc15f74c').textContent;
            const render_items = [{"docid":"251ccf3b-11cd-4d8c-9ce1-ee8ba19b059a","roots":{"p1075":"c2d8216c-5737-4b83-8212-83eb87b9cecf"},"root_ids":["p1075"]}];
            root.Bokeh.embed.embed_items(docs_json, render_items);
            }
            if (root.Bokeh !== undefined) {
              embed_document(root);
            } else {
              let attempts = 0;
              const timer = setInterval(function(root) {
                if (root.Bokeh !== undefined) {
                  clearInterval(timer);
                  embed_document(root);
                } else {
                  attempts++;
                  if (attempts > 100) {
                    clearInterval(timer);
                    console.log("Bokeh: ERROR: Unable to run BokehJS code because BokehJS library is missing");
                  }
                }
              }, 10, root)
            }
          })(window);
        });
      };
      if (document.readyState != "loading") fn();
      else document.addEventListener("DOMContentLoaded", fn);
    })();
  </script>
  </body>
</html>




We can now set the normalised gradient length:

$$
\mathtt{gradient\_length\_te} = -(\mathtt{dtdrho\_max})a \times \frac{\mathtt{rho\_te\_max}}{\mathtt{te\_max}}
$$

!!! note "Temperature & Density Case"

    Calculations are identical for the temperature and density case, just with the substitution of `ne` and `te` in the variable names

-------

#### H-mode profile | ipedestal == 1

##### `pedestal_parameterisation()`

The density and temperature profile runner function [`TProfile/NProfile.run()`](plasma_density_profile.md#runner-function-run) is firstly called to re-calculate the profile and core values. 

 Perform integrations to calculate ratio of density-weighted to volume-averaged temperature, etc. Density-weighted temperature = $\frac{\int{nT \ dV}}{\int{n \ dV}}$,  which is approximately equal to the ratio $\frac{\int{\rho \ n(\rho) T(\rho) \ d\rho}}{\int{\rho \ n(\rho) \ d\rho}}$

 Density weighted temperatures are thus set as such

$$
\mathtt{ten} = \frac{\int_0^1{\rho \ n(\rho) T(\rho) \  d\rho}}{\int_0^1{\rho \ n(\rho) \ d\rho}} \\
\mathtt{tin} = \mathtt{ten}\times \frac{T_\text{i}}{T_\text{e}}
$$

The above is done numerically with [scipy.integrate.simpson](https://docs.scipy.org/doc/scipy/reference/generated/scipy.integrate.simpson.html) integration.

Set profile factor which is the ratio of density-weighted to volume-averaged temperature

$$
\mathtt{pcoef} = \frac{\mathtt{ten}}{T_\text{e}}
$$

Calculate the line averaged electron density by integrating the normalised profile using the class [`integrate_profile_y()`](./plasma_profiles_abstract_class.md#calculate-the-profile-integral-value-integrate_profile_y) function

$$
\mathtt{dnla} = \int_0^1{n(\rho) \ d\rho}
$$

A divertor variable `prn1` is set to be equal to the separatrix density over the mean density:

$$
\mathtt{prn1} = \frac{n_{\text{e,sep}}}{n_{\text{e}}}
$$

--------

##### `calculate_profile_factors()`

The same function is run from the `ipedestal == 0 ` profile case, found [here](plasma_profiles.md#calculate_profile_factors)

-----

## Key Constraints

--------

### Setting pedestal values as fractions of the Greenwald limit 

By default, the values of $n_{\text{ped}}$ and $n_{\text{sep}}$ are set as fractions of the [Greenwald](https://wiki.fusion.ciemat.es/wiki/Greenwald_limit) limit such as: 

$$
n_{\text{ped}} = \mathtt{fgwped} \times \frac{I_p [\text{A}]}{\pi a^2 [\text{m}^2]} \times 10^{14}
$$

$$
n_{\text{sep}} = \mathtt{fgwsep} \times \frac{I_p [\text{A}]}{\pi a^2 [\text{m}^2]} \times 10^{14}
$$

To set the values of $n_{\text{ped}}$ and $n_{\text{sep}}$ directly, the user can input the value of $\mathtt{fgwped}$ or $\mathtt{fgwsep}$ to be less than 0.0 (i.e negative) to prevent the Greenwald fraction value being set.

$\mathtt{fgwped}$ and $\mathtt{fgwsep}$ can be set as iteration variables respectively by using `ixc = 45`
and `ixc = 152` respectively

------

### Pedestal Density Upper limit

This constraint can be activated by stating `icc = 81` in the input file

To prevent unrealistic profiles when iterating the values of `ne0, nesep, neped` etc, this constraint ensures that the value of `ne0` is always higher that `neped` to get a converged solution. This can be scaled with `fne0`

-------

### Eich Critical Separatrix Density Model

This constraint can be activated by stating `icc = 76` in the input file

Eich et.al has given a usable formula for the critical separatrix density in terms of fundamental plasma parameters[^4]. A function for the ballooning parameter at the separatrix $(\alpha_{\text{sep}}^{\text{crit}})$ is seen to increase about 
linearly with the separatrix density normalised to Greenwald density. This is seen in JET and ASDEX based on Thomson-scattering measurements, a clear correlation of the density limit of the tokamak H-mode high-confinement regime with 
the approach to the ideal ballooning instability threshold at the periphery of the plasma. 
 
$$
\alpha_{\text{sep}}^{\text{crit}} \approx \kappa^{1.2}(1+1.5\delta) \approx 2.5
$$

$$
\alpha_{\text{sep}}^{\text{crit}} = \kappa^{1.2}(1+1.5\delta)
$$

Where $\kappa$ is the plasma elongation and $\delta$ is the plasma triangularity.

$$
n_{\text{sep}}^{\text{crit}} = 5.9 \times \alpha_{\text{crit}} A^{-\frac{2}{7}}\left(\frac{1+\kappa^2}{2}\right)^{-\frac{6}{7}} P_{\text{sep}}^{-\frac{11}{70}} \times n_{\text{GW}}
$$

Where $A$ is the plasma aspect ratio, $P_{\text{sep}}$ is the power crossing the separatrix and is the heating power minus the radiated power in this case, $n_{\text{GW}}$ is the Greenwald density limit.

The value of `nesep` is check to make sure that it does not go higher than $n_{\text{sep}}^{\text{crit}}$. This can be scaled with `fnesep`


[^1]: M. Bernert et al. Plasma Phys. Control. Fus. **57** (2015) 014038
[^2]: N.A. Uckan and ITER Physics Group, 'ITER Physics Design Guidelines: 1989',
[^3]: Johner Jean (2011) HELIOS: A Zero-Dimensional Tool for Next Step and Reactor Studies, Fusion Science and Technology, 59:2, 308-349, DOI: 10.13182/FST11-A11650
[^4]: T. Eich et al 2018 Nucl. Fusion, 'Correlation of the tokamak H-mode density limit with ballooning stability at the separatrix' 58 034001