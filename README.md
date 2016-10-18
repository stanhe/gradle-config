# gradle-config
android studio gradle config.
#### 1.build.gradle(Project)
添加gradle属性 **appVersionCode**
```
ext.appVersionCode = initVersionCode()

import java.util.regex.Pattern

def initVersionCode(){
    def manifestFile = file("app/src/main/AndroidManifest.xml")
    def manifestText = manifestFile.getText()
    def matcher = Pattern.compile("versionCode=\"(\\d+)\"").matcher(manifestText)
    def versionCode = 0;
    try{
        matcher.find()
        versionCode = Integer.parseInt(matcher.group(1))
    }catch (Exception e){e.printStackTrace()}
    println(" --------->  current versionCode : "+versionCode)
    if (versionCode==0)
        throw Exception("can't get versionCode");
    return versionCode
}
```
#### 2.build.gradle(Module:app)
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
