# Minestom LWJGL Example

This repository demonstrates how to setup Minestom with LWJGL.

This can be used to take advantage of OpenGL rendering for maps for instance.

## How to use LWJGL with Minestom
(everything in this section can also be found in code in `build.gradle` and `gradle.properties`)

Start by adding Jitpack & Mojang libraries as artifact repositories in your Gradle build script:

```groovy
repositories {
    // ...
    maven { url 'https://libraries.minecraft.net' }
    maven { url 'https://jitpack.io' } // to access packages from GitHub
}
```

As you depend on LWJGL, you will need to add it to your dependencies:

```groovy
// If you depend on LWJGL, you need to provide the version and OS
// Check Minestom's build.gradle file if you need to check which version is currently used in case you come across conflicts
project.ext.lwjglVersion = "3.2.3"

switch (org.gradle.internal.os.OperatingSystem.current()) {
    case org.gradle.internal.os.OperatingSystem.LINUX:
        def osArch = System.getProperty("os.arch")
        project.ext.lwjglNatives = osArch.startsWith("arm") || osArch.startsWith("aarch64")
                ? "natives-linux-${osArch.contains("64") || osArch.startsWith("armv8") ? "arm64" : "arm32"}"
                : "natives-linux"
        break
    case org.gradle.internal.os.OperatingSystem.MAC_OS:
        project.ext.lwjglNatives = "natives-macos"
        break
    case org.gradle.internal.os.OperatingSystem.WINDOWS:
        project.ext.lwjglNatives = System.getProperty("os.arch").contains("64") ? "natives-windows" : "natives-windows-x86"
        break
}

dependencies {
    implementation platform("org.lwjgl:lwjgl-bom:$lwjglVersion")
    // natives for LWJGL
    runtimeOnly "org.lwjgl:lwjgl::$lwjglNatives"
    runtimeOnly "org.lwjgl:lwjgl-opengl::$lwjglNatives"
    runtimeOnly "org.lwjgl:lwjgl-opengles::$lwjglNatives"
    runtimeOnly "org.lwjgl:lwjgl-glfw::$lwjglNatives"

    // ...
}
```

Then, add Minestom and Minestom LWJGL-related code as dependencies:
```groovy
dependencies {
    // ...

    implementation "com.github.Minestom:Minestom:${project.minestom_version}" // declare Minestom as dependency
    implementation "com.github.Minestom:Minestom:${project.minestom_version}:lwjgl" // declare Minestom LWJGL code as dependency

    // declare use of Minestom LWJGL-related code (parentheses are required here for the build script to be parsed correctly)
    implementation("com.github.Minestom:Minestom:${project.minestom_version}") {
        capabilities {
            requireCapability("net.minestom.server:Minestom-lwjgl")
        }
    }
}
```

You're good to go!