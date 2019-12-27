# Tech Link
- Instructions for users to use app with the Spotlight Library.
- https://github.com/TakuSemba/Spotlight/blob/master/README.md
# Description
- With spotlight & target object be provided of Spotlight Library, we can overlay layout current with another layout, then highlights targets(view) to function guide user. 
- illustration: 
<img src="https://github.com/TakuSemba/Spotlight/blob/master/arts/customTarget.gif" align="left" width="30%">

- **Usage:** 
	First time, we must implement library in Gradle file:
		```kt
		dependencies {
		    implementation 'com.github.takusemba:spotlight:x.x.x'
		}
		``` 
	Next, we must create target object:
		```kt
		val target = Target.Builder()
		    .setAnchor(view)
		    .setShape(Circle(100f))
		    .setEffect(RippleEffect(100f, 200f, argb(30, 124, 255, 90)))
		    .setOverlay(layout)
		    .setOnTargetListener(object : OnTargetListener {
		      override fun onStarted() {
			makeText(this@MainActivity, "first target is started", LENGTH_SHORT).show()
		      }
		      override fun onEnded() {
			makeText(this@MainActivity, "first target is ended", LENGTH_SHORT).show()
		      }
		    }).build()
		```

	Next, we must create spotlight object: 
		```kt
		val spotlight = Spotlight.Builder(activity)
		    .setTargets(firstTarget, secondTarget, thirdTarget ...)
		    .setBackgroundColor(R.color.spotlightBackground)
		    .setDuration(1000L)
		    .setAnimation(DecelerateInterpolator(2f))
		    .setOnSpotlightListener(object : OnSpotlightListener {
		      override fun onStarted() {
			Toast.makeText(this@MainActivity, "spotlight is started", Toast.LENGTH_SHORT).show()
		      }
		      override fun onEnded() {
			Toast.makeText(this@MainActivity, "spotlight is ended", Toast.LENGTH_SHORT).show()
		      }
		    }).build() 
		```

	Start/Finish Spotlight: 
		```kt
		spotlight.start()
		spotlight.finish()
		```
	
	Next/Previous/Show Target: 
		```kt
		spotlight.next()
		spotlight.previous()
		spotlight.show(index of target)
		```
	Custom Shape to set Target Shape: 
		```kt
		class CustomShape(
		    override val duration: Long,
		    override val interpolator: TimeInterpolator
		) : Shape {

		  override fun draw(canvas: Canvas, point: PointF, value: Float, paint: Paint) {
		    // draw your shape here.
		  }
		}
		```

	Custom Effect to set Target Effect: 
		```kt
		class CustomEffect(
		    override val duration: Long,
		    override val interpolator: TimeInterpolator,
		    override val repeatMode: Int
		) : Effect {

		  override fun draw(canvas: Canvas, point: PointF, value: Float, paint: Paint) {
		    // draw your effect here.
		  }
		}
		```
  **Note** 
	1:	
		```kt
		Target.Builder().setAnchor(view)		
		```
	2: 
		```kt
		Target.Builder().setOverlay(layout)		
		```
	3:
		```kt
		Spotlight.Builder(activity)...
		.build()
		```
## Note 1:
 - Get position of view parameter will be hightlight and anchor with it position(where will be show make animation hightlight... ). 
## Note 2:  	  
 - Set overlay current layout by layout parameter. 
 - Layout parameter is guide layout and show guide text. We can handle next, previous.. targets when click in this layout and we can reuse for multiple screen. 
## Note 3: 
 - After this code run, although spotlight has not been started but current layout(activity layout) will be overlay by background color has been set background color of target. So we should build spotlight at the same time when we want to start spotlight. 
# Tech hashtags
- **#spotlight**
# Sample: 
- https://github.com/TakuSemba/Spotlight/tree/master/app
