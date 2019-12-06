# Tech Link
Instruct for user use SensorManager of android to handle while the phone motion , 
https://code.tutsplus.com/vi/tutorials/android-from-scratch-hardware-sensors--cms-26985
https://code.tutsplus.com/vi/tutorials/android-sensors-in-depth-proximity-and-gyroscope--cms-28084
# Description
With Sensor . we can detect the motion , acceleration , ... and more
the type of sensor 
TYPE_AMBIENT_TEMPERATURE 
TYPE_DEVICE_PRIVATE_BASE
TYPE_GAME_ROTATION_VECTOR 
TYPE_GEOMAGNETIC_ROTATION_VECTOR 
TYPE_GRAVITY
TYPE_GYROSCOPE
TYPE_GYROSCOPE_UNCALIBRATED 
TYPE_HEART_BEAT 
TYPE_HEART_RATE 
TYPE_LIGHT
TYPE_LINEAR_ACCELERATION 
TYPE_MAGNETIC_FIELD
TYPE_MAGNETIC_FIELD_UNCALIBRATED 
TYPE_MOTION_DETECT
# note

class MainActivity : AppCompatActivity(), SensorEventListener {
    override fun onAccuracyChanged(p0: Sensor?, p1: Int) {
        //TODO("not implemented") //To change body of created functions use File | Settings | File Templates.
    }

    override fun onSensorChanged(p0: SensorEvent?) {
        p0?.let { getAccelerometer(it) }
    }

    private lateinit var sensorManager: SensorManager
    private var isColor = false
    private var lastUpdate: Long = 0
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        initView()
    }

    private fun initView() {
        sensorManager = getSystemService(Context.SENSOR_SERVICE) as SensorManager
        lastUpdate = System.currentTimeMillis()
    }

    private fun getAccelerometer(event: SensorEvent) {
        val values = event.values
        // Movement
        val x = values[0]
        val y = values[1]
        val z = values[2]

        val accelationSquareRoot =
            (x * x + y * y + z * z) / (SensorManager.GRAVITY_EARTH * SensorManager.GRAVITY_EARTH)

        val actualTime = System.currentTimeMillis()
        Toast.makeText(
            applicationContext, accelationSquareRoot.toString() + " " +
                    SensorManager.GRAVITY_EARTH, Toast.LENGTH_SHORT
        ).show()

        if (accelationSquareRoot >= 2)
        //it will be executed if you shuffle
        {

            if (actualTime - lastUpdate < 200) {
                return
            }
            lastUpdate = actualTime//updating lastUpdate for next shuffle
            if (isColor) {
                show_change_sensor.setBackgroundColor(Color.GREEN)

            } else {
                show_change_sensor.setBackgroundColor(Color.RED)
            }
            isColor = !isColor
        }
    }

    override fun onResume() {
        super.onResume()
        sensorManager.registerListener(this,sensorManager.getDefaultSensor(Sensor.TYPE_ACCELEROMETER),SensorManager.SENSOR_DELAY_NORMAL)
    }

    override fun onPause() {
        super.onPause()
        sensorManager.unregisterListener(this);
    }
}
# Tech hashtags
- **#sensor**

