# gradle-config
##android studio gradle config.
### 1.versionCode
#### 1.1 build.gradle(Project)
add gradle property **appVersionCode** versionCode in module:app is manifest+1.
```
// add properties appVersionCode
ext.appVersionCode = initVersionCode()
import java.util.regex.Pattern
def initVersionCode(){
    def manifestFile = file("jancartui2/src/main/AndroidManifest.xml")
    def manifestText = manifestFile.getText()
    def matcher = Pattern.compile("versionCode=\"(\\d+)\"").matcher(manifestText)
    def versionCode = -2;
    try{
        matcher.find()
        versionCode = Integer.parseInt(matcher.group(1))+1
    }catch (Exception e){e.printStackTrace()}
    println(" --------->  current versionCode : "+versionCode)
    if (versionCode==-1)
        throw Exception("can't get versionCode");
    return versionCode
}
```
#### 1.2 build.gradle(Module:app)
```

android {
    defaultConfig {
        applicationId "......"
        minSdkVersion 18
        targetSdkVersion 23
        versionName="1.0.0"
        versionCode appVersionCode
    }
}
dependencies {
    ......
    }
//versionnumberautoincrement
import java.util.regex.Pattern

task('increaseManifestVersionCode') << {
    def manifestFile = file("src/main/AndroidManifest.xml")
    def manifestText = manifestFile.getText()
    def matcher = Pattern.compile("versionCode=\"(\\d+)\"").matcher(manifestText)
    matcher.find()
    def versionCode = Integer.parseInt(matcher.group(1))
    def manifestContent = matcher.replaceAll("versionCode=\"" + ++versionCode + "\"")
    manifestFile.write(manifestContent)
}

tasks.whenTaskAdded { task ->
    if (task.name == 'generateReleaseBuildConfig') {
        task.dependsOn 'increaseManifestVersionCode'
    }
}
```
### 2. init properties
#### 2.1 local.properties
add properties
```
#Wed Jun 29 22:33:01 CST 2016
sdk.dir=C\:\\Users\\stan\\AppData\\Local\\Android\\Sdk
app.api=api-23-5.0
app.author = stan
```
#### 2.2 rootDir/setting.gradle
```
include ':app'
def initSettingsEnv(){
    Properties properties = new Properties()
    String path = rootDir.getAbsolutePath()+"/local.properties"
    File propertiesFile = new File(path)
    properties.load(propertiesFile.newDataInputStream())
    gradle.ext.api = properties.getProperty("app.api")
    gradle.ext.author = properties.getProperty("app.author")
}

initSettingsEnv()
```
and then we can get the property with gradle.api/gradle.author
