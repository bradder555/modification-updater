# GraalVM Demos: Java Kotlin Interoperability and AOT Compilation Demo

This repository contains the code for a demo application for [GraalVM](graalvm.org).

## Prerequisites
* [GraalVM](http://graalvm.org)
* [Native Image](https://www.graalvm.org/docs/reference-manual/native-image/)
* Maven

## Preparation

This is a simple Java/Kotlin application, where a Java method accesses a String from Kotlin and calls a Kotlin function, which later accesses a String from a Java class.
This example demonstrates how easy it is to interop between Java and Kotlin.

1. [Download GraalVM](https://www.graalvm.org/downloads/), unzip the archive, export the GraalVM home directory as the `$JAVA_HOME` and add `$JAVA_HOME/bin` to the `PATH` environment variable.

  On Linux:
  ```bash
  export JAVA_HOME=/home/${current_user}/path/to/graalvm
  export PATH=$JAVA_HOME/bin:$PATH
  ```
  On macOS:
  ```bash
  export JAVA_HOME=/Users/${current_user}/path/to/graalvm/Contents/Home
  export PATH=$JAVA_HOME/bin:$PATH
  ```
  On Windows:
  ```bash
  setx /M JAVA_HOME "C:\Progra~1\Java\<graalvm>"
  setx /M PATH "C:\Progra~1\Java\<graalvm>\bin;%PATH%"
  ```

2. Install [Native Image](https://www.graalvm.org/docs/reference-manual/native-image/#install-native-image) by running:
  ```bash
  gu install native-image
  ```

3. Download or clone the repository and navigate into the `java-kotlin-aot` directory:
  ```bash
  git clone https://github.com/graalvm/graalvm-demos
  cd graalvm-demos/java-kotlin-aot
  ```

## Build the Application

Before running this example, you need to build the application:
```bash
./build.sh
```

Look at the important line of the `build.sh` which creates a native executable for the Java application.
The `native-image` builder compiles the application ahead-of-time (AOT) for faster startup and lower general overhead at runtime.

```bash
$JAVA_HOME/bin/native-image --no-fallback -cp ./target/mixed-code-hello-world-1.0-SNAPSHOT-jar-with-dependencies.jar -H:Name=helloworld -H:Class=hello.JavaHello -H:+ReportUnsupportedElementsAtRuntime
```

It takes a couple of parameters, the class path, the main class of the application with the `-H:Class=...`, and the name of the resulting executable with `-H:Name=...`.

After executing the `native-image` command, check the directory, it should have produced an executable file `helloworld`.

## Run the Application

To run the application, you need to execute the fat JAR file in the `target` directory.
You can run it as a normal Java application using `java`.
Or, since we have a native image prepared, you can run that directly.

The `run.sh` file executes it both ways and times them with the `time` utility.
```bash
time java -cp ./target/mixed-code-hello-world-1.0-SNAPSHOT-jar-with-dependencies.jar hello.JavaHello
time ./helloworld
```

The `run.sh` script produces approximately the following output:
```bash
→ ./run.sh
+ java -cp ./target/mixed-code-hello-world-1.0-SNAPSHOT-jar-with-dependencies.jar hello.JavaHello
Hello from Kotlin!
Hello from Java!

real	0m0.646s
user	0m0.167s
sys	0m0.135s
+ ./helloworld
Hello from Kotlin!
Hello from Java!

real	0m0.030s
user	0m0.005s
sys	0m0.008s
```

The performance gain of the native version is largely due to the faster startup.

## License

The sample application in this directory is taken from the JetBrains' [Kotlin-examples repository](https://github.com/JetBrains/kotlin-examples/tree/master/maven/mixed-code-hello-world).

The code from that repository is distributed under the Apache License 2.0.
The code in this directory is also distributed under the Apache License 2.0. See [LICENSE.md](LICENSE.md) for more details.
