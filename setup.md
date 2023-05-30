# Setting up an addon development environment

## Prerequisites

* A Fabric mod development environment: see [here](https://fabricmc.net/wiki/tutorial:setup) for instructions.

## Gradle Setup

1. PSWG is not yet on Maven Central, so you must depend on the jarfile published each alpha as a GitHub release. This is accomplished with the following changes to your `build.gradle`.

```diff
diff --git a/build.gradle b/build.gradle
index 7a3be5d..87cdab6 100644
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
diff --git a/gradle.properties b/gradle.properties
index d9f5bbe..9bb5f59 100644
--- a/gradle.properties
+++ b/gradle.properties
@@ -15,3 +15,4 @@ org.gradle.parallel=true

 # Dependencies
        fabric_version=0.82.0+1.20.0
+       pswg_version=0.0.108+1.20.0

```

3. Reload your Gradle project. You're now ready to add new content!

## Next Steps

* Coming soon: Adding a new lightsaber
* Coming soon: Adding a new blaster
* Coming soon: Adding a new species
* Coming soon: Adding a new set of armor