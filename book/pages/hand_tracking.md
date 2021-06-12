# Hand Tracking

Lots of solutions for hand tracking exist online. There are even pieces of hardware which will handle the software for you! However, I thought it would be a fun process to code up my own solution. So I found [Media Pipe] (https://google.github.io/mediapipe/), which is an open source solution for computer vision tasks. It didn’t take me too long to have my hands displayed on screen from my webcam. This is where my project experienced the most iterations, let me outline my experience working with hand tracking.

## Using Regions To Control Notes

This was my first approach for controlling notes, I just made a grid on screen and based on where your hand was the program would output a different value. This worked really well, and it was really accurate. Something about it didn’t feel great though; you couldn’t smoothly transition between notes which was important. 

## Using Gestures 

This was the most natural way to interact via hand tracking. However, I was slightly intimidated by this approach and had some hesitation before trying this out. The first thing I tried out was using my right hand to change notes. This followed a simple pattern: you just touch your index finger to your thumb, this could be C. Next if you want to play B you can touch the index middle finger and thumb. This works for each finger. My only issue was I could only play 4 notes. I thought about using the screen regions with your left hand as a modifier for notes you played. I ended up not really liking this, something about it didn’t feel right. The next idea I tried was using a waving gesture to change what notes you played with your right hand. After a lot of debugging, I was able to calculate the speed at which your hand moves, and using that I could determine what position of the scale you were in (how the left hand modified the right). This gesture felt so bad to play on, you had to constantly wave your hand back and forth at the exact same speed. And since speed was normalized it was extremely sensitive (little to no control). The other issues about calculating speed was when a hand went off-screen, detecting another hand, or randomly blowing up to a giant value. I realized through this process that designing a general gesture mechanism would be really difficult, I needed to focus on something that made sense to me. My solution not only worked out well, it also really emphasized that *Talking Hands* title. 

## Using Good Gestures 

My right hand gesture stayed the same as before, for example in C:

<code>
<p align="center">
 	Index touches thumb -> C
	Middle touches thumb -> B
	Ring touches thumb -> D
Pinky touches thumb -> E
</p>

But now I have separate positions for my left hand:

<p align="center">
	No fingers touch thumb -> Position L0
 	Index touches thumb -> Position L1
	Middle touches thumb -> Position L2
	Ring touches thumb -> Position L3
Pinky touches thumb -> Position L4
</p>

This allowed me to modify the right hand. Here’s an idea of how this works:
<p align="center">
 	L0 & Index touches thumb -> C
	L0 & Middle touches thumb -> B
	L0 & Ring touches thumb -> D
L0 & Pinky touches thumb -> E
	L1 & Index touches thumb -> F
	L1 & Middle touches thumb -> G
	L1 & Ring touches thumb -> C
L1 & Pinky touches thumb -> B
.
.
.
</p>
</code>

I decided that I would also make it so that if all fingers of the left hand were touching the thumb the key would shift. You can pre program progressions you want to play over, but by default you’ll cycle over the circle of 4ths. 

## Cleaning Things Up

After implementing gestures I fixed all the bugs that came with my gestures:

<ul>
	<li>Right vs Left: I split the screen in half so that if a hand is to the right of the middle it acts as the right hand, and if a hand is to the left it acts as the left hand. </li>
	<li>Sensitivity: Dealing with real world distances can be a pain. The current build of my hand tracking software works best when your hands are ~2 feet away from your camera. I enjoy the distances that I set and found them to not be too sensitive. But at first if you breathed wrong you would play 100 notes and switch keys rapidly</li>
</ul>

## Max Patch

My max patch is fairly basic, it reads in data from my hand tracking software using UDP, and will play notes and switch keys based on input from the script. Currently you can choose to play major scales, pentatonic scales, and the aeolian mode of major.
