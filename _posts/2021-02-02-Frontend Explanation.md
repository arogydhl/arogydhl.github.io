---
layout: post
title: Frontend Explanation
published: true
---

It's a bummer that I cannot show you live how the webapp works, I am trying to host this webapp on <a href='https://www.pythonanywhere.com' target='_blank'>Python Anywhere</a> and if it works I will update the blog. In the mean while  let me tell you how the dull looking frontend was made.
<div class = 'message'>
  I am embarrassed to even link an image here, I will link the complete project once it's finished. You can, however, download the repo from GitHub and
  check it out <a href='https://github.com/arogydhl/HandwrittenEquationSolverWebApp' target='_blank'> here </a>.
</div>
## The Canvas
The canvas is where we draw/write the mathematical operations; all the processes begin here. Honestly, this is the only part of the frontend, everything else on the screen are just helper functions. Let's start by defining the canvas, you can define a canvas with an id of `canvas` in html in this way:
{% highlight html %}
<div class="field">
    <canvas id="canvas"></canvas>
    <div class="tools">
        <button onclick="clear_canvas()" type="button" class="button">Clear</button>
    </div>
</div>
{% endhighlight %}
The `clear_canvas` function clears the canvas (are you surprised?). The html part of boring, so let's move on to the JavaScript part.
The entire JavaScript part is pretty long, so I will be talking about the key components only. Coding in JavaScript was hands down the trickiest part, all those semi-colons and commas. First I made this canvas drawable which was pretty smooth, there were great tutorials online on how to make a canvas element drawable and I was able to make my canvas do just that quickly; adding that `clear_canvas` was easy as well. There were some hiccups here and there but they were not major problems. The hardest part was making the canvas capture the image drawn on it and sending it to the backend.
<div class = 'message'>
Disclaimer: I am 90% sure this is the worst approach. In my defence, I couldn't find any help online and this was the best I could come up with.  
</div>
### JavaScript Code:
{% highlight js %}
function image_create() {
  var mouseX_min = Math.min.apply(Math, mouse_posX);
  var mouseX_max = Math.max.apply(Math, mouse_posX);
  var mouseY_min = Math.min.apply(Math, mouse_posY);
  var mouseY_max = Math.max.apply(Math, mouse_posY);
  var w = (mouseX_max - mouseX_min);
  var h = (mouseY_max - mouseY_min);
  var bx = Math.max(w,h)
  var newCanvas = document.createElement('canvas');
  newCanvas.width = bx;
  newCanvas.height = bx;
  var newContext = newCanvas.getContext('2d');
  newContext.drawImage(canvas, mouseX_min - 10, mouseY_min - 10, w + 20, h + 20, 0, 0, bx, bx);
  var img_last = newCanvas.toDataURL();
  mouse_posX = [];
  mouse_posY = [];
  return img_last;
}
{% endhighlight %}
The approach I took was to record the movement of the mouse on the canvas, which was not so complex as JavaScript is nice enough to grant us with `EventListeners`. So I created two empty arrays one for the X position of the mouse and another one for the Y position of the mouse (inside the canvas). These arrays are then sent to the main function i.e. the `image_create` function, this function takes the minimum values of X array and Y array and creates the square image starting from this (x - 10, y - 10) position (the position is relative to the canvas i.e. (0,0) is the top left corner of the canvas) of size bx, the maximum between the X position and Y position, and sends it to python backend. This process of creating the image was the hardest part. So what I did was, in the `image_create` function I created a second canvas on top of the original one which is of size <strong>bx</strong> (I will tell you why I needed to do this in a while). Then I created the clipping of the drawing of the canvas of size <strong>bx</strong> and placed it in the top left corner of the new canvas. Since both the canvas and the drawing are of the same size I can convert the new canvas directly into a base64 encoded image and send it via a POST request to the backend. This sounds pretty easy and it is pretty easy, but I had such a hard time finding this out I was about to scrap this project. So yeah this is basically the frontend, I don't think anyone will read this far but if you did then, Thank you for reading.