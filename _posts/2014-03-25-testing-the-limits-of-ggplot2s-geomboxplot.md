---
layout: post
title: "Testing the limits of ggplot2's geom_boxplot"
date: 2014-03-25 03:03:04 -0700
tags: ggplot2 r dataviz
---

Let me just start by saying this: if you're in the business of visualizing data, [ggplot2](http://ggplot2.org) is great. There's a bit of a learning curve, especially for those new to [R](http://www.r-project.org/) — but it's pretty redeeming when you finally master it and people actually **want** to look at your graphics. These days — partly for aesthetics and consistency, but also partly out of pure stubbornness — I try to create all of my plots with ggplot. This generally involves a lot of [documentation](http://docs.ggplot2.org/current/)-consulting and [stackoverflow](http://stackoverflow.com/questions/tagged/ggplot2)-searching, but luckily for me, the internet can usually provide at least one answer (if not ten) for most ggplot problems.

Recently, though, I ran up against an unexpected roadblock when attempting to make a fairly simple boxplot. Here's the idea: a continuous measure on the y-axis, boxes with a color fill defined by a two-level factor, and a numerical but discrete and non-linearly-increasing measure of time on the x-axis. Here's some simulated data to demonstrate:

<!--
{% highlight r %}
data.df = data.frame(time = rep(c(0,3,6,12,24,48),each=50),
    value = rnorm(300, 20, 5),
    measure = factor(rep(rep(c("A","B"),each=25), 6)))
{% endhighlight %}
-->

{% gist saraemoore/9766858 sim_boxplot_data.r %}

My first approach was pretty much straight out of the [geom_boxplot docs](http://docs.ggplot2.org/current/geom_boxplot.html):

<!--
{% highlight r %}
library(ggplot2)
ggplot(data.df, aes(x=factor(time), y=value)) +
    geom_boxplot(aes(fill=measure)) +
    xlab("Time (time units)") + ylab("Value (value units)") +
    scale_fill_discrete(name = "Measure")
{% endhighlight %}
-->

{% gist saraemoore/9766858 boxplot_attempt1.r %}

![First incorrect boxplot.](/figs/boxwrong1.png){:height="500px" width="600px"}

Of course, ggplot did exactly what I asked of it — it converted the "time" variable to a factor and placed each unique level on the plot without regard to how they were related numerically. OK, let's try this again. We need the x-axis variable to remain numerical.

<!--
{% highlight r %}
ggplot(data.df, aes(x=time, y=value)) +
    geom_boxplot(aes(fill=measure)) +
    xlab("Time (time units)") + ylab("Value (value units)") +
    scale_fill_discrete(name = "Measure")
{% endhighlight %}
-->

{% gist saraemoore/9766858 boxplot_attempt2.r %}

![Second incorrect boxplot.](/figs/boxwrong2.png){:height="500px" width="600px"}

Wrong again. Maybe ggplot can be tricked into re-grouping the boxes by the unique times on the x-axis?

<!--
{% highlight r %}
ggplot(data.df, aes(x=time, y=value)) +
    geom_boxplot(aes(fill=measure, group=factor(time))) +
    xlab("Time (time units)") + ylab("Value (value units)") +
    scale_fill_discrete(name = "Measure")
{% endhighlight %}
-->

{% gist saraemoore/9766858 boxplot_attempt3.r %}

![Third incorrect boxplot.](/figs/boxwrong3.png){:height="500px" width="600px"}

Apparently not. In a last ditch effort, I found [this SO post](http://stackoverflow.com/questions/10805643/ggplot2-add-color-to-boxplot-continuous-value-supplied-to-discrete-scale-er) which, while not answering my question directly, prompted [an interesting comment](http://stackoverflow.com/questions/10805643/ggplot2-add-color-to-boxplot-continuous-value-supplied-to-discrete-scale-er#comment14070382_10806683). Specifically, the commenter suggested adding a `position` aesthetic for a factor version of the x-axis variable. In terms of this running example, that would look like the following:

<!--
{% highlight r %}
ggplot(data.df, aes(y=value, x=time)) +
    geom_boxplot(aes(position = factor(time), fill=measure)) +
    xlab("Time (time units)") + ylab("Value (value units)") +
    scale_fill_discrete(name = "Measure")
{% endhighlight %}
-->

{% gist saraemoore/9766858 boxplot_correct1.r %}

What's interesting about this approach is that the way `position` is used here seems to be entirely undocumented. However, it works. Below is a slightly improved code snippet, along with the (correct!) boxplot it produces.

<!--
{% highlight r %}
ggplot(data.df, aes(y=value, x=time)) +
    geom_boxplot(aes(position = factor(time), fill=measure)) +
    scale_x_continuous(breaks = unique(data.df$time)) +
    xlab("Time (time units)") + ylab("Value (value units)") +
    scale_fill_discrete(name = "Measure")
{% endhighlight %}
-->

{% gist saraemoore/9766858 boxplot_correct2.r %}

![The correct boxplot](/figs/boxright.png){:height="500px" width="600px"}

(Note: this post utilizes ggplot2 v0.9.3.1.)