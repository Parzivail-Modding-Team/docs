# Setting up an addon development environment

## Prerequisites

* A Fabric mod development environment: see [here](https://fabricmc.net/wiki/tutorial:setup) for instructions.

## Gradle Setup

1. PSWG is not yet on Maven Central, so you must depend on the jarfile published each alpha as a GitHub release. This is accomplished with the following changes to your `build.gradle`.

```diff
--- a/build.gradle
+++ b/build.gradle
@@ -16,6 +16,18 @@ repositories {
        // Loom adds the essential maven repositories to download Minecraft and libraries from automatically.
        // See https://docs.gradle.org/current/userguide/declaring_repositories.html
        // for more information about repositories.
+    ivy {
+        patternLayout {
+            artifact "releases/download/[revision]/[artifact]-[revision](-[classifier])(.[ext])"
+        }
+        metadataSources {
+            artifact()
+        }
+        content {
+            includeGroup "com.parzivail"
+        }
+        url = "https://github.com/Parzivail-Modding-Team/GalaxiesParzisStarWarsMod"
+    }
 }
 
 dependencies {
@@ -31,6 +43,7 @@ dependencies {
        // These are included in the Fabric API production distribution and allow you to update your mod to the latest modules at a later more convenient time.
 
        // modImplementation "net.fabricmc.fabric-api:fabric-api-deprecated:${project.fabric_version}"
+    modImplementation "com.parzivail:pswg:${project.pswg_version}"
 }
 
 processResources {
```

2. Then, specify the minimum version of PSWG you wish to depend on in your `gradle.properties`.

```diff
--- a/gradle.properties
+++ b/gradle.properties
@@ -15,3 +15,4 @@ org.gradle.parallel=true

 # Dependencies
        fabric_version=0.82.0+1.20.0
+       pswg_version=0.0.108+1.20.0

```

3. Then, add the dependency on PSWG in your Fabric Mod JSON file.

```diff
--- a/src/main/resources/fabric.mod.json
+++ b/src/main/resources/fabric.mod.json
@@ -30,6 +30,7 @@
     "fabricloader": ">=0.14.19",
     "fabric-api": "*",
     "minecraft": "~1.20.0",
+    "pswg": ">=0.0.108",
     "java": ">=17"
   },
   "suggests": {

```

<details>
<summary>Q&A: Why can't I use the value from the Gradle properties file?</summary>

At runtime, if you're not building and running with Gradle (building and running with IntelliJ is recommended), these values in the Fabric Mod JSON are not populated with the values present in `gradle.properties`, and the version at runtime would be the literal string `${pswg_version}` instead of interpolating with the correct value, e.g. `0.0.108+1.20.0`. Hopefully future developments will prevent this redundancy, but unfortunately, specifying the version number in both locations is best practice for now.

</details>

4. Finally, reload your Gradle project. You're now ready to add new content!

## Next Steps

* Coming soon: Adding a new lightsaber
* Coming soon: Adding a new blaster
* Coming soon: Adding a new species
* Coming soon: Adding a new set of armor