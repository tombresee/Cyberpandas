


Applying Cyberpandas
=============================
Many aspects of a chart's appearance can be configured at the top level using
the ``configure_*()`` methods.
These methods and the properties that they set are only valid at the top level
of a chart, and can be thought of as a way of setting a chart theme: that is,
they set the default styles for the entire chart, and these defaults can be
overridden by specific style settings associated with chart elements.

These methods and their arguments will be outlined below:




Axis Configuration
------------------
Axis configuration defines default settings for axes, and can be set using
the :meth:`Chart.configure_axis` method.
Properties defined here are applied to all axes in the figure.

Additional property blocks can target more specific axis types based on the
orientation ("axisX", "axisY", "axisLeft", "axisTop", etc.) or band scale
type ("axisBand").
For example, properties defined under the "axisBand"
property will only apply to axes visualizing "band" scales.
If multiple axis config blocks apply to a single axis, type-based options
take precedence over orientation-based options, which in turn take precedence
over general options.




Header Configuration
--------------------
The :meth:`Chart.configure_header` method allows configuration of facet headers,
including the font, color, size, and position of the title and labels.
Here is an example:


    import altair as alt
    from vega_datasets import data

    source = data.cars.url

    chart = alt.Chart(source).mark_point().encode(
        x='Horsepower:Q',
        y='Miles_per_Gallon:Q',
        color='Origin:N',
        column='Origin:N'
    ).properties(
        width=180,
        height=180
    )

    chart.configure_header(
        titleColor='green',
        titleFontSize=14,
        labelColor='red',
        labelFontSize=14
    )

.. altair-object-table:: altair.HeaderConfig





Legend Configuration
--------------------
The :meth:`Chart.configure_legend` allows you to customize the appearance of chart
legends, including location, fonts, bounding boxes, colors, and more.
Here is an example:




Mark and Mark Style Configuration
---------------------------------
The mark configuration can be set using the :meth:`Chart.configure_mark`
method, which sets the default properties for all marks in the chart.
In addition, the config object also provides mark-specific configuration
using the mark type (e.g. :meth:`Chart.configure_area`) for
defining default properties for each mark.

For general configuration of all mark types, use:



In addition to the default mark properties above, default values can be
further customized using named styles defined as keyword arguments to
the :meth:`Chart.configure_style` method.
Styles can then be invoked by including a style property within a mark
definition object.




View Configuration
------------------
The :meth:`Chart.configure_view` method allows you to configure aspects of the
chart's *view*, i.e. the area of the screen in which the data and scales are
drawn. Here is an example to demonstrate some of the visual features that can
be controlled:

    import altair as alt
    from vega_datasets import data

    source = data.cars.url

    chart = alt.Chart(source).mark_point().encode(
        x='Horsepower:Q',
        y='Miles_per_Gallon:Q',
    )

    chart.configure_view(
        continuousHeight=200,
        continuousWidth=200,
        strokeWidth=4,
        fill='#FFEEDD',
        stroke='red',
    )





Altair Themes
-------------
Altair makes available a theme registry that lets users apply chart configurations
globally within any Python session. This is done via the ``alt.themes`` object.

The themes registry consists of functions which define a specification dictionary
that will be added to every created chart.
For example, the default theme configures the default size of a single chart:

    >>> import altair as alt
    >>> default = alt.themes.get()
    >>> default()
    {'config': {'view': {'continuousWidth': 400, 'continuousHeight': 300}}}

You can see that any chart you create will have this theme applied, and these configurations
added to its specification:


    import altair as alt
    from vega_datasets import data

    chart = alt.Chart(data.cars.url).mark_point().encode(
        x='Horsepower:Q',
        y='Miles_per_Gallon:Q'
    )

    chart.to_dict()






Changing the Theme
~~~~~~~~~~~~~~~~~~
If you would like to enable any other theme for the length of your Python session,
you can call ``alt.themes.enable(theme_name)``.
For example, Altair includes a theme in which the chart background is opaque
rather than transparent:


Notice that the background color of the chart is now set to white.
If you would like no theme applied to your chart, you can use the
theme named ``'none'``:

Because the view configuration is not set, the chart is smaller
than the default rendering.

If you would like to use any theme just for a single chart, you can use the
``with`` statement to enable a temporary theme:


Currently Altair does not offer many built-in themes, but we plan to add
more options in the future.






Defining a Custom Theme
~~~~~~~~~~~~~~~~~~~~~~~
The theme registry also allows defining and registering custom themes.
A theme is simply a function that returns a dictionary of default values
to be added to the chart specification at rendering time, which is then
registered and activated.

For example, here we define a theme in which all marks are drawn with black
fill unless otherwise specified:

altair-plot::

    import altair as alt
    from vega_datasets import data

    # define the theme by returning the dictionary of configurations
    def black_marks():
        return {
            'config': {
                'view': {
                    'height': 300,
                    'width': 400,
                },
                'mark': {
                    'color': 'black',
                    'fill': 'black'
                }
            }
        }

    # register the custom theme under a chosen name
    alt.themes.register('black_marks', black_marks)

    # enable the newly registered theme
    alt.themes.enable('black_marks')

    # draw the chart
    cars = data.cars.url
    alt.Chart(cars).mark_point().encode(
        x='Horsepower:Q',
        y='Miles_per_Gallon:Q'
    )


If you want to restore the default theme, use::

   alt.themes.enable('default')


For more ideas on themes, see the `Vega Themes`_ repository.


.. _Vega Themes: https://github.com/vega/vega-themes/