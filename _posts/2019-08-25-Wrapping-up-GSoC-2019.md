---
title: 'Wrapping up GSoC 2019'
date: 2019-08-22
permalink: /gsoc2019/2019/08/22/Wrapping-up-GSoC-2019/
tags:
 - GSoC2019
 - Ruby
 - SciRuby
 - Rubyplot
---

Summary: The final blog for GSoC 2019.  

**Table of Contents**
* TOC
{:toc}

# Introduction
With GSoC 2019 coming to an end, this is my final blog which mentions all my work for the project Rubyplot.  
  
# What is Rubyplot?
RubyPlot is a plotting library in Ruby for scientific development inspired by the library Matplotlib for Python. Users can create various types of plots like scatter plot, bar plot, etc. and can also create subplots which combine various of these plots. The long-term goal of the library is to build an efficient, scalable and user-friendly library with a backend-agnostic frontend to support various backends so that the library can be used on any device.  
  
## Examples
Creating graphs in Rubyplot is very simple and can be done in just a few lines of code, for example:
```ruby
require 'rubyplot'

figure = Rubyplot::Figure.new(width: 30, height: 30)

axes00 = figure.add_subplot! 0,0
axes00.plot! do |p|
  d = (0..360).step(5).to_a
  p.data d, d.map { |a| Math.sin(a * Math::PI / 180) }
  p.fmt = 'ok-'
  p.marker_fill_color = :white
  p.marker_size = 0.5
  p.line_width = 2
  p.label = "sine"
end

axes00.title = "A plot function example"
axes00.square_axes = false

figure.write('example1.png')
```
Has the output:  
![Example1](https://raw.githubusercontent.com/alishdipani/alishdipani.github.io/master/_posts/Resources/Wrapping_up_GSoC_2019/example1.png)
  
```ruby
require 'rubyplot'

figure = Rubyplot::Figure.new(width: 30, height: 30)

axes00 = figure.add_subplot! 0,0
axes00.candle_stick! do |p|
  p.lows = [0, 10, 20, 30, 20, 10] 
  p.highs = [40, 50, 60, 70, 60, 50] 
  p.opens = [10, 20, 30, 40, 30, 20]
  p.closes = [30, 40, 50, 60, 50, 40] 
  p.color = :yellow
end
axes00.candle_stick! do |p|
  p.lows = [5, 5, 25, 30, 10, 10]
  p.highs = [50, 40, 65, 70, 80, 60]
  p.opens = [10, 20, 30, 40, 30, 20] 
  p.closes = [35, 35, 45, 60, 75, 50]
  p.color = :blue
end

axes00.title = "A multi candle-stick plot"
axes00.square_axes = false

figure.write('example2.png')
```
Has the output:  
![Example2](https://raw.githubusercontent.com/alishdipani/alishdipani.github.io/master/_posts/Resources/Wrapping_up_GSoC_2019/example2.png)
  
```ruby
require 'rubyplot'

figure = Rubyplot::Figure.new(width: 30, height: 30)

axes00 = figure.add_subplot! 0,0
axes00.bar! do |p|
  p.data [1, 2, 3, 4, 5]
  p.color = :lemon
  p.spacing_ratio = 0.2
end
axes00.bar! do |p|
  p.data [5, 4, 3, 2, 1]
  p.color = :blue
end
axes00.bar! do |p|
  p.data [3, 5, 7, 5, 3]
  p.color = :red
end

axes00.title = "A multi bar plot"
axes00.square_axes = false

figure.write('example3.png')
```
Has the output:  
![Example3](https://raw.githubusercontent.com/alishdipani/alishdipani.github.io/master/_posts/Resources/Wrapping_up_GSoC_2019/example3.png)
  
  
# History of Rubyplot
Rubyplot started as two GSoC 2018 projects by Pranav Garg[(@pgtgrly)](https://github.com/pgtgrly) and Arafat Dad Khan[(@Arafatk)](https://github.com/Arafatk) and the mentors from The Ruby Science Foundation[(SciRuby)](http://sciruby.com/), Sameer Deshmukh[(@v0dro)](https://github.com/v0dro), John Woods[(@mohawkjohn)](https://github.com/mohawkjohn) and Pjotr Prins[(@pjotrp)](https://github.com/pjotrp). Pranav Garg worked on the GRRuby which had the GR backend and Arafat Dad Khan worked on Ruby Matplotlib which had the ImageMagick backend. The ultimate goal of combining both and creating Rubyplot. After GSoC 2018, Sameer Deshmukh combined both projects and created Rubyplot and he has maintained it ever since. Around May 2019, I started working on Rubyplot as a part of GSoC 2019.  
  
# GSoC 2019
As a part of GSoC 2019, my project had 3 major deliverables:
1. **ImageMagick support**(Phase 1): Support for ImageMagick back-end will be added in addition to the currently supported back-end GR, the front-end of the library will be back-end agnostic and the current overall integrity of the library will be preserved.  
2. **Plotting and show function**(Phase 2): A new plot function will be added which plots markers (for example circles) to form a scatter plot with the points as inputs (same as plot function in Matplotlib). A new function show will be added which will allow viewing of a plot without saving it. This plot function will be back-end agnostic and hence will support both GR and Magick back-end.  
3. **Integration with iruby notebooks**(Phase 3): Rubyplot will be integrated with iruby notebooks supporting all backends and allowing inline plotting.  
  
As a part of GSoC 2019, I completed all the deliverables I had initially planned along with a tutorial for the library and some other general improvements.  
Details of my work are as follows:  
  
## Phase 1
During Phase 1, I focused on setting up the ImageMagick backend which involved the basic functionality required for any backend of the library which are X-axis and Y-axis transform functions, `within_window` function which is responsible for placing the plots in the correct position, function for drawing the X and Y axis, functions for drawing the text and scaling the figure according to the dimensions given by the user. I implemented these functions using internal rmagick functions which were very useful like `scale`, `translate`, `rotate`, etc.  
After this, I worked on the scatter plot, which was the first plot I ever worked on. This plot had a very particular and interesting problem, which was that different types of markers were internally implemented in the GR backend, but for ImageMagick backend, I had to implement everything using basic shapes like circles, lines, polygons and rectangles. To solve this I created a hash of lambdas which had the code to create different types of markers using the basic shapes.  
After this I implemented all the simple plots which Rubyplot supports, these are line plot, area plot, bar plot, histogram, box plot, bubble plot, candle-stick plot and error-bar plot.  
  
So, during Phase 1, I completed the following deliverables -  
1. Set up the ImageMagick backend to have the basic functionality.  
2. Implemented and tested the simple plots in Rubyplot which are scatter plot, line plot, area plot, bar plot, histogram, box plot, bubble plot, candle-stick plot and error-bar plot.  
  
Code for Phase 1 can be found [here](https://github.com/SciRuby/rubyplot/pull/45/files/01a26777175c83e3e44a33d5ac5d6544b8ff9353..95f24730e04eb13460ed088dcf7569848b66a2ea).  
  
## Phase 2
I started Phase 2 by implementing the multi plots which are multi stack-bar plot, multi-bar plot, multi-box plot and multi candle-stick plot.  
Next, I implemented the `plot` function which is a combination of scatter plot and line plot, using the plot function the user can easily create a scatter plot or a line plot or a combination of both. The most interesting feature of the `plot` function is the `fmt` argument which sets the marker type, line type and the colour of the plot using just characters, so instead of writing the name of the type and setting the variables, the user can simply input a string in `fmt` argument which has the characters for corresponding marker type, line type and colour.  
Next was to implement the `show` function which is an alternative to `write` function. It draws the Figure and shows it on a temporary pop-up window without the need of saving the Figure on the device, this allows the user to test the code quickly and easily. This was done by using internal functions of the backends which are `display` for ImageMagick and `gr_updatews` for GR.  
  
So, during Phase 2, I completed the following deliverables -  
1. Implemented and tested the multi plots in Rubyplot which are multi stack-bar plot, multi-bar plot, multi-box plot and multi candle-stick plot.  
2. Implemented and tested the `plot` function with fmt argument.  
3. Implemented and tested the `show` function.  
  
Code for Phase 2 can be found [here](https://github.com/SciRuby/rubyplot/pull/45/files/36497cc3fdb6b9eddaee51777509d1f388a8ba4a..f918e9c9f390cfa96e453f3db610eeaa8f3acbed) and [here](https://github.com/SciRuby/rubyplot/pull/52/files/21d2450229ce8508f8a906914fd4bf47e1c62fa3..5947c911d290f34a09effca5ce3e866519b8c5db).
  
## Phase 3
During Phase 3, I integrated Rubyplot with the IRuby notebooks which allow the user to draw figures inside the notebook just by using the `show` function, through this integration the user can quickly and easily test the code step by step before running the whole codebase.  
I also implemented ticks for ImageMagick backend.  
Finally, I created a tutorial for the library which also contains template codes for all the plots which a user can easily get familiar with the working of the library and start using it.  
  
So, during Phase 3, I completed the following deliverables -  
1. Integrated Rubyplot with IRuby notebooks with the support for inline plotting.  
2. Implemented and tested ticks for Magick backend.  
3. Created the tutorial for Rubyplot.  
  
Code for Phase 3 can be found [here](https://github.com/SciRuby/rubyplot/pull/52/files/ca2aa7397581eaf92b64522361e3565f032e6250..3624962d8f96e3611c6bbc55a5c2c8aa1a299e27).
  
# Resources(blogs, code, etc.)
  
## Previous Work
- GSoC 2018 project GRRuby by Pranav Garg can be found [here](https://github.com/pgtgrly/GRruby-extension)
- GSoC 2018 project Ruby Matplotlib by Arafat Dad Khan can be found [here](https://github.com/Arafatk/magick-rubyplot)
- A talk on Rubyplot by Pranav Garg in RubyConf 2018 can be found [here](https://youtu.be/7QBkckZ1aNQ)
  
## My work
- Daily updates can be found [here](https://discourse.ruby-data.org/t/gsoc-2019-project-rubyplot-discussion/57)
- Proposal can be found [here](https://github.com/alishdipani/rubyplot/wiki/GSoC-2019-Proposal)
- Tutorial notebook can be found [here](https://github.com/alishdipani/rubyplot/blob/master/tutorial/magick/Rubyplot_Tutorial(Magick).ipynb) and can be viewed online(rendered) [here](https://nbviewer.jupyter.org/github/alishdipani/rubyplot/blob/master/tutorial/magick/Rubyplot_Tutorial%28Magick%29.ipynb)
- Rubyplot Github Repository can be found [here](https://github.com/SciRuby/rubyplot)
- All my work can be found in these PRs: [PR#45](https://github.com/SciRuby/rubyplot/pull/45) and [PR#52](https://github.com/SciRuby/rubyplot/pull/52)
- Other blogs can be found here:
  1. [GSoC 2019 project introduction](https://alishdipani.github.io/gsoc2019/2019/06/08/GSoC-2019-project-introduction/)
  2. [Rubyplot installation guide](https://alishdipani.github.io/gsoc2019/2019/06/09/Rubyplot-installation-guide/)
  3. [The Scatter plot example](https://alishdipani.github.io/gsoc2019/2019/06/10/The-Scatter-plot-example/)
  4. [Simple Plots in Rubyplot](https://alishdipani.github.io/gsoc2019/2019/06/28/Simple-Plots-in-Rubyplot/)
  5. [Multi plots in Rubyplot](https://alishdipani.github.io/gsoc2019/2019/07/13/Multi-plots-in-Rubyplot/)
  6. [The show and the plot functions](https://alishdipani.github.io/gsoc2019/2019/07/26/The-show-and-the-plot-functions/)
  7. [IRuby integration and ticks](https://alishdipani.github.io/gsoc2019/2019/08/22/IRuby-integration-and-ticks/)
  
# Future Work
I plan to keep contributing to Rubyplot and also start contributing to other projects of SciRuby.  
Future work to be done for Rubyplot is to write documentation, add more tests, add more types of plots, add more backends, make the plots interactive and in future add the feature for plotting 3-Dimensional graphs which would also be interactive.  
  
# EndNote
With this, we come to an end of GSoC 2019. These 3 months have been very challenging, interesting, exciting and fun. I got to learn a lot of things while working on Rubyplot and while interacting with my mentors. I have experienced an improvement in my Software development skills and programming in general which will help me a lot in future. I would love to keep working with SciRuby on more such interesting projects and maybe even try for GSoC again next year ;)  
  
# Acknowledgements
I would like to express my gratitude to my mentor Sameer Deshmukh for his guidance and support. He was always available and had solutions to every problem I faced, I got to learn a lot from him and I hope to learn a lot more from him in the future. I could not have asked for a better mentor.  
  
I would also like to thank Pranav Garg who introduced me to Ruby and also to the SciRuby community. During his GSoC 2018 project, he introduced me to the Rubyplot library and helped me get started with it. His suggestions were very helpful during my GSoC 2019 project.  
  
I would also like to thank mentors from SciRuby Prasun Anand and Shekhar Prasad Rajak for mentoring me and organising the occasional meetings and code reviews. I would also like to thank Udit Gulati for his helpful insights during the code reviews.
  
I am grateful to Google and the Ruby Science Foundation for this golden opportunity.
