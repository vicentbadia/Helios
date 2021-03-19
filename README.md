<h1>Neurobehavioral classifier module</h1>
<p>A set of algorithms implemented as an extension module to analyse the <b>emotional statements</b> of the users with two main aims:</p>
<ul>
    <li>Determine an <b>emotional score</b> between an ego and an alter, giving this info to feed the <b>trust model</b>.</li>
    <li>Analyse the content shared creating metrics to give feedback on how the users response to a specific stimulus.</li>
</ul>
<h3>Module with the following functionalities:</h3>
<ul>
    <li>Interface to communicate with Neurobehavioral classifier module</li>
    <li>Metrics of accelerometer sensor for each event in chat activity</li>
    <li>Sentiment analysis of each image sent or received through Helios chat</li>
	<li>Sentiment analysis of Helios chat texts</li>
	<li>SQLite database to store on the device results of sentiment analysis</li>
    <li>egoAlterTrust method to get results of sentiment analysis of an Ego - Alter relationship through NeurobehaviourListener</li>
</ul>
<p><b>HELIOS Neuro-behavioral classifier module</b> is one of the HELIOS Module APIs as highlighted in the picture below:</p>
<img src="https://raw.githubusercontent.com/helios-h2020/h.extension-NeuroBehaviouralClassifier/master/doc/Esquema-Neurobehavioural.png" alt="HELIOS Neuro-behavioral classifier module" />

<h2>Cloning repository</h2>
<p>git clone https://github.com/helios-h2020/h.extension-NeuroBehaviouralClassifier.git </p>

<h2> </h2>
<h2>Neurobehaviour module</h2>
<p>Current version of Neurobehaviour module works with TestClient app (app + modules) with the purpose of integrate callings to Neurobehaviour module when the user interacts with Helios chat.</p>

<p> <br>
<img src="https://raw.githubusercontent.com/helios-h2020/h.extension-NeuroBehaviouralClassifier/master/doc/module01.png" alt="HELIOS app integration" />
</p>
<br>
<p>Please follow the next steps to integrate Neurobehaviour module into your app.</p>

<h2> </h2>
<h2>Integration of the module in the app</h2>
<p>Open your app in Android Studio.</p>
<p><b>File > New > Import Module</b></p>
<p>Go to <i>neurobehaviouralclassifier</i> folder. Select <b>neurobehaviour</b> folder like source directory. Click <b>Finish</b> to import.</p>
<p>New module should appears in first line of <b>settings.gradle</b> file:</p>
<code>include ':app', ':storage', ':messaging', ':profile', ':security', ':context', <b>':neurobehaviour'</b></code>
<p> <br>Open <b>build.gradle</b> file of app module. In <b>dependencies</b> section add:</p>
<code>implementation project(":neurobehaviour")</code>

<p> <br>
<img src="https://raw.githubusercontent.com/helios-h2020/h.extension-NeuroBehaviouralClassifier/master/doc/Neurobehaviour-Classes.png" alt="HELIOS Neuro-behavioral classifier module" />
</p>

<p>In order to test all features of Neurobehavioral module, you will need to modify some files in TestClient app folder. Please follow <a href="doc/testclient-files.md" title="testclient app files">these steps</a>.</p>

<h2> </h2>
<h2>Integration of the module using Nexus Helios repository</h2>

<p>Add the repository in build.gradle file of project:</p>

```java
allprojects {
    repositories {
        google()
        jcenter()
        maven {
            url "https://builder.helios-social.eu/repository/helios-repository/"
            credentials {
                username = heliosUser
                password = heliosPassword
            }
        }

    }
}
```

<p>Add dependency to the build.gradle file of the app:</p>

```java
dependencies {
	implementation 'eu.h2020.helios_social.modules.neurobehaviour:neurobehaviour:1.1.9'
}
```

<h2> </h2>
<h2>Creation of the AAR library</h2>

<p>It's possible to create an AAR file to use the module like a library for any project.</p>

<p>The <b>build.gradle</b> file of Neurobehaviour module includes a line to set the build configuration:</p>

```java
abiFilters "arm64-v8a", "x86"
```

<p>This setting allows to generate a build for two architectures, PC (Android emulator) and Android mobile.</p>

<p>In order to limit size of AAR file, we can use only <b>"arm64-v8a"</b> value for a production environment. In other hand, we can use <b>"x86"</b> value to test AAR library with Android Studio emulator.</p>

<h2> </h2>
<h1>Neurobehaviour module inputs</h1>

<p><b>Helios app should activate Neurobehavioral module with this events and its correspondent calling:</b></p>
    
<p>We will use the same calling for all type of message (text / audio / image)</p>

<ul>
	<li>App starts -> <b>startAccel</b> function to start accelerometer measurement</li>
    <li>App onDestroy method -> <b>stopAccel</b> function</li>
</ul><ul>
    <li>Settings -> Nick name changed -> <b>createCsv</b> method to create files for analysis results</li>
</ul><ul>
	<li>User sends a message -> <b>sendingMsg</b> function to start sentimental analysis of message</li>
</ul><ul>
    <li>Updating Ego - Alter Trust value -> calling to <b>egoAlterTrust</b> function</li>
</ul>

<h2> </h2>
<h1>Neurobehaviour module outputs</h1>

<h3>Ego - Alter sentimental analysis</h3>

<p><b>Neurobehaviour module > Trust Manager</b></p>

<p>Neurobehaviour module extracts metrics from user behaviour:</p>

<ul>
	<li>Arousal: positive / negative</li>
	<li>Valence: positive / negative</li>
	<li>Attention: low / medium / high</li>
</ul>

<p>Calling to <b>egoAlterTrust</b> function</p>
<p>When Trust module make the call, Neurobehaviour module sends this metrics to calculate the <b>new Trust value</b>.</p>

<h3>Sensors analysis</h3>

<p>After user has sent or read a message, Neurobehaviour module sends sensor metrics (acceleration average) using <b>SensorValueListener</b> interface (Context module)</p>

<p><b>DataHandler.java</b></p>

> //Using Context module interface to send acceleration value
> import eu.h2020.helios_social.core.sensor.ext.DeviceSensor;
> 
> obj.msgId = msgId;
> obj.accelAverage = averageAccel;
> deviceSensor.receiveValue(obj);



<h2> </h2>
<h1>Using the module</h1>

<h3>AndroidManifest permissions</h3>

<p>In order to use sensors of Android device, we have to add permissions in <b>AndroidManifest.xml</b> file of the app:</p>

```xml
    <!-- UPV LAB - Using camera to send photos -->
    <uses-permission android:name="android.permission.CAMERA" />
    <uses-permission android:name="android.permission.FLASHLIGHT" />
    <!-- Only for Google Play: -->
    <uses-feature android:name="android.hardware.camera" android:required="false" />
    <uses-feature android:name="android.hardware.camera.flash" android:required="false" />
    <!-- UPV LAB - Using audio recording to send voice messages -->
    <uses-permission android:name="android.permission.RECORD_AUDIO" />

    <!-- UPV LAB · Provider to manage files · Used by the camera -->
    <provider
            android:name="androidx.core.content.FileProvider"
            android:authorities="eu.h2020.helios_social.fileprovider"
            android:exported="false"
            android:grantUriPermissions="true">
            <meta-data
                android:name="android.support.FILE_PROVIDER_PATHS"
                android:resource="@xml/file_paths" />
    </provider>
```

<h3>Calls to Neurobehaviour module methods</h3>

<p>How to call module functions from other modules through methods of the listener:</p>

<ul>
	<li><a href="doc/contents-analysis.md" title="Contents analysis">Contents analysis: Text, image and speech analysis</a></li>
	<li><a href="doc/ego-alter-analysis.md" title="Ego - Alter analysis">Ego - Alter analysis</a></li>
</ul>


<h2> </h2>
<h1>Module structure - Java classes</h1>

<p>Module view in Android Studio</p>

<p style="margin: 20px; margin-left: 40px; margin-bottom: 40px; valingn: top">
	<img src="https://raw.githubusercontent.com/helios-h2020/h.extension-NeuroBehaviouralClassifier/master/doc/module01.png" alt="HELIOS Neuro-behavioral classifier module" style="margin-left: 20px" />
</p>
<p style="margin: 20px; margin-left: 140px; margin-bottom: 40px; valingn: top">
	<img src="https://raw.githubusercontent.com/helios-h2020/h.extension-NeuroBehaviouralClassifier/master/doc/Neurobehaviour-Classes02.png" alt="HELIOS Neuro-behavioral classifier module" style="margin-left: 20px" />
</p>

<ul>
	<li><a href="doc/classes-interface.md" title="Neurobehaviour interface class">Neurobehaviour Interface Class</a></li>
	<li><a href="doc/classes-listener.md" title="Neurobehaviour listener class">Neurobehaviour module listener Class</a></li>
    <li><a href="doc/classes-datahandler.md" title="Datahandler Class">DataHandler Class</a></li>
    <li><a href="doc/classes-acceleration.md" title="Acceleration class">Acceleration Class</a></li>
    <li><a href="doc/classes-average.md" title="Acceleration average class">Acceleration average Class</a></li>
    <li><a href="doc/classes-imageanalysis.md" title="Image analysis class">Sentiment analysis Class</a></li>
</ul>

<h2></h2>
<h2>Sentiment analysis class</h2>

<h3>Image analysis</h3>

<p>This class performs a sentimental analysis of each image sent by user using Helios chat. Using deep learning models, this module extracts:</p>

<ul>
    <li>Number of faces</li>
    <li>Sentiment of each face: happy, fear, anger...</li>
    <li>Score of each face sentimental analysis</li>
</ul>

<p>Results are saved in a text file and are drawn over the image:</p>

<p><img src="https://raw.githubusercontent.com/helios-h2020/h.extension-NeuroBehaviouralClassifier/master/doc/image-analysis.jpg" alt="image analysis" /></p>

<h3>Text analysis</h3>

<p>This class performs too a sentimental analysis of each text sent by user in Helios chat. Using deep learning models, this function extracts:</p>

<ul>
	<li>Text translation from any language to english using Google API</li>
	<li>Tags for each word of text</li>
	<li>Result of sentiment analysis of text. Positive / negative and score</li>
</ul>

```java
public class SentimentalAnalysis extends AppCompatActivity {
   public void runThread(final Context context, final String fileName, final HeliosMessageListener messageListener, final HeliosTopic topic, final HeliosMessage message)
   public int realImageWidth(String resource)
   private String getExtension(String word)
   private void textAnalysis (Context context, String message, HeliosMessageListener messageListener, HeliosTopic topic)
   private void pythonAnalysis (Context context, String script, String picture, Float scale, ImageView imageView, HeliosMessageListener messageListener, HeliosTopic topic)
   private void paintingImage(PyObject squares, PyObject emotions, PyObject scores, String imageResource, Float scale, ImageView imageView, Context context, HeliosMessageListener messageListener, HeliosTopic topic)
   public String getDate(long time)
   private void saveImageData (Context context, int numFaces, String emotionsData, String scoreData)
   private void saveTextData (Context context, PyObject origText, PyObject engText, PyObject tags, PyObject emotions) 
   private String extractList(List<PyObject> list)
   private void saveAudioData (Context context, String audioData)
}
```


<h2> </h2>
<h2>Neurobehaviour module storage</h2>

<p>The module implements a local storage system to save the sentiment analysis of each message sent or received through Helios chat. This storage system is implemented using a <b>SQLite database</b> and <b>Room</b> like a data object interface.</p>

<p>Design of Neurobehavioral module database:</p>

<img src="https://raw.githubusercontent.com/helios-h2020/h.extension-NeuroBehaviouralClassifier/master/doc/NeurbDatabase.png" alt="Android SQLite database">

<p>SQLite + Room database implementation</p>

<img src="https://raw.githubusercontent.com/helios-h2020/h.extension-NeuroBehaviouralClassifier/master/doc/RoomDatabase.PNG" alt="Room database implementation">

    
<h2> </h2>
<h2>Testing the module</h2>

<p>Unit tests carried out using <b>JUnit4 framework</b> testing library</p>

<p>Instrumented unit tests for Neurobehaviour module in:</p>

<img src="https://raw.githubusercontent.com/helios-h2020/h.extension-NeuroBehaviouralClassifier/master/doc/AndroidTesting-Path.png" alt="Android testing path">

<p> </p>
<p>Info about Android instrumented unit tests:</p>
<a href="https://developer.android.com/training/testing/unit-testing/instrumented-unit-tests" target="_blank" title="Andriod instrumented unit tests">https://developer.android.com/training/testing/unit-testing/instrumented-unit-tests</a>

<h3>Results of conducted tests</h3>

<p>Results of unit tests for NeurobehaviourListener class:</p>

<img src="https://raw.githubusercontent.com/helios-h2020/h.extension-NeuroBehaviouralClassifier/master/doc/AndroidTesting.png" alt="Results for NeurobehaviourListener class">

<p> </p>
<p>Results of unit tests for Acceleration class:</p>

<img src="https://raw.githubusercontent.com/helios-h2020/h.extension-NeuroBehaviouralClassifier/master/doc/AndroidTesting-Acceleration.png" alt="Results for Acceleration class">
    
<h2> </h2>
<h2>Running the module</h2>

<p><b>Logcat tags to debug the module</b></p>
<ul>
	<li>v/listen > show info about listener callbacks</li>
	<li>v/thread > acceleration measurement values (real time linear acceleration and average)</li>
    <li>v/cv > functions for Computer Vision and results of image sentimental analysis</li>
	<li>v/text > results of text sentimental analysis</li>
	<li>v/audio > logs of sentimental analysis of audio files</li>
    <li>v/storage > it shows info about module storage system operations</li>
    <li>v/lab > functions for sessions of Neurobehavioural module validation</li>
</ul>


